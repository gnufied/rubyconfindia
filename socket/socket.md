!SLIDE left smaller

## Socket classes in Ruby ##

    @@@ c
    rb_cBasicSocket = rb_define_class("BasicSocket", rb_cIO);
    cIPSocket = rb_define_class("IPSocket", rb_cBasicSocket);
    rb_define_global_const("IPsocket", rb_cIPSocket);

    rb_cTCPSocket = rb_define_class("TCPSocket", rb_cIPSocket);
    rb_define_global_const("TCPsocket", rb_cTCPSocket);

    rb_cSOCKSSocket = rb_define_class("SOCKSSocket", rb_cTCPSocket);
    rb_define_global_const("SOCKSsocket", rb_cSOCKSSocket);

    rb_cTCPServer = rb_define_class("TCPServer", rb_cTCPSocket);
    rb_define_global_const("TCPserver", rb_cTCPServer);

    rb_cUDPSocket = rb_define_class("UDPSocket", rb_cIPSocket);
    rb_define_global_const("UDPsocket", rb_cUDPSocket);


    rb_cUNIXSocket = rb_define_class("UNIXSocket", rb_cBasicSocket);
    rb_define_global_const("UNIXsocket", rb_cUNIXSocket);

    rb_cUNIXServer = rb_define_class("UNIXServer", rb_cUNIXSocket);
    rb_define_global_const("UNIXserver", rb_cUNIXServer);
    rb_cSocket = rb_define_class("Socket", rb_cBasicSocket);

!SLIDE left smaller

## Obligatory Echo server ##

    @@@ ruby
    # echo_server.rb
    require "socket"

    server = TCPServer.new("127.0.0.1",3600)
    Signal.trap("INT") { server.close(); exit; }
    while(socket = server.accept()) 
      data = socket.gets()
      data ? socket.puts(data) : socket.close()
    end
    server.close()
    
    # echo_client.rb
    require "socket"
    socket = TCPSocket.open("localhost",3600)
    socket.puts("Hello World")
    puts socket.gets()
    socket.close()
    
!SLIDE left smaller

## Ain't everything right in Server's kingdom ##

    @@@ ruby

    # Potentially dangerous and simple code
    require "socket"
    server = TCPServer.new("127.0.0.1",3600)
    Signal.trap("INT") { server.close(); exit; }
    client_threads = []
    while(socket = server.accept()) 
      client_threads << Thread.new(socket) do |client_socket| # be careful when simply globbing from scope.
        loop do 
          data = client_socket.gets()
          data ? client_socket.puts(data) : client_socket.close()
        end
      end
    end
    server.close()

!SLIDE left smaller bullets incremental

## On what we just wrote ##

* Was using blocking IO. 
* Had to use threads if more than one concurrent client has to be handled.
* There was a protocol, albiet simple one. Line based.
* Unbounded number of threads is never a good idea.Thread pools.

!SLIDE left smaller bullets

## In MRI 1.8, all blocking calls are turned into non-blocking calls internally.##

    @@@ c
        fd = fileno(fptr->f);
        str = rb_tainted_str_new(0, buflen);
    retry:
        rb_str_locktmp(str);
        rb_thread_wait_fd(fd); // will call rb_thread_schedule
        TRAP_BEG;
        slen = recvfrom(fd, RSTRING(str)->ptr, buflen, flags, (struct sockaddr*)buf, &alen);
        TRAP_END;
        rb_str_unlocktmp(str);
	
### And it doesn't work on windows ###
    

!SLIDE left bullets incremental
 
## And in MRI 1.9 ##

* A blocking call is actually blocking call. The thread which makes the blocking call releases the GVL.
* Did I say GVL?
* Works more reliably on Windows.

!SLIDE

## Behind the scene code for MRI 1.9 ##

    @@@ c
    // socket.c
    #define BLOCKING_REGION(func, arg) 
       (long)rb_thread_blocking_region((func),(arg), RUBY_UBF_IO, 0)
       
    while (rb_io_check_closed(fptr),
	   rb_thread_wait_fd(arg.fd),
	   (slen = BLOCKING_REGION(recvfrom_blocking, &arg)) < 0) {
	   if (RBASIC(str)->klass || RSTRING_LEN(str) != buflen) {
	      rb_raise(rb_eRuntimeError, "buffer string modified");
	   }
    }
    // thread.c
    //
    VALUE rb_thread_blocking_region(rb_blocking_function_t *func, \
         void *data1,rb_unblock_function_t *ubf, void *data2)

!SLIDE bullets incremental

## State of parallelism in Ruby ##

* MRI 1.8 has green threads.
* MRI 1.9 has native threads, but has GVL.
* JRuby has native threads.
* All is not lost.

!SLIDE bullets

## You can use process level parallelism ##

* @CRRI we reduced our build times using process level parallelism.
* UNIX Sockets.
* Packet/BackgrounDRb.
* Unicorn application server.




  








