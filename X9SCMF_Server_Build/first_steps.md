```bash
cd /etc/apt/sources.list.d/
rm pve-enterprise.list
```  
Once Proxmox is installed, here are the first steps.  
  
Login with ssh.  
Complete system updates:  
```bash
apt update
apt upgrade
```  
Navigate to the APT sources list directory and remove the Proxmox Enterprise source list:   
```bash
cd /etc/apt/sources.list.d/
rm pve-enterprise.list
```
  
Edit the main sources list to add the Proxmox VE no-subscription repository:  
```bash
cd ../
nano sources.list
```
  
Add the following line:  
```plaintext
deb http://download.proxmox.com/debian/pve bullseye pve-no-subscription
```
  
Update and upgrade the system again:  
```bash
apt update
apt upgrade
```

Configure GRUB for IOMMU and disable certain warnings:  
```bash
nano /etc/default/grub
```
  
Update the line to include:  
```plaintext
GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_iommu=on kvm.ignore_msrs=1 pcie_acs_override=downstream multifunction"
```
  
Before installing Proxmox VE, ensure to back up any existing system on the drive as it will be overwritten.  
BACKFLASH FIRST Before leaving the Ubuntu system:  
  
References for the setup can be found [here](https://habr.com/en/company/hetmansoftware/blog/547086/) , [here](https://www.redhat.com/sysadmin/resize-lvm-simple#:~:text=display%20LV%20information.-,Extend%20the%20Logical%20Volume,%2Fdev%2Fcentos%2Froot.) , [here](https://serverfault.com/questions/852920/lvm-raid-5-not-resulting-in-logical-volume-size-expected) , [here](https://unix.stackexchange.com/questions/691627/how-can-i-extend-logical-volume-to-100) for LVM RAID5 setup. (Note: One of the physical 1TB HDDs was bad, so only 2TB is used for this setup)  
Reference for creating a Btrfs file system is [here](https://unix.stackexchange.com/questions/691627/how-can-i-extend-logical-volume-to-100) .  
Use GParted to create LVM2 PV container partitions of 465 GiB on a GPT table for /dev/sdk, /dev/sdl, /dev/sdm (where /dev/sdl was a full 1TB drive, and others were old 500GB).  
```bash
apt install lvm2
pvcreate /dev/sdk1 /dev/sdl1 /dev/sdl2 /dev/sdm1
vgcreate vghdd1 /dev/sdk1 /dev/sdl1 /dev/sdl2 /dev/sdm1
lvcreate --type raid5 -L 10G -i 4 -n lvhddr5 vghdd1
lvextend -l +100%FREE /dev/vghdd1/lvhddr5
mkfs.btrfs -L smallstore /dev/vghdd1/lvhddr5
mkdir /mnt/smallstore
mount /dev/vghdd1/lvhddr5 /mnt/smallstore
```
  
The df command result showed smallstore is 1.5TB or 1.37TiB.
```bash
cd /mnt/smallstore
chown 1000:1000 .
```
  
Mounted from within Files/Nautilus and copied some files over.
During Proxmox VE setup:  

Boot into Proxmox VE setup or installer. Select debug mode for shell access.  
```bash
dd if=/dev/sdb conv=sync,noerror bs=512K | gzip -c > /mnt/smallstore/supermicroubuntu250G.img.gz
```
  
This completed 250GB in 31 minutes, averaging 116MB/sec.  
Post-Proxmox VE Installation:  
  
After the Proxmox VE installation, ensure the IOMMU and ACS patches are applied as described above.  
Mount smallstore (lvhddr5) in the fresh install.  
```bash
apt install gddrescue
```
  
Adding the RAID controller software (important for recognizing and managing RAID arrays):  
```bash
cd /mnt/smallstore/2021-007.1613.0000.0000_Unified_StorCLI/Unified_storcli_all_os/Ubuntu
ls # Result: storcli_007.1613.0000.0000_all.deb storcli.log
dpkg -i ./storcli_007.1613.0000.0000_all.deb
cd /mnt/smallstore/8-07-14_MegaCLI/Linux
ls # Result: megacli_8.07.14-1_all.deb MegaCli-8.07.14-1.noarch.rpm MegaSAS.log
```
  
Issue: DPKG version with Proxmox does not support ZST compression.  
Repack the .deb package:  
```bash
cd /mnt/smallstore/8-07-14_MegaCLI/Linux
mkdir deb-temp
cd deb-temp
apt install binutils zstd
ar x ../megacli_8.07.14-1_all.deb
zstd -d *.zst
rm *.zst
xz *.tar
ar r ../megacli_8.07.14-1_all-XZ.deb debian-binary control.tar.xz data.tar.xz
cd ../
apt install ./megacli_8.07.14-1_all-XZ.deb
```
  
END ISSUE - KEEP - NEED FOR NON-STANDARD / PROPRIETARY PACKAGES  
```bash
ln /opt/MegaRAID/storcli/storcli64 /usr/local/bin/storcli
ln /opt/MegaRAID/MegaCli/MegaCli64 /usr/local/bin/megacli
```
  
It is worth noting this hardware gets passed through to TrueNAS, so same installs in BSD.
Not sure if the final home for RAID will be in the virtualized system or on the base system yet.


### Articles Referenced

- For LVM RAID5 setup:
  - [Habr: LVM RAID5](https://habr.com/en/company/hetmansoftware/blog/547086/)
  - [Red Hat: Resizing LVM](https://www.redhat.com/sysadmin/resize-lvm-simple#:~:text=display%20LV%20information.-,Extend%20the%20Logical%20Volume,%2Fdev%2Fcentos%2Froot.)
  - [ServerFault: LVM RAID 5 not resulting in logical volume size expected](https://serverfault.com/questions/852920/lvm-raid-5-not-resulting-in-logical-volume-size-expected)
  - [Unix & Linux: How can I extend logical volume to 100%](https://unix.stackexchange.com/questions/691627/how-can-i-extend-logical-volume-to-100) (Note: One of the physical 1TB HDDs was bad, so only 2TB is used for this setup)

- Reference for creating a Btrfs file system:
  - [Unix & Linux: Extending Logical Volume to 100%](https://unix.stackexchange.com/questions/691627/how-can-i-extend-logical-volume-to-100)



### More was needed since should be installed on bsd and such too - The below is guess work. Ill refine later.
### Organize Binaries:  
First, create directories for `MegaCLI` and `storcli` under `/opt`. Then, place each version in its own subdirectory. This example assumes you have version `8.07.14` for `MegaCLI` and version `007.1613.0000` for `storcli`.  
  
```shell  
# Create directories for MegaCLI  
sudo mkdir -p /opt/megaraid/megacli/8.07.14  
  
# Place the MegaCLI binary in the versioned directory  
# Assume you've transferred the binary as /path/to/MegaCli64  
sudo cp /path/to/MegaCli64 /opt/megaraid/megacli/8.07.14/  
  
# Create directories for storcli  
sudo mkdir -p /opt/megaraid/storcli/007.1613.0000  
  
# Place the storcli binary in the versioned directory  
# Assume you've transferred the binary as /path/to/storcli64  
sudo cp /path/to/storcli64 /opt/megaraid/storcli/007.1613.0000/  
```  
  
### Create Symlinks:  
Create symbolic links in `/usr/local/bin` for ease of use. This allows you to execute `MegaCli` and `storcli` from anywhere without specifying their full paths.  
  
```shell  
# Create a symlink for MegaCLI  
sudo ln -sfn /opt/megaraid/megacli/8.07.14/MegaCli64 /usr/local/bin/MegaCli  
  
# Create a symlink for storcli  
sudo ln -sfn /opt/megaraid/storcli/007.1613.0000/storcli64 /usr/local/bin/storcli  
```  
  
### Usage:  
With the symlinks in place, you can simply use `MegaCli` or `storcli` commands directly:  
  
```shell  
MegaCli -showSummary -aALL  
storcli /c0 show  
```  
  
### Future Updates:  
When you download new versions of `MegaCLI` or `storcli`, simply place them in their respective versioned directory under `/opt`, and update the symlink to point to the new version. This method keeps your original files intact and makes it easy to revert to an older version if necessary.  


### Additional Dependencies Installation:    
Before setting up `MegaCLI` and `storcli`, you might need to install additional libraries on Ubuntu to ensure compatibility. This might not be necessary on TrueNAS, as FreeBSD systems and their derivatives have different dependency management.    
    
```shell    
sudo apt install libncurses5    
```    
    
This installs the `libncurses5` library, which is often required for terminal handling in command-line utilities.    
    
### Verifying Installation and Logs:    
After installation, verifying the setup or checking the logs might be useful:    
    
```shell    
# For MegaCLI    
sudo megacli help    
    
# Checking storcli operation logs    
cat storcli.log    
```    
    
### Creating Symlinks:    
Creating symlinks is crucial for easy access to the CLI tools without having to specify the full path each time.    
    
```shell    
# For storcli    
sudo ln -sfn /opt/MegaRAID/storcli/storcli64 /usr/local/bin/storcli    
    
# For MegaCLI    
sudo ln -sfn /opt/MegaRAID/MegaCli/MegaCli64 /usr/local/bin/megacli    
```    
    
### Basic RAID Management Commands:    
Here are some common `storcli` commands that were used for RAID management:    
    
```shell    
storcli /cx show all    
storcli /cx show    
storcli show    
storcli -cx    
storcli /cx    
storcli help    
storcli help | grep show    
storcli /cx show bios    
storcli show bios    
storcli /c0 show bios    
```    
    
### Installing MegaCLI on Ubuntu:    
For Ubuntu, the installation of `MegaCLI` was done via `dpkg`:    
    
```shell    
sudo apt install ./megacli_8.07.14-1_all.deb    
```    
    
### Reviewing Firmware Information:    
To check the RAID firmware version or related information, viewing the firmware file might be necessary:    
    
```shell    
cat 20.13.1-0240_iMR_2008_SAS_FW_2.130.404-4659.txt    
```    
    
### Firmware Installation Instructions (To Be Completed):    
This section is intended for future instructions on updating the firmware for RAID controllers without relying on Windows-based utilities. Details will be added as the process is defined and tested.    

