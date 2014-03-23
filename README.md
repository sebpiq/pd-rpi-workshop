Misc
=====

Installing pd-extended on the Pi
----------------------------------
http://puredata.info/downloads/pd-extended-0-43-3-on-raspberry-pi-raspbian-wheezy-armhf


Shutting down the PI through SSH
---------------------------------

sudo shutdown -h now



Connecting a usb audio interface
=================================

troubleshooting : http://wiki.linuxaudio.org/wiki/raspberrypi


Sound check
------------

copy file `soundcheck.pd` to directory `/home/pi` on your RPI. Plug the sound output to some speaker or headphones, and run :

```
pd -nogui soundcheck.pd
```

With the usb interface, first print device list :

```
pd -nogui -listdev
```

Then `ctrl+c` to stop pd. Note the number of your output device and the start again pd this time telling it what device you want to use for output. For example my usb dongle output is device number 3 :

```
pd -nogui -audiooutdev 3 soundcheck.pd
```


Mic check
----------

copy file `miccheck.pd` to directory `/home/pi` on your RPI.

To get the device number of your usb interface input, run : 

```
pd -nogui -listdev
```

Then, start pd specifying sound input and sound output (because you presumably want the input/output to go through the same usb device), and optionally the number of channels (my usb dongle only works with 1 channel) :

```
pd -nogui -audiodev 3 -inchannels 1 miccheck.pd
```


Controller
=============

Install pd-hid on your laptop if you don't have it.

Making sure you have permissions


Managing permissions
---------------------

http://puredata.info/docs/tutorials/HowToReadHIDDevicesInLinuxWithoutBeingRoot/


Testing a mouse controller
----------------------------

To listen to the events from a device, you will need its number. To print all connected devices, execute :

```
pd -nogui controllercheck.pd
```

Write down the number of your mouse, and now execute (my mouse is device 4) :

```
pd -send ";deviceNum 4" -nogui controllercheck.pd
```


Starting a patch without GUI
==============================


Starting DSP
-------------




Ensuring Pd is initialized properly
--------------------------------------

When started from the command-line like so `pd myPatch.pd`, Pure Data might launch your patch before being initialized properly. This can therefore cause problems with some objects, and your patch might not work normally.

There is an ugly work around this. It is to start a patch  




