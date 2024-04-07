```bash
#!/bin/bash

#Considering recent issues (ghosts), if still have not-authorized changes, or something. 
#Reinstall the broken stuff, this fixed some issues, they must be ghost. Or bad update. 
#Why im worried (Weird changed both monitors to exact same lighting value), ignored it.
#Next was seeing errors due to permissions errors on amd libs. Reinstall below fixed
apt install --reinstall apt && apt update 
apt install --reinstall amdgpu-install libdrm-amdgpu1 xserver-xorg-video-amdgpu 

#x11-utils – no specific comments on this one yet. 
#This gives you xprop xwininfo – there are others but these are the ones I use sometimes 
apt install --reinstall x11-utils 
 
#Do not update Mesa at this time, download and extract what you need if ever need some 
#Luckily ubuntu separates the utils package for mesa from the main package, so this okay 
#This gives you glxgears es2gears glxinfo es2gears_wayland es2gears_x11 
apt install --reinstall mesa-utils  
 
#colord-tools this gives you colormgr command, not sure what else 
apt install --reinstall colord-tools 
 
#ddcutil this gives you a command interface for the i2c over hdmi, dsp for monitors 
apt install --reinstall ddcutil 
 
#glxinfo – gives you info about glx 
apt install –reinstall glxinfo 

#vulkan-tools this gives you vulkaninfo and some libraries too it seems. 
apt install –reinstall vulkan-tools 
 
#x11-apps this gives xeyes oclock xedit and others – I only use xeyes to test mouse in xwaylan 
apt  install –reinstall x11-apps

```
