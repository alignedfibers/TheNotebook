https://askubuntu.com/questions/1468462/switching-to-nvidia-driver-switched-my-system-to-x11-now-im-back-to-using-nouv/1504086#1504086  

```plaintext

These two posts on ubuntu forums show to review the
/usr/lib/udev/rules.d/61-gdm.rules.
```
https://ubuntuforums.org/showthread.php?t=2486867&p=14143185#post14143185  

https://ubuntuforums.org/showthread.php?t=2486867&p=14143243#post14143243  

```plaintext
I was able to boot back into wayland after blacklisting nouveau driver,
installing my nvidia card drivers (AMD Card already installed, select no
to installing OpenGL or Graphics libararies when prompted), renaming
the file /usr/lib/udev/61-gdm.rules to /usr/lib/udev/61-gdm.rules.old
and then touch to create a new empty file /usr/lib/udev/rules.d/61-gdm.rules
hoping that any subsequent updates do not replace the file. **Unfortunately
it appears recent graphics updates in ubuntu 22.04.3 break something with
the udev rules file but am not perfectly sure. It ran over a year like this
now when I run some updates it goes back to starting in x11 and I think it
is because the udev rule, no way to know for sure, I just restored with
timeshift. Wish me luck with placing my rules with priority and hope it
works when I update next. This post shows to ensure the rule takes
priority, it needs to be placed in /etc/udev/rules.d and have a higher
number it starts with than the one in usr/lib/udev/.
```  
https://askubuntu.com/a/6158/1652771  
  
```plaintext
My use case is Nvidia K80 running compute only and does not process any
graphics outputs and does not need an x-thread. It works great with stable
diffusion (have to install specific versions of pytorch and cuda, but is
easy), and works within both gimp, and blender but requires some help for
some things. In order to make this work, required to have a separate AMD
GPU as the primary gpu, and do when prompted upon installing the nvidia
driver to choose if you would like to install OpenGL or EGL as a current
other version is already installed, SELECT NO. You have to have your AMD
Card all setup and working before installing the NVIDIA card, and should
not be using the AMD proprietary drivers (not sure which it is OpenGL, or
EGL has been a while.)

Since I had to look back at this today, and had a hard time finding what
I was looking for, this unanswered question (Oddly the first result on
google) is where I am placing the information I need to get back to, that
and keeping it in a file somewhere with my other notes on pytorch versions.****
```
