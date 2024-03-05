# Roon-Metering-Bridge for RPi5 (8GB)
**Instructions on how to set up a Raspberry Pi 5 as a groupable RAAT endpoint to display peak-hold spectrum, stereo correlation and VU bars, as well as a dynamic range dislay conforming to EBU-128.**

***CAVEAT:***
*I've set this up and tested it on a RPi5 8GB with bookworm 64bit, so it'll likely need adaptions here and there to work for any other model/RPi-OS version.
This guide also assumes basic knowledge on how to navigate and use RPi desktop menus, accessories like "Add/Remove Software", terminal window and the editor "Nano" to create and edit some config files.*

...

Install raspios-bookworm-arm64 with Raspberry Pi Imager.

...

Attach a keyboard and mouse to the RPi while setting things up.

Navigate the desktop menu to "Accessories" and open a "Terminal" window.

In the terminal window, enter each of the following lines, followed by return to install Roon's bridge software:

Install Roon bridge

***$ curl -O https://download.roonlabs.net/builds/roonbridge-installer-linuxarmv8.sh***

***$ chmod +x roonbridge-installer-linuxarmv8.sh***

***$ sudo ./roonbridge-installer-linuxarmv8.sh***

...

Still in the terminal window, enter the following to set up a loopback sound device:

***sudo nano /etc/rc.local***

In Nano editor, add the line:

***sudo modprobe snd-aloop***

between the lines

***fi***

and

***exit 0***

Save and exit the file.

...


Still in the terminal window, enter the following to set up jack audio:

***sudo apt-get install jackd***

...

Still in the terminal window, enter the following to configure the loopback sound device:

***sudo nano /home/pi/.jackdrc***

In Nano editor, add the line:

***/usr/bin/jackd -dalsa -r44100 -p4096 -n2 -S -D -Chw:Loopback -Phw:Loopback***

Save and exit the file.

...

While still in the terminal window, enter the following to set up a script to detect active Roon streaming status in order to automatically turn the RPi touch screen on and off:

***sudo nano /home/pi/output-monitor.sh***

In Nano editor, add lines:

***#!/bin/bash***

***DIR='/proc/asound/Loopback/pcm1p/sub0/status'***

***while :***

***do***

***content=`cat $DIR`***

***if [[ "$content" == 'closed' ]]; then***

***echo 0 > /sys/class/backlight/4-0045/brightness***

***elif [[ "$content" != 'closed' ]]; then***

***echo 127 > /sys/class/backlight/4-0045/brightness***

***fi***

***sleep 1***

***done***

Save and exit the file.

...

While still in the terminal window, enter the following to turn the RPi PCB status LEDs off:

***sudo nano /boot/firmware/config.txt***

In Nano editor, add lines:

***#Turn off Power LED***

***dtparam=pwr_led_trigger=default-on***

***dtparam=pwr_led_activelow=off***

***dtparam=act_led_trigger=default-on***

***dtparam=act_led_activelow=off***

***#Turn off Activity LED***

***dtparam=eth_led0=4***

***dtparam=eth_led1=4***

Save and exit the file.

...

While still in the terminal window, enter the following if you want to change the RPi touch screen backlight brightness:

***sudo nano /sys/class/backlight/4-0045/brightness***

In Nano editor, change the shown value from between ***0*** to ***255*** to adjust the brightness, then save and exit the file.

...

While still in the terminal window, consecutively enter the following lines to install the Jack sound server user interface and the meters:

***sudo apt-get install qjackctl***

***sudo apt-get install jkmeter***

***sudo apt-get install ebumeter***

***sudo apt-get install japa***

...

Now it's time to reboot the Raspberry Pi to activate the changes:

Navigate the desktop menu to "Logout", then click "Reboot".

...

When the RPi is back up and running, check Roon's "Settings", "Audio", find the RPi with its loopback devices and activate the second one of them (which works for me). Now, Roon should have added a new zone for it, which you can group with your music system's RAAT zone.

...

Navigate the desktop menu to "Accessories", open a "Terminal" window and enter:

***/usr/bin/jackd -dalsa -r44100 -p4096 -n2 -S -D -Chw:Loopback -Phw:Loopback &***

***qjackctl &***

Click "Setup" in the "Jack Audio Connection Kit" window.

![image](https://github.com/Marin-Weigel/Roon-Dynamic-Range-Metering-Bridge/assets/108012806/e4fba088-23cf-4bba-8936-7033b45b41ae)

Check, adjust and save if needed, "Settings", "Parameters" as in screenshot.

![image](https://github.com/Marin-Weigel/Roon-Dynamic-Range-Metering-Bridge/assets/108012806/86654131-4aa0-4823-b51c-02fd28b6f435)

Check, adjust and save if needed, "Settings", "Advanced" as in screenshot.

![image](https://github.com/Marin-Weigel/Roon-Dynamic-Range-Metering-Bridge/assets/108012806/da0ff576-7be0-44bc-b479-8253d16210ab)

Check, adjust and save if needed, "Misc" as in screenshot.

![image](https://github.com/Marin-Weigel/Roon-Dynamic-Range-Metering-Bridge/assets/108012806/2c84b2ec-a517-4ebd-9b06-602b7f5f1705)

...

Navigate the desktop menu to "Sound&Video" and start "Ebumeter", "Japa (with JACK support)" and "Jkmeter".

...

Click "Connect" in the "Jack Audio Connection Kit" window and set up the connections as shown:

![image](https://github.com/Marin-Weigel/Roon-Dynamic-Range-Metering-Bridge/assets/108012806/3529696f-1b7f-4d63-a9ec-8363551050ff)

...

If everything went well and the grouped zone with the RPi is currently streaming music, you should see some action already ...

...

"Japa" needs to be configured first to show a spectrogram, so set it up like in the screenshot, but feel free to adjust to your liking:

![image](https://github.com/Marin-Weigel/Roon-Dynamic-Range-Metering-Bridge/assets/108012806/d131f5c4-9df1-4842-845a-a1d6fb926756)

...

Now it's up to your imagination to move and resize the individual windows.

Here's my layout:

![image](https://github.com/Marin-Weigel/Roon-Dynamic-Range-Metering-Bridge/assets/108012806/99fdabd6-e02c-42f5-bee9-41ca5d7405db)

...

Finally, back in the terminal window, (likely after entering "ctrl c" to get a prompt) enter the following lines followed by return to activate automatic screen turn-on/turn-off:

***sudo su***

***./output-monitor.sh &***

The screen should turn off now if you "Stop" Roon's stream, and turn back on when starting it -  it will stay on when pausing, though.

...

**There might still be quirks, omissions and faults in the guide, as I'm working from memory and my sketchy notes, so feel free to PM me via Roon's forum - GOOD LUCK AND HAVE FUN!**
