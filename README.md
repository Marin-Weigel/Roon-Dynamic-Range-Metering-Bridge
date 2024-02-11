# Roon-Metering-Bridge
**Instructions on how to set up a Raspberry Pi as a groupable RAAT endpoint to display peak-hold spectrum, stereo correlation and VU bars, as well as a EBU-128 conform dynamic range dislay.**

***CAVEAT:***
*I've set-up and tested this on a RPi3B+ with Raspbian GNU/Linux 11 (bullseye), so it'll need adaptions for any other model/OS-version to work as described.
This guide also assumes basic knowledge on how to navigate and use RPi desktop menus, accessories like "Add/Remove Software", terminal window and the editor "Nano" to create and adapt some config files.*

...

Download and flash RaspberryPi OS desktop 32-bit with either Raspberry Pi Imager or Balena Etcher, and read relevant instructions for first set-up to finally boot to the RPi desktop environment.

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

***content=`cat $DIR`***

***if [[ "$content" != 'closed' ]]; then***

***xset s reset -dpms***

***elif [[ "$content" == 'closed' ]]; then***

***xset s blank dpms 1 1 0***

***fi***

***sleep 1***

***done***

Save and exit the file.

...

Navigate the desktop menu to "Preferences" and click "Add/Remove Software".

To find and then **install the Jack sound server user interface** in the repository search field, enter:

***qjackctl***

When found, check the box and install via "Apply".

To **install the meters**, consecutively type and "Apply" following entries in the repository search field:

***jkmeter***

***ebumeter***

***japa***

When done, exit the software repository.

...



**I'm in the process of still editing this, so be patient...**
