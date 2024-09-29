
### This is the monitors.xml file
### You can find them at the below locations

```bash
/var/lib/gdm3/.config/monitors.xml
~/.config/monitors.xml
/var/snap/gdm/common/.config/monitors.xml
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
          <vendor>unknown</vendor>
          <product>unknown</product>
          <serial>unknown</serial>
        </monitorspec>
        <mode>
          <width>2048</width>
          <height>1152</height>
          <rate>60</rate>
        </mode>
      </monitor>
    </logicalmonitor>
  </configuration>
  <configuration>
    <logicalmonitor>
      <x>0</x>
      <y>1336</y>
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
          <width>3440</width>
          <height>1336</height>
          <rate>74.999908447265625</rate>
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


