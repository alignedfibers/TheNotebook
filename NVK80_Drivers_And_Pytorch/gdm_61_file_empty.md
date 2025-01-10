## Freaking do this if and only if you want to be able to use your nvidia card for compute only.
## The idea here is you are disabling GTK and Gnome from using your nvidia card completely.
## The other idea here is you are just removing the script that verifies if GPUs support wayland.
## Matrox and some NVIDIA GPU cause problems in wayland so 61-gdm disables it. 
## What needs to be done? Make a backup of the file to .old. Delete all content from original and save.
## This also needs to be done on Ubuntu 22.04 inside the VM if you want passthrough gpu to work, and wayland to work.
## You must black list matrox drivers or other drivers for the bad gpus that should not be loaded and disable via udev.
## You may want to use UDEV rules to compeletely disable some onboard graphics on some supermicro boards if use for home.
## Keep in mind IPMI will work up to once the graphics load in OS if you completely disable MATROX.
## PS, you do not have to disable your nvidia card, or card otherwise filtered out in this file but you do need at least:
## -Installed Primary AMD Card /supported card that you wish to display your graphic through and have wayland option available.
## -Actually have a second/onboard-mb gpu wish NOT to use in OS, and gets caught in 61-gdm disabling your wayland option.
## -Understand and read this script and have identified the methods and agree that updating the script is hard.
## -Disable any of the "bad" gpus from being primary in GTK/Gnome. Or disable your compute cards from Gnome.
## -Enabled your AMD or other supported card not filtered in 61-gdm to be used by Gnome/GTK as the only primary.
## -If nvidia card for compute, you did not install via apt, since nvidia opengl not coexist with amd opengl.
## -If nvidia card for compute, you did download and install proprietary driver direct from Nvidia
## -If nvidia card for compute, you did download and install correct latest cuda for the nvidia card you have.
## -Acknowledge have done steps any onboard graphics or other that are "Bad" to disable via udev or other method.
## -Acknowledge have done steps to blacklist noveau.
## -Per my recommendation if you have a passthrough, or want to unbind rebind your nvidia gpu, you do not let GTK start a service on it.
## -Realize that if you use X, it will tie a freaking process to you Nvidia GPU automatically even if set to compute only, it will sit idle.
## -Realize that if you want to live unload and load drivers for the nvidia GPU such that you want to run locally & passthrough no reboot
## -Realize you cannot bind unbind live if there is an X-thread tied to your gpu running concurrently, and that process needs to stop.
## -Realize that just using wayland stops this X-thread issue from happening.
## -Per my recommendation, research driverctl, and us it to bind your vfio or other driver as the correct way to do it.
## Do this if you want wayland but it was not letting you before.
## Do this inside the vm if you have older than 24.04 ubuntu inside qemu vm and want to use a passthrough.
## Realize the new 24.04 ubuntu 61-gdm leaves wayland enabled on the virtio gpu in vm without doing this.
## Realize I have not fully tested 24.04, and have yet to know if it disables wayland option with certain GPUs present
```bash
sudo mv gdm_61-gdm.rules gdm_61-gdm.rules.old
sudo touch gdm_61-gdm.rules
```