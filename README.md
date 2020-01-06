![D-Link DCS-8600LH](https://eu.dlink.com/uk/en/-/media/product-pages/dcs/8600lh/dcs_8600lh_left.png?h=1085&la=en-GB&w=550)
This is an attempt to get the DCS-8600LH defogged. Any help is deeply appreciated.

First of all: THANKS bmork! I would not have got started without your defogger for the 8000.

The script is totally based on bmork's original dcs8000lh-configure.py, which got me hopeful that the 8600 would be just as "easy". Since then I've changed by mind on the easy part.

Things learned so far, in no order:

* My laptop refused to change MTU on the BTLE packets, so nothing got sent in it's entirety, so I did all the BTLE communication with the camera from a Raspberry Pi 3 (Raspbian stretch/9.6, Bluez 5.43)

Firmware v1.00.10:

* Start telnetd: 
```
  $ dcs8600.py 00:11:22:33:44:55 012345 --telnetd
  This is only active until the next reboot.
or
  Create a file ".tw_enable_telnet" at the root of the SD-card and reboot the camera.
  This also makes it persistent.
or
  $ dcs8600.py 00:11:22:33:44:55 012345 --command "/bin/echo>/tmp/SDCard/.tw_enable_telnet"
  $ dcs8600.py 00:11:22:33:44:55 012345 --command "reboot"
```

* telnet login: root:twipc
  
* The key binaries are Rtk_MainProc, strmsrv, da_adaptor, cda, sa, StreamProxy all of none has released source code.
  * Rtk_MainProc listens on ports 80, 554, 443
  * da_adaptor listens on ports 8080, 8081
  * strmsrv listens on port 8088
  * StreamProxy listens on port 7000

* root filesystem is read-only
 
* remounting directories is possible, which makes testing changes easier and faster
```
    e.g # mount /etc/ /tmp/SDCard/new-etc/  
```

* http://CAMERA-IP/common/info.cgi will output camera information without requiring a password.

* As with the 8000LH, you can easily backup the partitions via tftp on the 8600LH as well:
```
  # for i in 0 1 2 3 4 5 6 7 8 9; do tftp -l /dev/mtd${i}ro -r mtd$i -p ${TFTP_SERVER}; done
or
  # ls /dev/mtd*ro|cut -c9|while read i; do tftp -l /dev/mtd${i}ro -r mtd$i -p ${TFTP_SERVER}; done
```
* All Bluetooth communication is logged in /tmp/blue.log

* Disable internet communication on an already fogged camera (attached to the cloud), only allowing local traffic:
```
  $ dcs8600.py 00:11:22:33:44:55 012345 --command "route del -net default"
or via telnet:
  # route del -net default
```
* If telnet login is rejected, get the current passwd:
```
  $ dcs8600.py 00:11:22:33:44:55 012345 --command "tftp -l /etc/passwd -r passwd -p ${TFTP_SERVER}
```
* There are traces of lighttpd configuration in /etc but there is no binary

* credentials tonywu:123qwe in httpd.conf is not used anywhere as far as I know

* I have not been able to access the open services on ports 80, 443, 554, 7000, 8080, 8081, 8088 since user and password still eludes me

* If you mess up the admin_passwd during testing, fix it via telnet:
```
  # mdb set admin_passwd "012345"
```
* I am just above clueless about Python
