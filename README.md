General
---------


#### Starting a patch without the GUI

You can start a Pure Data patch without Pure Data GUI, by running `pd -nogui myPatch.pd`. If you use pd-extended, just replace `pd` by `pd-extended`. 

Now, a lot of problems can arise when using Pd without the GUI. For troubleshooting, see the section *Starting a patch without GUI, troubleshooting*.


#### Installing pd-extended on the Pi

See http://puredata.info/downloads/pd-extended-0-43-3-on-raspberry-pi-raspbian-wheezy-armhf


#### Shutting down the PI through SSH

See https://github.com/kr15h/RPi-Setup/tree/master/shutdown-service


Connecting a usb audio interface
----------------------------------

For troubleshooting problems with audio interfaces, see : http://wiki.linuxaudio.org/wiki/raspberrypi


#### Sound check

Let's first check that the sound output works

copy file `soundcheck.pd` to directory `/home/pi` on your RPI. Plug the sound output to some speaker or headphones, and run :

```
pd -nogui soundcheck.pd
```

With the usb interface, first print device list :

```
pd -nogui -listdev
```

Then `ctrl+c` to stop pd. Note the number of your output device and the start again pd this time telling it what device you want to use for output. For example my usb dongle output appears to be device number 3 :

```
pd -nogui -audiooutdev 3 soundcheck.pd
```


#### Mic check

Do this if you want to use a microphone, or any sound input.

Copy file `miccheck.pd` to directory `/home/pi` on your RPI.

To get the device number of your usb interface input, run : 

```
pd -nogui -listdev
```

Then, start pd specifying sound input and sound output (because you presumably want the input/output to go through the same usb device), and optionally the number of channels (my usb dongle only works with 1 channel) :

```
pd -nogui -audiodev 3 -inchannels 1 miccheck.pd
```


Connecting a midi controller
------------------------------

It seems like there is no way to force Pd to give us a reliable device ID for our midi device, so the simplest way is just to guess. Usually the ID is `1`.

Copy file `midicheck.pd` to directory `/home/pi` on your RPI.

Run the following command, and move some controls on your midi controller, you should see the values printed in the terminal.

```
pd -nogui -mididev 1 midicheck.pd
```


Connecting a game controller, mouse, ...
------------------------------------------

Install pd-hid on your laptop if you don't have it.

Making sure you have permissions


#### Managing permissions

See : http://puredata.info/docs/tutorials/HowToReadHIDDevicesInLinuxWithoutBeingRoot/


#### Testing a mouse controller

To listen to the events from a device, you will need its number. To print all connected devices, execute :

```
pd -nogui controllercheck.pd
```

Write down the number of your mouse, and now execute (my mouse is device 4) :

```
pd -send ";deviceNum 4" -nogui controllercheck.pd
```


Starting a patch without GUI, troubleshooting
-----------------------------------------------


#### Starting DSP

As you won't have a GUI, you need to start DSP when your patch is loaded (otherwise you won't have sound). For this, on load of your patch, you must send the message `[;pd dsp 1(`.

There's a catch though ... there is a bug currently in Pd, so that if you connect a `[loadbang]` directly to the message, and start the DSP instantly when the patch load, sound will not work. You need to put a delay in between, so basically executing the `[;pd dsp 1(` message a few hundred milliseconds after the load.

For an example of this, open `soundcheck.pd`. 


#### Adding the needed abstractions to the path

If you use some abstractions which are not in the same directory as your patch, you need to make Pure Data can find them. Otherwise it will just print an error `... couldn't create` and your patch will not run correctly.

Say you have a folder with abstractions in `/home/pi/my-abstractions`. To tell Pure Data to look for objects there, start it with the `-path` option :

```
pd -path /home/pi/my-abstractions myPatch.pd
```


#### Ensuring Pd is initialized properly

When started from the command-line, Pure Data might launch your patch before being initialized properly. This can therefore cause problems with some objects, and your patch might not work normally.

There is an ugly work around this : launching a first patch which waits for a little while, and then opens your patch.

There is an example of this in `patch-launcher.pd`. Open it to see how this works.
