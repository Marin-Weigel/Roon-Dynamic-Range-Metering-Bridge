# Roon-Metering-Bridge
**Instructions on how to set up a Raspberry Pi as a groupable RAAT endpoint to display peak-hold spectrum, stereo correlation and VU bars, as well as a dynamic range dislay conforming to EBU-128.**

***CAVEAT:***
*I've set this up and tested it on a RPi3B+ with Raspbian GNU/Linux 11 (bullseye), so it'll likely need adaptions here and there to work for any other model/RPi-OS version.
This guide also assumes basic knowledge on how to navigate and use RPi desktop menus, accessories like "Add/Remove Software", terminal window and the editor "Nano" to create and edit some config files.*

...

Download and flash RaspberryPi OS desktop 32-bit with either Raspberry Pi Imager or Balena Etcher, and read relevant instructions for first set-up to finally boot to the RPi desktop environment.

Make sure to set your wired/wireless network to be on the same subnet as your Roon server and remotes, and don't keep both connected at the same time to avoid networking issues.

...

Attach a keyboard and mouse to the RPi while setting things up.

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

While still in the terminal window, enter the following to **set up a script to detect active Roon streaming status in order to turn the RPi touch screen on and off**:

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

***ATTENTION: You might have a different loopback set up, so might need to adapt loopback parameters above!***

***Following test only works with Roon already being able to stream to the loopback device.***

***Here's my configuration***

![image](https://github.com/Marin-Weigel/Roon-Dynamic-Range-Metering-Bridge/assets/108012806/0898c72b-f06b-4102-9ece-26de35403cf3)

***And here's the terminal response for the streaming status for the correct loopback output***

![image](https://github.com/Marin-Weigel/Roon-Dynamic-Range-Metering-Bridge/assets/108012806/5cd23c96-8e06-4d13-b0cd-6db26bf2d1d6)

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

In Nano editor, change the shown value from between 0 to 255 to adjust the brightness, then save and exit the file.

***ATTENTION: You might have a different backlight set up, so might need to adapt parameters above!***

***Here's my configuration***

![image](https://github.com/Marin-Weigel/Roon-Dynamic-Range-Metering-Bridge/assets/108012806/2ac53215-3898-4376-9d0f-e529256c2065)

...

Navigate the desktop menu to "Preferences" and click "Add/Remove Software".

To find and then **install the Jack sound server user interface and the meters**, consecutively type following entries in the repository search field, wait for the result followed by clicking "Apply" and waiting for execution:

***qjackctl***

***jkmeter***

***ebumeter***

***japa***

When done, exit the software repository.

...

**Now it's time to reboot the Raspberry Pi to activate the changes**:

Navigate the desktop menu to "Logout", then click "Reboot".

...

When the RPi is back up and running, check Roon's "Settings", "Audio", find the RPi with its loopback devices and activate the second one of them. Roon should now have added a new zone for it, which you can now group with your music system's RAAT zone.

...

Navigate the desktop menu to "Accessories", open a "Terminal" window and enter:

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

"Japa" needs some preliminary configuring to show a spectrogram, so set it up like in the screenshot, but feel free to adjust to your liking:

![image](https://github.com/Marin-Weigel/Roon-Dynamic-Range-Metering-Bridge/assets/108012806/d131f5c4-9df1-4842-845a-a1d6fb926756)

...

Now it's up to your imagination to "Undecorate", "Move", "Size" and "Layer" the individual windows as you like, although not all options are available for all windows.

![image](https://github.com/Marin-Weigel/Roon-Dynamic-Range-Metering-Bridge/assets/108012806/9bec2805-f01b-45e4-b8ef-a8260f831e0e)

You might also want to set "Panel preferences" to hide the task bar ...
 
![image](https://github.com/Marin-Weigel/Roon-Dynamic-Range-Metering-Bridge/assets/108012806/adb5d833-e983-4ebc-b605-99b2a0bd9e8d)

Here's my layout:

![image](https://github.com/Marin-Weigel/Roon-Dynamic-Range-Metering-Bridge/assets/108012806/d01b02dc-2602-48cc-8b2e-c54fff648473)

...

Finally, back in the terminal window, (likely after entering "ctrl c" to get a prompt) enter the following line to activate automatic screen turn-on/turn-off:

***./output-monitor.sh &***

The screen should turn off now if you "Stop" Roon's stream, and turn back on when starting it -  it will stay on when pausing, though.

...

**There might still be quirks, omissions and faults in the guide, as I'm working from memory and my sketchy notes, so feel free to PM me via Roon's forum**

**GOOD LUCK AND HAVE FUN!**
