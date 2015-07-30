Warning : These instructions are meant to be used as a hack in order to use ardupilot on the Bebop.
Parrot cannot be held responsible if anything happens to your bebop after using the following instructions.
Nevertheless, an official Parrot firmware is provided with the appropriate procedure to go back to normal mode.

In order to install arducopter on the bebop, you need :

- A USB cable with the USB PIN ID tied to the 1 logical level or a usb cable with the red button (pushing the red button ties the PIN ID to 1). If you hack a regular USB cable into tying the PIN ID to 1, you need to use another cable for other USB communications with the bebop.
- The tarball bebop_install.tar.bz2
- (optional) a 3.3V UART cable. example :http://www.ftdichip.com/Products/Cables/USBTTLSerial.htm and a male connector adapter https://www.pololu.com/product/965 on which you can cut 3 pins.
then plug it in the bebop UART connector and on the 3 pins starting at the black wire on the uart cable.The black wire needs to positioned at the front of the bebop.


# Instructions: 

## Phase 1
- Detar the tarball
- Copy pinst_usb and pflasher binaries to /usr/local/bin and add this directory to your PATH variable if not done
- Plug the usb cable with the pin ID to 1 (or press the red button)
- Start the bebop (then you can release the red button).
- type "sudo pinst_usb mykonos3_linux_installer.tar.gz"
- wait for flashing to end (if you have a uart cable plugged, then you can see what is going on)
- When the flashing is done ("payload flashing OK"), the bebop stops
- You can then restart it.

**Now your bebop is able to run ardupilot**

## Phase 2
- Get adb (android debugger)
- create $HOME/.android/adb_usb.ini file and just write 0x19cf in it. This will add the vendor id of the bebop (0x19cf) to the list of supported devices. For more information see adb documentation
- plug a regular usb cable and type "adb shell". 
- If a prompt is displayed, you now have access to the bebop console
- Remount the / partition as writeable "mount -o remount,rw /" (needed to push to the bebop in the / partition.)

## Phase 3
- Get the armhf toolchain in order to compile ardupilot
- Get the latest version of ardupilot
- "cd ArduCopter"
- "make bebop"
- push Arducopter.elf file to the target : "adb push/tmp/ArduCopter.build/ArduCopter.elf /usr/bin/arducopter"

## Phase 4
- push gps_firmware.bin and gps_firmware.bin.cfg to /etc/ ("adb push gps_firmware.bin /etc; adb push gps_firmware.bin.cfg /etc")
- connect via adb shell.
- launch libgps_cli on the target.
- connect via another adb shell (other terminal).
- type "echo U > /tmp/gps_debug"
- You can watch in the other terminal how things are going
- When the update is over, replace /etc/gps_config.txt by the gps_config file from the GPS directory in the tarball.
- then restart the gps by typing "echo 0 > /sys/devices/platform/user_gpio/GPS_POWER_EN/value; echo 1 > /sys/devices/platform/user_gpio/GPS_POWER_EN/value"

## Phase 5
- From a computer, connect to the Bebop's wifi network (dragonfly-mykonos3).
- Check the computer's IP address, it should start with 192.168.42.
- on the bebop console (adb shell), launch arducopter : "arducopter -A your_ip_address:14550 -C udp:your_ip_address:14551 -B /dev/ttyPA1 -E /dev/null -l /data/ftp/internal_000/APM/logs -t /data/ftp/internal_000/APM/terrain"
- Connect with the host software of your choice and fly !

## Recovery Procedure
 **In case you want to go back to the regular Bebop software:**
- type "sudo pinst_usb official_bebop.tar.gz", it will reflash an official firmware that will allow you to update to the last available one".


