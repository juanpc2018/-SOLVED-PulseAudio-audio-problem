# -SOLVED-PulseAudio-problem

There are several problems in Linux with USB sound interfaces like Focusrite 18i20 mk2, those </br>
require a propietary control software for Windows or OSX to change HW configuration. </br>

The software works in Wine, but the USB interface is Not detected. </br>
probably because Wine does Not have proper USB Passthrough like VirtualMachine Softwares, VMware, Parallels, VirtualBox, Qemu, Kvm, etc...  </br>

The USB device has playback, Recording, Control software, MIDI. *see list at the bottom. </br>

that has been solved by recreating the control software for Linux </br>
https://github.com/geoffreybennett/alsa-scarlett-gui/blob/master/docs/INSTALL.md </br>

2nd problem: </br>
PulseAudio sets Linux Default Sample Rate to 44.1Khz at Boot, fixed, forced, Not Auto / 0, does Not allow to change. </br>
if the USB Focusrite interface was set to External s/pdif clock & 48Khz,  </br>
will Not work. </br>

3rd problem: </br>
$ alsamixer </br>
does Not allow to change SampleRate, </br>
Only clock Source for that particular USB sound interface, and others. </br>

4th problem:  </br>
when USB interface was set to Internal Clock, </br>
works but only at 44.1Khz, </br>
when you try to play a 48Khz file/stream/source, </br>
PulseAudio Sample Rate Converter goes Crazy. </br>
because PulseAudio does Not allow to Change Sample Rate to 48Khz, </br>
activates a Software Sample Rate Converter, </br>
but Default SRC, does Not work well going Down from a Higher SR file / source like 48Khz, 88.2Khz, 96Khz to 44.1Khz Hw output. </br>

the proper way in theory is to change the USB interface Sample Rate internal clock to use the same Sample Rate required for the audio file, or a Higher Sample Rate *Oversampling. </br>

if the file is 44.1Khz but the Sound interface is 48Khz, 88.2Khz 96Khz, SRC works Ok. </br>
Pulse Audio Default Sample Rate works ok going Up,  </br>
from a lower sample rate file to higher SR output. </br>
but Not Downwards. </br>

Workarround was using JackAudio, </br>

The permanent sollution is simply comment out "delete" the ; and change 44100 to 48000 or 96000 Default Sample Rate. </br>
maybe auto or 0 could work, havent tested, yet. </br>
and reset Pulseaudio,  </br>
Problem Solved, works Ok. </br>

Internal or External Audio Clock, Works Ok. </br>

> cat /etc/pulse/daemon.conf </br>

line 82: default-sample-rate = 48000 </br>

save. </br>

$ pulseaudio - -kill </br>

[SOLVED]. </br>

$ pulseaudio --help </br>
$ pulseaudio --dump-resample-methods </br>

if want better quality:  </br>
resample-method = ffmpeg </br>
or speex-float-10  </br>
or speex-fixed-10 </br>
or soxr-vhq </br>
& </br>
default-sample-format = s24le </br>
or S32LE </br>
or S24_32LE </br>
or FLOAT32LE </br>

optional: </br>
alternate-sample-rate = 96000 </br>
to force Pulse Audio to Always Only 96kHz. </br>

$ pulseaudio --dump-resample-methods </br>
trivial </br>
speex-float-0 </br>
speex-float-1 </br>
speex-float-2 </br>
speex-float-3 </br>
speex-float-4 </br>
speex-float-5 </br>
speex-float-6 </br>
speex-float-7 </br>
speex-float-8 </br>
speex-float-9 </br>
speex-float-10 </br>
speex-fixed-0 </br>
speex-fixed-1 </br>
speex-fixed-2 </br>
speex-fixed-3 </br>
speex-fixed-4 </br>
speex-fixed-5 </br>
speex-fixed-6 </br>
speex-fixed-7 </br>
speex-fixed-8 </br>
speex-fixed-9 </br>
speex-fixed-10 </br>
ffmpeg </br>
auto </br>
copy </br>
peaks </br>
soxr-mq </br>
soxr-hq </br>
soxr-vhq </br>

tested so many things, Nothing worked, exept QjackCtl,  </br>
i thought it was a bug, the answer was so simple. </br>

nice wiki/tutorial: </br>
https://wiki.archlinux.org/title/PulseAudio </br>
https://freedesktop.org/software/pulseaudio/doxygen/sample.html </br>

----- </br>

DeJaVu </br>

----- </br>

if that does Not work, </br>
also needs to create/edit other config files,  </br>
but probably Not needed. </br>

---- </br>

/usr/share/alsa/alsa.conf </br>

---- </br>

/etc/asound.conf </br>

defaults.pcm.card 2 </br>
defaults.!rate 48000 </br>
defaults.ctl.card 2 </br>

---- </br>

/home/user/.asoundrc </br>
pcm.usb-audio { </br>
          type hw </br>
          card 2 </br>
          rate 48000 </br>
       } </br>
       
       ctl.usb-audio { </br>
          type hw </br>
          card 2 </br>
       } </br>

----- </br>

Just for fun: </br>

$ alsamixer </br>
F6 </br>
F2 </br>
 
/proc/asound/version </br>
/proc/asound/cards </br>
/proc/asound/devices </br>
/proc/asound/oss/devices </br>
/proc/asound//timers </br>
/proc/asound/pcm </br>

$ cat /proc/asound/devices </br>
  1:        : sequencer </br>
  2: [ 0- 3]: digital audio playback </br>
  3: [ 0- 7]: digital audio playback </br>
  4: [ 0- 8]: digital audio playback </br>
  5: [ 0- 9]: digital audio playback </br>
  6: [ 0-10]: digital audio playback </br>
  7: [ 0- 0]: hardware dependent </br>
  8: [ 0]   : control </br>
  9: [ 2- 0]: digital audio playback </br>
 10: [ 2- 0]: digital audio capture </br>
 11: [ 2- 1]: digital audio playback </br>
 12: [ 2- 0]: hardware dependent </br>
 13: [ 2]   : control </br>
 14: [ 1- 0]: digital audio playback </br>
 15: [ 1- 0]: digital audio capture </br>
 16: [ 1]   : control </br>
 17: [ 1- 0]: raw midi </br>
 33:        : timer </br>


$ aplay -L | grep CARD </br>
hw:CARD=HDMI,DEV=3 </br>
hw:CARD=HDMI,DEV=7 </br>
hw:CARD=HDMI,DEV=8 </br>
hw:CARD=HDMI,DEV=9 </br>
hw:CARD=HDMI,DEV=10 </br>
plughw:CARD=HDMI,DEV=3 </br>
plughw:CARD=HDMI,DEV=7 </br>
plughw:CARD=HDMI,DEV=8 </br>
plughw:CARD=HDMI,DEV=9 </br>
plughw:CARD=HDMI,DEV=10 </br>
hdmi:CARD=HDMI,DEV=0 </br>
hdmi:CARD=HDMI,DEV=1 </br>
hdmi:CARD=HDMI,DEV=2 </br>
hdmi:CARD=HDMI,DEV=3 </br>
hdmi:CARD=HDMI,DEV=4 </br>
dmix:CARD=HDMI,DEV=3 </br>
dmix:CARD=HDMI,DEV=7 </br>
dmix:CARD=HDMI,DEV=8 </br>
dmix:CARD=HDMI,DEV=9 </br>
dmix:CARD=HDMI,DEV=10 </br>
usbstream:CARD=HDMI </br>
hw:CARD=PCH,DEV=0 </br>
hw:CARD=PCH,DEV=1 </br>
plughw:CARD=PCH,DEV=0 </br>
plughw:CARD=PCH,DEV=1 </br>
sysdefault:CARD=PCH </br>
front:CARD=PCH,DEV=0 </br>
surround21:CARD=PCH,DEV=0 </br>
surround40:CARD=PCH,DEV=0 </br>
surround41:CARD=PCH,DEV=0 </br>
surround50:CARD=PCH,DEV=0 </br>
surround51:CARD=PCH,DEV=0 </br>
surround71:CARD=PCH,DEV=0 </br>
iec958:CARD=PCH,DEV=0 </br>
dmix:CARD=PCH,DEV=0 </br>
dmix:CARD=PCH,DEV=1 </br>
usbstream:CARD=PCH </br>
hw:CARD=USB,DEV=0 </br>
plughw:CARD=USB,DEV=0 </br>
sysdefault:CARD=USB </br>
front:CARD=USB,DEV=0 </br>
surround21:CARD=USB,DEV=0 </br>
surround40:CARD=USB,DEV=0 </br>
surround41:CARD=USB,DEV=0 </br>
surround50:CARD=USB,DEV=0 </br>
surround51:CARD=USB,DEV=0 </br>
surround71:CARD=USB,DEV=0 </br>
iec958:CARD=USB,DEV=0 </br>
dmix:CARD=USB,DEV=0 </br>
usbstream:CARD=USB </br>

$ aplay -l </br>
**** List of PLAYBACK Hardware Devices **** </br>
card 0: HDMI [HDA Intel HDMI], device 3: HDMI 0 [HDMI 0] </br>
  Subdevices: 1/1 </br>
  Subdevice #0: subdevice #0 </br>
card 0: HDMI [HDA Intel HDMI], device 7: HDMI 1 [HDMI 1] </br>
  Subdevices: 1/1 </br>
  Subdevice #0: subdevice #0 </br>
card 0: HDMI [HDA Intel HDMI], device 8: HDMI 2 [HDMI 2] </br>
  Subdevices: 1/1 </br>
  Subdevice #0: subdevice #0 </br>
card 0: HDMI [HDA Intel HDMI], device 9: HDMI 3 [HDMI 3] </br>
  Subdevices: 1/1 </br>
  Subdevice #0: subdevice #0 </br>
card 0: HDMI [HDA Intel HDMI], device 10: HDMI 4 [HDMI 4] </br>
  Subdevices: 1/1 </br>
  Subdevice #0: subdevice #0 </br>
card 1: PCH [HDA Intel PCH], device 0: CS4208 Analog [CS4208 Analog] </br>
  Subdevices: 1/1 </br>
  Subdevice #0: subdevice #0 </br>
card 1: PCH [HDA Intel PCH], device 1: CS4208 Digital [CS4208 Digital] </br> 
  Subdevices: 1/1 </br>
  Subdevice #0: subdevice #0 </br>
card 2: USB [Scarlett 18i20 USB], device 0: USB Audio [USB Audio] </br>
  Subdevices: 0/1 </br>
  Subdevice #0: subdevice #0 </br>

$ ls -l /proc/asound </br>
total 0 </br>
dr-xr-xr-x 18 root root 0 Jul 14 16:57 card0 </br>
dr-xr-xr-x  7 root root 0 Jul 14 16:57 card1 </br>
dr-xr-xr-x 10 root root 0 Jul 14 18:16 card2 </br>
-r--r--r--  1 root root 0 Jul 14 18:19 cards </br>
-r--r--r--  1 root root 0 Jul 14 18:19 devices </br>
lrwxrwxrwx  1 root root 5 Jul 14 18:25 HDMI -> card0 </br>
-r--r--r--  1 root root 0 Jul 14 18:25 hwdep </br>
-r--r--r--  1 root root 0 Jul 14 18:25 modules </br>
dr-xr-xr-x  4 root root 0 Jul 14 18:19 oss </br>
lrwxrwxrwx  1 root root 5 Jul 14 18:25 PCH -> card1 </br>
-r--r--r--  1 root root 0 Jul 14 18:19 pcm </br>
dr-xr-xr-x  6 root root 0 Jul 14 18:25 seq </br>
-r--r--r--  1 root root 0 Jul 14 18:19 timers </br>
lrwxrwxrwx  1 root root 5 Jul 14 18:25 USB -> card2 </br>
-r--r--r--  1 root root 0 Jul 14 18:19 version </br>

$ cat /proc/asound/cards </br>
 0 [HDMI           ]: HDA-Intel - HDA Intel HDMI </br>
                      HDA Intel HDMI at 0xa0910000 irq 73 </br>
 1 [PCH            ]: HDA-Intel - HDA Intel PCH </br>
                      HDA Intel PCH at 0xa0914000 irq 74 </br>
 2 [USB            ]: USB-Audio - Scarlett 18i20 USB </br>
                      Focusrite Scarlett 18i20 USB at usb-0000:00:14.0-1.4, high speed </br>
more here: </br>
https://forum.videolan.org/viewtopic.php?f=13&t=160171&p=529506#p528767 </br>
