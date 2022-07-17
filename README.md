# -SOLVED-PulseAudio-problem

There are several problems in Linux with USB sound interfaces like Focusrite 18i20 mk2, those 
require a propietary control software for Windows or OSX to change DSP HW configuration.

The software works in Wine, but the USB interface is Not detected.
probably because Wine does Not have proper USB Passthrough like other VirtualMachine Softwares, VMware, Parallels, VirtualBox, Qemu, Kvm, etc... 

The USB device has playback, Recording, Control software, MIDI. *see list at the bottom.

2nd problem:
PulseAudio sets Linux Default Sample Rate to 44.1Khz at Boot, fixed, forced, Not Auto nor 0, and does Not allow to change.
if the USB Focusrite interface was set to External s/pdif clock & 48Khz, 
will Not work.

3rd problem:
$ alsamixer does Not allow to change SampleRate,
Only clock Source for that particular USB sound interface, and others i guess.

4th problem: 
when USB interface was set to Internal Clock,
works but only at 44.1Khz,
when you try to play a 48Khz file/stream/source,
PulseAudio Sample Rate Converter goes Crazy.
because PA does Not allow to Change Sample Rate to 48Khz,
activates a Software Sample Rate Converter,
but does Not work well going Down from a Higher SR file / source 48Khz, 88.2Khz, 96Khz to 44.1Khz Hw output.

the proper way in theory is to change the USB interface Sample Rate internal clock to use the proper Sample Rate required for the audio file, or a Higher Sample Rate. *Oversampling.
if the file is 44.1Khz but the Sound interface is 48Khz, 88.2Khz 96Khz, works Ok.
Pulse Audio Sample Rate works ok going Up, 
from a lower sample rate to higher output.
but Not Downwards.

Workarround was using JackAudio,

The permanent sollution is simply comment out "delete" the ; and change 44100 to 48000 Default Sample Rate.
maybe auto could work, havent tested, yet.
and reset Pulseaudio, 
Problem Solved, works Ok.

Internal or External Audio Clock.

/etc/pulse/daemon.conf
line 82: default-sample-rate = 48000

save.

$ pulseaudio -kill

[SOLVED].

ive tested so many things, Nothing worked, exept QjackCtl, 
i thought it was a bug, the answer was so easy.

-----

DeJaVu

-----

if that does Not work,
also needs to create/edit other config files, 
but probably Not needed.

----

/usr/share/alsa/alsa.conf

----

/etc/asound.conf

defaults.pcm.card 2
defaults.!rate 48000
defaults.ctl.card 2

----

/home/user/.asoundrc
pcm.usb-audio {
          type hw
          card 2
          rate 48000
       }
       
       ctl.usb-audio {
          type hw
          card 2
       }

-----

Just for fun:

$ alsamixer
F6
F2

/proc/asound/version
/proc/asound/cards
/proc/asound/devices
/proc/asound/oss/devices
/proc/asound//timers
/proc/asound/pcm

$ cat /proc/asound/devices
  1:        : sequencer
  2: [ 0- 3]: digital audio playback
  3: [ 0- 7]: digital audio playback
  4: [ 0- 8]: digital audio playback
  5: [ 0- 9]: digital audio playback
  6: [ 0-10]: digital audio playback
  7: [ 0- 0]: hardware dependent
  8: [ 0]   : control
  9: [ 2- 0]: digital audio playback
 10: [ 2- 0]: digital audio capture
 11: [ 2- 1]: digital audio playback
 12: [ 2- 0]: hardware dependent
 13: [ 2]   : control
 14: [ 1- 0]: digital audio playback
 15: [ 1- 0]: digital audio capture
 16: [ 1]   : control
 17: [ 1- 0]: raw midi
 33:        : timer


$ aplay -L | grep CARD
hw:CARD=HDMI,DEV=3
hw:CARD=HDMI,DEV=7
hw:CARD=HDMI,DEV=8
hw:CARD=HDMI,DEV=9
hw:CARD=HDMI,DEV=10
plughw:CARD=HDMI,DEV=3
plughw:CARD=HDMI,DEV=7
plughw:CARD=HDMI,DEV=8
plughw:CARD=HDMI,DEV=9
plughw:CARD=HDMI,DEV=10
hdmi:CARD=HDMI,DEV=0
hdmi:CARD=HDMI,DEV=1
hdmi:CARD=HDMI,DEV=2
hdmi:CARD=HDMI,DEV=3
hdmi:CARD=HDMI,DEV=4
dmix:CARD=HDMI,DEV=3
dmix:CARD=HDMI,DEV=7
dmix:CARD=HDMI,DEV=8
dmix:CARD=HDMI,DEV=9
dmix:CARD=HDMI,DEV=10
usbstream:CARD=HDMI
hw:CARD=PCH,DEV=0
hw:CARD=PCH,DEV=1
plughw:CARD=PCH,DEV=0
plughw:CARD=PCH,DEV=1
sysdefault:CARD=PCH
front:CARD=PCH,DEV=0
surround21:CARD=PCH,DEV=0
surround40:CARD=PCH,DEV=0
surround41:CARD=PCH,DEV=0
surround50:CARD=PCH,DEV=0
surround51:CARD=PCH,DEV=0
surround71:CARD=PCH,DEV=0
iec958:CARD=PCH,DEV=0
dmix:CARD=PCH,DEV=0
dmix:CARD=PCH,DEV=1
usbstream:CARD=PCH
hw:CARD=USB,DEV=0
plughw:CARD=USB,DEV=0
sysdefault:CARD=USB
front:CARD=USB,DEV=0
surround21:CARD=USB,DEV=0
surround40:CARD=USB,DEV=0
surround41:CARD=USB,DEV=0
surround50:CARD=USB,DEV=0
surround51:CARD=USB,DEV=0
surround71:CARD=USB,DEV=0
iec958:CARD=USB,DEV=0
dmix:CARD=USB,DEV=0
usbstream:CARD=USB

$ aplay -l
**** List of PLAYBACK Hardware Devices ****
card 0: HDMI [HDA Intel HDMI], device 3: HDMI 0 [HDMI 0]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
card 0: HDMI [HDA Intel HDMI], device 7: HDMI 1 [HDMI 1]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
card 0: HDMI [HDA Intel HDMI], device 8: HDMI 2 [HDMI 2]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
card 0: HDMI [HDA Intel HDMI], device 9: HDMI 3 [HDMI 3]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
card 0: HDMI [HDA Intel HDMI], device 10: HDMI 4 [HDMI 4]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
card 1: PCH [HDA Intel PCH], device 0: CS4208 Analog [CS4208 Analog]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
card 1: PCH [HDA Intel PCH], device 1: CS4208 Digital [CS4208 Digital]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
card 2: USB [Scarlett 18i20 USB], device 0: USB Audio [USB Audio]
  Subdevices: 0/1
  Subdevice #0: subdevice #0

$ ls -l /proc/asound
total 0
dr-xr-xr-x 18 root root 0 Jul 14 16:57 card0
dr-xr-xr-x  7 root root 0 Jul 14 16:57 card1
dr-xr-xr-x 10 root root 0 Jul 14 18:16 card2
-r--r--r--  1 root root 0 Jul 14 18:19 cards
-r--r--r--  1 root root 0 Jul 14 18:19 devices
lrwxrwxrwx  1 root root 5 Jul 14 18:25 HDMI -> card0
-r--r--r--  1 root root 0 Jul 14 18:25 hwdep
-r--r--r--  1 root root 0 Jul 14 18:25 modules
dr-xr-xr-x  4 root root 0 Jul 14 18:19 oss
lrwxrwxrwx  1 root root 5 Jul 14 18:25 PCH -> card1
-r--r--r--  1 root root 0 Jul 14 18:19 pcm
dr-xr-xr-x  6 root root 0 Jul 14 18:25 seq
-r--r--r--  1 root root 0 Jul 14 18:19 timers
lrwxrwxrwx  1 root root 5 Jul 14 18:25 USB -> card2
-r--r--r--  1 root root 0 Jul 14 18:19 version

$ cat /proc/asound/cards
 0 [HDMI           ]: HDA-Intel - HDA Intel HDMI
                      HDA Intel HDMI at 0xa0910000 irq 73
 1 [PCH            ]: HDA-Intel - HDA Intel PCH
                      HDA Intel PCH at 0xa0914000 irq 74
 2 [USB            ]: USB-Audio - Scarlett 18i20 USB
                      Focusrite Scarlett 18i20 USB at usb-0000:00:14.0-1.4, high speed
more here:
https://forum.videolan.org/viewtopic.php?f=13&t=160171&p=529506#p528767
