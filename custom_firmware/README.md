For more info, check:

https://github.com/tqz/defogger-8600

This is firmware for the D-Link DCS-8600LH *only*

It is version 1.00.10 but with lighttpd enabled.

Hints:
  # cp -pr /etc/lighttpd /tmp/SDCard/lighttpd

  * in lighttpd.conf, define port since default ports are not available:
      server.port = 

  * disable or change the authentication parts of the default conf.

  # killall mips-linux-lighttpd ; mips-linux-lighttpd -f /tmp/SDCard/lighttpd.conf -m /usr/lib/lighttpd


