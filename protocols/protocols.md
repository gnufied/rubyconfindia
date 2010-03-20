!SLIDE center

## Network Protocols ##

![Protocols](protocols_english.gif)

!SLIDE bullets incremental

## Designing better protocols ##

* Don't create binary protocols, unless speed is becoming really an issue.
* Keep it simple and text based, but don't use newline or carriage return as delimiter.
* You can use JSON as your network protocol.
* Version your protocols, keep version information as part of handshake process.

!SLIDE smbullets incremental

## Using JSON as network Protocol ##

* JSON can be easily parsed in evented manner. Each message can be uniquely identified when it ends, without message length being prefixed with message.
* Excellent parses in almost every language out there.
* Essentially key/value and hence easier to be backward compatible.
* My last attempt to sell JSON as network protocol failed.

!SLIDE smaller


    @@@ ruby
    def post_init
      @parser = Yajl::Parser.new
    end
    
    def object_parsed(obj)
      puts obj.inspect
    end
    
    def connection_completed
      # once a full JSON object has been parsed from the stream
      # object_parsed will be called, and passed the constructed object
      @parser.on_parse_complete = method(:object_parsed)
    end
    
    def receive_data(data)
      # continue passing chunks
      @parser << data
    end
    
    






