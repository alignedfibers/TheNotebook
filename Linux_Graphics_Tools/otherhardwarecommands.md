```bash
lshw
lsusb
lspci
for d in $(find /sys/kernel/iommu_groups/ -type l | sort -n -k5 -t/); do      n=${d#*/iommu_groups/*}; n=${n%%/*};     printf 'IOMMU Group %s ' "$n";     lspci -nns "${d##*/}"; done;
cat 99-usb-cdc_ncm-disabled.rules 
#gedit 99-usb-cdc_ncm-disabled.rules 
tail -f /var/log/{messages,kernel,dmesg,syslog}
rpcinfo -p
iptables -L
ss -tulpn | grep LISTEN
ss -tulpn


#Old used troubleshooting commands, good to know them though and keep them fresh on the mind. These are important mostly when you need them.
netctl
nmcli con show
systemd-resolve --status eno1
resolvectl status eno1
man resolvectl
ls /etc/systemd/network/
ls /etc/systemd/network
virsh net-list
history | grep virsh
virsh net-dumpxml default
virsh net-start default
touch /usr/sbin/dnsmasq
chmod +x /usr/sbin/dnsmasq 
virsh net-start default
history | grep nmcli
nmcli con up virtbr0
ls -al /etc/default/ufw
gedit /etc/default/ufw
gedit /etc/ufw/sysctl.conf 
gedit /etc/ufw/before.rules
nmcli con show
gedit /etc/ufw/before.rules
systemctl restart ufw
gedit /etc/ufw/before.rules
gedit /etc/ufw/sysctl.conf 
gedit /etc/ufw/sysctl.conf clear
history | grep virsh
history | grep gedit 
gedit /usr/share/libvirt/networks/default.xml
history | grep virsh
virsh net-destroy default
virsh net-undefine default
virsh net-define /usr/share/libvirt/networks/default.xml
apt install dnsmasq
virsh net-autostart default
virsh net-list
virsh net-start default
virsh net-list
dnsmasq --test
systemctl status dnsmasq
sudo ss -lntp
gedit /etc/dnsmasq.conf
ls /etc/dnsmasq.d/
ls /etc/dnsmasq.d/localnet-dns 
ls -al /etc/dnsmasq.d/localnet-dns 
cd /etc/dnsmasq.d/localnet-dns 
cat /etc/dnsmasq.d/localnet-dns 
cat /etc/dnsmasq.d/libvirt-daemon 
history | grep dns
ls /etc/libvirt/qemu/networks/
gedit /etc/libvirt/qemu/networks/default.xml 
sudo ss -lntp
gedit /etc/libvirt/qemu/networks/default.xml 
cd /etc/dnsmasq.d-available/
ls
cat libvirt-daemon 
cd /etc/dnsmasq.d
ls
cat libvirt-daemon
apt install auditd
ls
cat README
cat libvirt-daemon 
gedit /etc/NetworkManager/NetworkManager.conf
cat README
gedit /etc/libvirt/qemu/networks/default.xml 
ls
cat libvirt-daemon 
cat localnet-dns 
cat localnet-daemon 
touch ~z0000-clear
mv \~z0000-clear \~z0000-clear-all
cat libvirt-daemon 
gedit \~z0000-clear-all 
history grep net
history | grep nmcli
nmcli con show
systemctl restart dnsmasq
journalctl -xeu dnsmasq.service
ls
gedit \~z0000-clear-all 
systemctl restart dnsmasq
systemctl start dnsmasq
journalctl -xeu dnsmasq.service
gedit \~z0000-clear-all 
systemctl start dnsmasq
history | grep ss
ss -lntp
systemctl stop dnsmasq
ss -lntp
gedit \~z0000-clear-all 
systemctl stop dnsmasq
systemctl start dnsmasq
ss -lntp
systemctl stop dnsmasq
ss -lntp
systemctl restart dnsmasq
gedit /etc/libvirt/qemu/networks/default.xml 
systemctl stop dnsmasq
ls
cat localnet-daemon 
cat libvirt-daemon 
gedit \~z0000-clear-all 
systemctl start dnsmasq
ss -lntp
history | grep virsh
virsh net-list
ls /usr/share/libvirt/networks
ls /usr/share/libvirt/
history | grep libvirt
/etc/libvirt/qemu/networks/default.xml
gedit /etc/libvirt/qemu/networks/default.xml
gedit /etc/libvirt/networks/default.xml 
gedit /usr/share/libvirt/networks/default.xml
history | grep virsh
virsh net-list
virsh net-dumpxml default
virsh net-undefine default
gedit /usr/share/libvirt/networks/default.xml
virsh net-define /usr/share/libvirt/networks/default.xml
virsh net-autostart default
virsh net-list
virsh net-start default
ss -lntp
systemctl start dnsmasq
ss -lntp
systemctl stop dnsmasq
ss -lntp
ls
ls libvirt-daemon 
cat libvirt-daemon 
cat README
gedit /etc/default/dnsmasq 
systemctl disable dnsmasq
virsh net-define /usr/share/libvirt/networks/default.xml
virsh net-list
gedit /usr/share/libvirt/networks/default.xml
ss -lntp
(journalctl -xe | grep libvirtd and journalctl -xe | grep dnsmasq)
(`journalctl -xe | grep libvirtd` and `journalctl -xe | grep dnsmasq`)
`journalctl -xe | grep libvirtd` and `journalctl -xe | grep dnsmasq`
journalctl -xe | grep libvirtd
journalctl -xe | grep dnsmasq
journalctl -xe | grep dns
ss -lntp
ls /etc/libvirt/
gedit /etc/libvirt/qemu.conf 
virsh net-list
virsh net-stop default
virsh net-destroy default
virsh net-start default
ss -lntp
history | grep nmcli
nmcli con show
history | grep virsh
virsh net-dumpxml default
ss -lntp
gedit /etc/libvirt/networks/default.xml 
history | grep gedit
ss -lntp
ps aux | grep dns
journalctl -u dnsmasq
journalctl -u lease
journalctl -u dns
ps aux | grep dns
gedit /usr/lib/libvirt/libvirt_leaseshelper
ls /usr/lib/libvirt/
gedit /usr/lib/libvirt/libvirt-guests.sh 
file /usr/lib/libvirt/libvirt_leaseshelper
ls
clear
ps aux | grep dns
ss -lntp
systemctl status dnsmasq
history | grep virsh
virsh net-dumpxml default
virsh net-list
exit
gedit /var/lib/libvirt/dnsmasq/default.conf
history | grep conf
history | grep gedit
gedit /etc/libvirt/networks/default.xml
ls /etc/libvirt/networks/
gedit /etc/libvirt/qemu/networks/default.xml
history | grep virsh
virsh net-list
virsh net-dumpxml default
virsh net-destroy default
virsh net-list
virsh net-dumpxml default
virsh net-start default
virsh net-dumpxml default
history | grep gedit
gedit /usr/share/libvirt/networks/default.xml
virsh net-list
virsh net-destroy default
virsh net-undefine default
virsh net-list
history | grep virsh
virsh net-define /usr/share/libvirt/networks/default.xml
virsh net-autostart default
virsh net-start default
virsh net-dumpxml default
ps aux | grep dns
history | grep ss
ss -lntp
virsh net-destroy default
virsh net-undefine default
ss -lntp
virsh net-list default
virsh net-list
gedit /usr/share/libvirt/networks/default.xml
virsh net-define /usr/share/libvirt/networks/default.xml
virsh net-autostart default
virsh net-start default
virsh net-dumpxml default
hostory | grep systemd-resolve
history | grep systemd-resolve
cd /etc/systemd/
ls
cd network
ls
cd ../
cat system.conf 
clerar
clear
ls
cat resolved.conf 
gedit resolved.conf 
exit
ss -lntp
virsh net-list
virsh net-dumpxml default
ss -lntp
history | grep dkms
cd lib/dkms/
ls
ls nvidia
ls nvidia/470.199.02/
ls nvidia/470.199.02/source
ls /usr/src
history | grep .service
cd /lib/systemd/system/
ls
history | grep .service
ls | grep way
ls | grep x11
gedit x11-common.service 
cat /usr/lib/udev/rules.d/61-gdm.rules.old 
fdisk
fdisk -l
fdisk /dev/nbd0
fdisk -l /dev/nbd0
mkfs.ext4 /dev/nbd0
/usr/bin/opensnitchd -rules-path /etc/opensnitchd/rules/
apt install --reinstall timeshift
timeshift restore
timeshift --list
timeshift --create --comments "First Backup" --tags M
cat /etc/fstab
```

