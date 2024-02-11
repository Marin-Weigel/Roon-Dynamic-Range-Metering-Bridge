# Roon-Metering-Bridge
**Instructions on how to set up a Raspberry Pi as a groupable RAAT endpoint to display peak-hold spectrum, stereo correlation and VU bars, as well as a EBU-128 conform dynamic range dislay.**

***CAVEAT:***
*I've set this up and tested it on a RPi3B+ with Raspbian GNU/Linux 11 (bullseye), so it'll likely need adaptions here and there to work for any other model/RPi-OS version.
This guide also assumes basic knowledge on how to navigate and use RPi desktop menus, accessories like "Add/Remove Software", terminal window and the editor "Nano" to create and edit some config files.*

...

Download and flash RaspberryPi OS desktop 32-bit with either Raspberry Pi Imager or Balena Etcher, and read relevant instructions for first set-up to finally boot to the RPi desktop environment.

Make sure to set your wired/wireless network to be on the same subnet as your Roon server and remotes, and don't keep both connected at the same time to avoid networking issues.

...

Attach a keyboard and mouse to the RPi.

Navigate the desktop menu to "Accessories" and open a "Terminal" window.

...

In the terminal window, enter each of the following lines, followed by return to **install Roon's bridge software**:

***curl -O https://download.roonlabs.net/builds/roonbridge-installer-linuxarmv7hf.sh***

***chmod +x roonbridge-installer-linuxarmv7hf.sh***

***sudo ./roonbridge-installer-linuxarmv7hf.sh***

...

Still in the terminal window, enter the following to **set up a loopback sound device**:

***sudo nano /etc/rc.local***

In Nano editor add the line:

***sudo modprobe snd-aloop***

between the lines

***fi***

and 

***exit 0***

Save and exit the file.

...


While still in the terminal window, enter the following to **configure the loopback sound device**:

***sudo nano /home/pi/.jackdrc***

In Nano editor add the line:

***/usr/bin/jackd -dalsa -r44100 -p4096 -n2 -S -D -Chw:Loopback -Phw:Loopback***

Save and exit the file.

...

While still in the terminal window, we need to **set up a script to detect active Roon streaming status to turn the RPi touch screen on and off**, so enter:

***sudo nano /home/pi/output-monitor.sh***

In Nano editor add lines:

***#!/bin/bash***

***DIR='/proc/asound/Loopback/pcm1p/sub0/status'***

***while :***

***do***

***content='cat $DIR'***

***if [[ "$content" != 'closed' ]]; then***

***xset s reset -dpms***

***elif [[ "$content" == 'closed' ]]; then***

***xset s blank dpms 1 1 0***

***fi***

***sleep 1***

***done***

Save and exit the file.

...

While still in the terminal window, enter the following to **turn the RPi PCB status LEDs off**:

***sudo nano /boot/cofig.txt***

In Nano editor add lines:

***#Turn off Power LED***

***dtparam=pwr_led_trigger=default-on***

***dtparam=pwr_led_activelow=off***

***#Turn off Activity LED***

***dtparam=act_led_trigger=none***

***dtparam=act_led_activelow=off***

Save and exit the file.

...

While still in the terminal window, enter the following if you want to **change the RPi touch screen backlight brightness**:

***sudo nano /sys/class/backlight/10-0045/brightness***

In Nano editor, change the shown value from between 0 to 255 to adjust the brightness to your liking, then save and exit the file.

...

Navigate the desktop menu to "Preferences" and click "Add/Remove Software".

To find and then **install the Jack sound server user interface and the meters**, consecutively type and "Apply" following entries in the repository search field:

***qjackctl***

***jkmeter***

***ebumeter***

***japa***

When done, exit the software repository.

...

**Now it's time to reboot the Raspberry Pi to activate the changes**:

Navigate the desktop menu to "Logout", then click "Reboot".

...

When the RPi is back up and running, check Roon's "Settings", "Audio", find the RPi with its loopback devices and activate the second one of those. Roon should now have added a new zone for it, which you could group with any other RAAT zone.

navigate the desktop menu to "Accessories", open a "Terminal" window and enter:



...

In the terminal window, enter each of the following lines, followed by return to set up your metering screen:



**I'm in the process of still editing this, so be patient...**
