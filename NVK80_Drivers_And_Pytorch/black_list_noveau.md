```bash
#!/bin/bash
sudo nano /etc/modprobe.d/blacklist.conf
#add blacklist nouveau to the file
sudo update-initramfs -u
sudo reboot
```
