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

## Obligatory chat server ##


