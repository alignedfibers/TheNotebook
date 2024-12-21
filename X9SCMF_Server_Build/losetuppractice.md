## ***History of the steps taken before doing actual pvcreates and vg group stuff to move volumes and stuff. Includes losetup straight forward steps***

```bash
#In my practice
truncate pv1 --size 100MB
truncate pv2 --size 100MB

losetup /dev/loop3 pv1
losetup /dev/loop4 pv2

pvcreate /dev/loop3
pvcreate /dev/loop4

vgcreate vg1 /dev/loop11

lvcreate -L 10M -n lv1 vg1
lvcreate -L 10M -n lv2 vg1

*** Used this to create a cache for the 32G disk added to hd vg
lvcreate -L 27G -n cache1 HDLibrary /dev/sdh
lvcreate -L 225M -n cache1meta HDLibrary /dev/sdh
lvconvert --type cache-pool --poolmetadata HDLibrary/cache1meta HDLibrary/cache1

lvconvert --type cache --cachepool HDLibrary/cache1 --cachemode writethrough HDLibrary/hdlibvol

mkfs.ext4 -j /dev/vg1/lv1
mkfs.ext4 -j /dev/vg1/lv2

mount /dev/vg1/lv1 mnt1/
touch mnt1/theonetwocopy
echo iamtheone >  mnt1/theonetwocopy
umount mnt1/

vgchange -a n vg1

#Ran a successful test
vgmerge -A y -l -t -v vg2 vg1

#Now did the merge an we will look at those results.
vgmerge -A y -l -v vg2 vg1
#results lvs
  lv1     vg2        -wi-------   12.00m                                                    
  lv2     vg2        -wi-------   12.00m
mount /dev/vg2/lv1 mnt2
#Results ls mnt2/
lost+found  theonetwocopy
#Results cat mnt2/theonetwocopy
iamtheone

umount mnt2/
lvconvert --type raid1 --mirrors 1 /dev/vg1/lv1 /dev/loop4
lvconvert --splitmirrors 1 --name lv1_copy /dev/vg2/lv1
#Results lvs
  lv1      vg2        -wi-a-----   12.00m                                                    
  lv1_copy vg2        -wi-a-----   12.00m                                                    
  lv2      vg2        -wi-a-----   12.00m 

#Test if these will work, then run without test
vgextend
lvconvert -t --type raid1 --mirrors 1 /dev/vg1/lv1 /dev/loop4
lvconvert -t --splitmirrors 1 --name lv1_copy /dev/vg1/lv1

#This was good to learn and knowing you cannot mirror outside of the volume group is a good lesson. 
#Next we look at creating a mirror to another pv on same volume group. Splitting that volume group and renaming the group on the device that has the old mirror.
#Review - https://wiki.networksecuritytoolkit.org/index.php/HowTo_Change_The_LVM_Volume_Group_Name_That_Includes_The_Root_Partition
#Review - https://man7.org/linux/man-pages/man8/vgextend.8.html
#Review - https://man7.org/linux/man-pages/man8/vgsplit.8.html
#Review - https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/logical_volume_manager_administration/lv_rename

#Next I complete on the real partitions.
vgextend ubuntu /dev/sdf3
#Results VGS
  VG         #PV #LV #SN Attr   VSize    VFree  
  fedora       1   2   0 wz--n-  <29.13g      0 
  genstorage   1   1   0 wz--n- <931.51g      0 
  ubuntu       2   2   0 wz--n-  562.98g 325.98g
  vg2          2   3   0 wz--n-  184.00m 128.00m
lvconvert --type raid1 --mirrors 1 /dev/ubuntu/root /dev/sdf3   *** Not completed yet / check on how to save to specific PV or device.
** Maybe something like: lvconvert -t --splitmirrors 1 --name lv1_copy /dev/vg1/lv1
** # lvconvert --splitmirrors 2 --name copy vg/lv /dev/sd[ce]1 per https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/logical_volume_manager_administration/lv
#Run tests on loop mounts again to check what I just pasted above in a more non-"oops I fucked up" way.
losetup /dev/loop1 pv1
losetup /dev/loop2 pv2




#Extend vg you would like mirror volume from onto pv intend to hold the mirror
#If volume already exist on pv, then only allow to merge volume (but they get split later)
vgextend vg1 /dev/sdX##
#vgmerge -A y -l -v vg2 vg1

#Convert to raid1 mirror and set the new pv as the volume for the mirror.
lvconvert --type raid1 --mirrors 1 /dev/vg1/lv1 /dev/sdX##

#Split the mirrors and name the result "copyoflv" on the specified PV
lvconvert --splitmirrors 1 --name copyoflv vg1/lv1 /dev/sdX##

#see what lv is on the pv
pvdisplay -m | more 

#check sync percentage 
lvs -a -o +devices

#deactivate volume before vgsplit
vgchange -a n vg1

#changevgname on specified pv (vgsplit oldname newname pv)
#if vg exists, it should merge, if not creates new.
vgsplit vg1 vg2 /dev/sdX##

#display lv new location and vg new name
vgs 
pvdisplay -m

#increase the size of the volumes
lvextend --size 50G /dev/volgrp/lgvol1
lvextend --size 50G /dev/volgrp/lgvol2

#increase the size of the filesystems
fsck -f /dev/volgrp/lgvol1
fsck -f /dev/volgrp/lgvol2
resize2fs /dev/volgrp/lgvol1
resize2s /dev/volgrp/lgvol1



/dev/sdc1: LABEL_FATBOOT="EFIPAYLOADS" LABEL="EFIPAYLOADS" UUID="BA74-35A5" TYPE="vfat" PARTLABEL="efipayloads" PARTUUID="cf9952b4-168a-4247-b0af-75a92dfe51cf"
/dev/sdc2: LABEL="ext4payload" UUID="45e5941c-39ce-407f-9393-e407e0c0b89f" TYPE="ext4" PARTLABEL="ext4payload" PARTUUID="c163074b-ed5d-478c-83a1-b9034014532c"


*Quickly type this off my phone, as cannot copy when not connected to the internet
Since this ended up being completed with btrfs and used LVM, as apposed to snapshot made with btrfs's
built-in snapshot functionality, you must treat the file system like a cloned filesystem if you wish to mount it on the original system 
you will not be able to have both copies concurrently mounted. Once unmounted and deactivate the previous volume, it worked but only once.
So you need to change the metadata UUID of the snapshot/clone filesystem, or you can mount the new one without physically removing the old drive.

After making the copy you also have to run
btrfstune -m /dev/mapper/vg-lv  
** this must be done while the volume is not mounted.

```