## KM1 Firmware Analysis - Taking apart the km1_2T2R_V1.1_stock_ATV9_PI.V7.20200110.img
***Downloaded from here by clicking through:   
https://mega.nz/file/sVRQyQbD#***  
***Actual Download for me completed from here:   
https://mega.nz/ad244cf0-fd2c-44be-a7cb-4e11b52e287c***

## COMPLETED
*[ ] Extract each embedded image separately*  
- [X] Find the offsets system_root,product,odm,bootimg,ramdisk
```bash
#Listing areas of image we might be interested in.
binwalk km1_2T2R_V1.1_stock_ATV9_PI.V7.20200110.img | grep -v -f excludepatterns.txt > binwalkfound.txt 
```
- [] Review superblock at offsets for fs parts that should have one.
```bash
#Getting the EXT File Systems offsets we are interested in.
grep "Linux EXT" binwalkfound.txt | awk -F',' '{split($1, f1, " "); print f1[1], f1[4], $6, $7}' > extfsfound.txt
#Getting the Android bootimgs offsets we are interested in.
#There is a aboot.img, and a bootloader also. 
#Determining location for aboot, and bootloader harder.
#Took a good guess on aboot and extracted correct one and know offset
#Have a guess on bootloader, will review with uboot tools and such.
```