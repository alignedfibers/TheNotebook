## This may need to be redone after ever change display in gnome

***Specifically case having a EDID fob plugged for fake display.***
#### from ssh session nano /etc/gdm3/custom.conf
```ini
# GDM configuration storage
#
# See /usr/share/gdm/gdm.schemas for a list of available options.

[daemon]
# Uncomment the line below to force the login screen to use Xorg
#WaylandEnable=false

# Enabling automatic login
AutomaticLoginEnable = true
AutomaticLogin = exampleuser

# Enabling timed login
#  TimedLoginEnable = true
#  TimedLogin = user1
#  TimedLoginDelay = 10

[security]

[xdmcp]

[chooser]
```

#### Once you have completed the first login, you can change the autologin back, the first login completes some install finishing steps which are needed before we can complete the next steps. Once in the desktop set the monitor settings normally with right click display settings, and disable the ghost screen for now. Once they are set they will create the ~/.config/monitors.xml, copy this file into the /var/lib/gdm3/.config/monitors.xml, and while everything is correctly set to ensure it applies, notice that the one monitor I call "ghost" is enclosed in disabled tags in the xml, this is unique to individuals running linux in qemu vm, ontop of linux host, when installing on a physical machine potential similar issues may encounter with driver or gpu issues, but less likely, and you should just be able to get to work normally without worrying about this extra screen fob some how being detected as primary in GDM (Gnome Display Manager.)



#### Once you have copied over the monitors.xml files you do have to restart your gdm before doing the reboot, and then do the reboot. 
#### Previous versions required nulling out the /usr/lib/udev/rules.d/61-gdm.rules after you made a backup, but in this case that is not needed.
#### Also by default it appears the graphics are able to use the pass through as the discrete card, and still render throug virtio-spice, as the graphics have no tearing on pretty high quality, but exists issues on the hosts side since it does not seem the spice client is expecting to use that much memory, so may have to find a way to tweak that a litte. 


### This has been needed before -
### But lets try something else.
### This is the monitors.xml file
### You can find them at the below locations

```bash
/var/lib/gdm3/.config/monitors.xml
~/.config/monitors.xml
```

```xml
<!-- This disables the screen mirror fob BBC-->
<monitors version="2">
  <configuration>
    <logicalmonitor>
      <x>0</x>
      <y>0</y>
      <scale>1</scale>
      <primary>yes</primary>
      <monitor>
        <monitorspec>
          <connector>Virtual-1</connector>
          <vendor>RHT</vendor>
          <product>QEMU Monitor</product>
          <serial>0x00000000</serial>
        </monitorspec>
        <mode>
          <width>2048</width>
          <height>1152</height>
          <rate>60</rate>
        </mode>
      </monitor>
    </logicalmonitor>
    <logicalmonitor>
      <x>3440/x>
      <y>0</y>
      <scale>1</scale>
      <monitor>
        <monitorspec>
          <connector>HDMI-2</connector>
          <vendor>SAM</vendor>
          <product>C34H89x</product>
          <serial>H1AK500000</serial>
        </monitorspec>
        <mode>
          <width>3440</width>
          <height>1440</height>
          <rate>49.986808776855469</rate>
        </mode>
      </monitor>
    </logicalmonitor>
    <disabled>
      <monitorspec>
        <connector>HDMI-1</connector>
        <vendor>BBC</vendor>
        <product>HDP-V104</product>
        <serial>demoset-1</serial>
      </monitorspec>
    </disabled>
  </configuration>
</monitors>

```


