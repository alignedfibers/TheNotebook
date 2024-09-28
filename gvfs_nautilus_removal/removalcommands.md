 
 
 ### This is like a trial and error command history, just historical
 ```bash
 #Nautilus will stop crashing, and you can still see everything that you have mounted
 #I believe USB drives may still automount, but if they do not, it is very easy.
 #My network drives do not need gvfs, and the nautilus integration with the protocols is not needed.
 #I will keep nautilus right, but "default" file system app on my system will start aligning with how android does it.
 #I like the share options in the android applications that allow to choose an app at the time of upload or download or share.
 #I have some integration to do for some of my "cloud" media apps and stuff that removes a lot of work away from nautilus.
 #Also Grails, Jruby, Kafka, GO, and
 
 sudo dpkg --force-depends --purge gvfs-fuse* avahi-ui-utils libavahi-ui-gtk3-dev libavahi-ui-gtk3-0
 #apt purge notion
 #apt purge gvfs-udisks2-volume-monitor
 #apt purge gvfs-daemons
 #apt purge gvfs-fuse*
 #apt purge libmm-glib0 check on this
 #apt remove avahi-ui-utils vinagre remmina libavahi-ui-gtk3-dev libavahi-ui-gtk3-0

 #avahi is not needed since resolve.d can handle multicast protocols and the added features it gives nautilus are not that useful.
 #Unfortunately after removing all of this it seems that when you click on the  "+ Other Locations" It shows all the correct drive listingings but seems that it is a bug that the mouse cursor still shows the clock like it is waiting for something which I am pretty sure is what is be removed here that is normally loading here, but at least it does not crash, and since I write .service files and load before and after conditions to ensure anything I place within fstab loads in the correct order if it is dependent on any services like Network, NBD, and DNS or dependent on another drive being loaded my files in /etc/fstab and /etc/systemd/system/ *.service excetera, wan might be more locations which I would like to know where else I might of put those other scripts based on the normal locations for them, not sure there is like .module or other type of file name which is the .unit or something like that an where those might be, I know on some of my services I don't have them setup in the best way possible but at least I can start them from systemctl. Anyways what is the trouble here is want to get rid of the clock on the mouse cursor, it seems to do nothing and is not an issue, more of an annoyance, at least it does not crash or have trouble with the part of nautilus that I have said to remove above, oh, and also since I did not write those correctly and they need to force remove without removing other things that supposedly have dependency on them, please rewrite that, nautilus has feature that use these, but is not dependent on them it is only soft dependent and listed in it's dependencies and indeed a simple fix which I am trying to come up with or learn how to do can fix the one little annoyance making this change causes, overall, this changes makes my Gnome Based Ubuntu 22.04 LTS Extremely More Stable, in combination with removing the System DNSMasq instance that clashes..
```
```text
 Please look at my troubled mouse cursor I attached as screenshot, and please reveiw and rewrite to make more sense in what I did to lol behead my GNU Gnome if I am even calling it by the right name, anyways yeah, if you can provide a warning at the bottom of this how-to for the mouse clock issue which does not actually cause a wait or clocking, but rather only effects the mouse please do and provide suggestion on how to possibly fix that, seriously though people should be able to install a stable Ubuntu and opt-in to this unstable stuff if they like the candy features and ease of use, as a long time linux user, mount -remount or fstab and systemd related mounting or just using the Disks Application to set mounting specifics and mount and unmount drives is perfectly fine, being able to type in the network drive locations and mount them via user space is a little more than I need, I might later add a very vetted version of smb protocol to gnome and change the default for the address bar to show the address without the ctrl+l because omg, I keep hitting Windows+l and locking my screen everytime I need to copy the address in bar, the resource location is more important than the pretty to me, I am happpy to add a pane or note at top with the "pretty" later but dang, the shipped-as-is-ubuntu-gnome or gnome on anything else is def not to my liking..

```

---

### What I Did

I trimmed down Nautilus, getting rid of components that were just adding unnecessary layers. I removed a lot of the automatic mounting features like `gvfs`, because I prefer to handle mounts manually through `fstab` and `systemd`. It’s more efficient for me, and I don’t need things running in the background when I can set it up once and forget it.

I kind of mis-spoke in my original report when talking about **Avahi**. The **avahi-ui-utils** and other packages I mentioned aren’t part of the Avahi or Multicast server—they’re more UI-related, acting as clients. So removing them doesn’t affect multicast itself, but it *does* disable the graphical features in Nautilus that rely on Avahi to automatically detect network devices. While I don’t use those features, I realize that for others who rely on them to easily browse network devices like printers or NAS systems, losing this could be inconvenient.

---

### The Impact of Removing gvfs and fuse

In addition to Avahi, I removed some parts of **gvfs**, which is GNOME’s virtual file system layer. It’s what enables Nautilus to handle various file systems and network protocols (like SMB, SFTP, or Google Drive) without needing you to mount them manually. It’s pretty handy for some, but I’m all about keeping things lean and manually mounting my drives when I need them.

By removing **gvfs**, I lose some of the seamless mounting and integration Nautilus offers. Here’s what this means for me:
- **Network Drives**: I’ll no longer be able to click on network locations in Nautilus and have them automatically mount. (In fact they will not even list which lowers the time taken when navigating to the tab that searches for these items and stops a lot of unstable crashing issues in nautilus, all drives already mounted either via fstab, systemd .unit files or somehow via .service files will show quickly instead and not lookups are done)
- **Cloud Services**: If you were using Nautilus to access cloud storage services (e.g., Google Drive), that functionality goes away unless re-enabled with other tools.

As for **fuse**, it's tied to how Nautilus allows user-space applications to handle mounting (without needing root access). So by removing **gvfs-fuse**, I’ll lose that ease-of-use feature, meaning I’ll have to manage mount points via `fstab` or manually through `mount`. That’s fine for me, but for others who prefer the simplicity of automatic mounts and easy access in Nautilus, this could feel cumbersome, I would say it is okay for a laptop that reboot often and does not run anything important you need access to on the network as it does cause nautilus to freeze and crash and has seemingly caused full crashes and though not proven, issues with not being able to type into the lock screen to login have gone away, though this change likely improved that, maybe other precipitating factors or root causes were involved and until the branches for gnome, nautilus get the bugs out of the sytem, and what I mean by that is they don't merge them back in because of bad merges I will avoid anything that causes await type scenerios in nautilus.

---

### Cleaned-up Commands

Here’s the exact list of packages I removed, ensuring we only target what’s necessary without breaking any critical dependencies:


```bash
#The force-dependends tells apt ignore the Gnome-Core dependency - It does no harm, so I leap with unwavering arms -lol.
sudo dpkg --force-depends --purge gvfs-fuse* avahi-ui-utils libavahi-ui-gtk3-dev libavahi-ui-gtk3-0
sudo apt remove nautilus-wipe
sudo apt remove nautilus-script-collection-svn
sudo apt remove nautilus-image-converter
sudo apt remove nautilus-sendto
sudo apt remove nautilus-dropbox

```

```bash
#Chat GPT recommends, but I have not yet tried or verifiedm usually these gsettings it thinks exist, do not at all exist, ill update later.
#Since my mouse icon still acts like it is searching (shows the clock) but obviously functions perfectly, and nautilus is not waiting, these commands might correct that, but I have my doubts. (Indeed I have tried and to no luck, nautilus doesnt have any scripts to edit, ill need a modified .so to fix the mouse wait icon)
gsettings reset org.gnome.desktop.interface cursor-theme
nautilus -q
rm -rf ~/.cache/nautilus ~/.thumbnails
```

I double-checked to make sure nothing crucial was accidentally pulled along with these. The goal here was to strip down Nautilus’s reliance on these auto-mounting and network discovery features without impacting my manual control.

---

### What’s Next

One thing I’m thinking about is whether there's anything else to tweak. Since I’ve simplified Nautilus, I might look into adding a basic file-sharing tool that lets me manually control which apps I use for uploading, downloading, and managing network resources. GNOME’s built-in features are more for user-friendly access, but once you’re in the world of custom mount points and manual network setups, there’s a lot more flexibility. I’ll keep exploring to see if there’s anything else that can make this leaner without losing control.

---

### Final Warning for Others

If you’re following my setup, just be aware that pulling **Avahi**, **gvfs**, and **fuse** will strip away some of Nautilus’s automatic features. This includes the ability to auto-detect network devices, easily mount network drives, and access cloud services through the file manager. For most advanced users or those comfortable with manual configurations, this is a solid trade-off for a cleaner, more stable system. But if you prefer the ease of Nautilus’s built-in integration with network devices and cloud services, you might want to reconsider pulling these features.

---

### Keep in mind these important packages below stay installed

```bash
#If you uninstall these, your on your own.
#I do not recall or know if I tried. We only stripped out UI parts.
#Since they are libs I can still write my own code using them.
> apt list --installed | grep gvfs
gvfs-common/jammy-updates,jammy-updates,now 1.48.2-0ubuntu1 all [installed]
gvfs-daemons/jammy-updates,now 1.48.2-0ubuntu1 amd64 [installed]
gvfs-libs/jammy-updates,now 1.48.2-0ubuntu1 amd64 [installed]
gvfs/jammy-updates,now 1.48.2-0ubuntu1 amd64 [installed]

> apt list --installed | grep avahi
libavahi-client3/jammy-security,jammy-updates,now 0.8-5ubuntu5.2 amd64 [installed,automatic]
libavahi-client3/jammy-security,jammy-updates,now 0.8-5ubuntu5.2 i386 [installed,automatic]
libavahi-common-data/jammy-security,jammy-updates,now 0.8-5ubuntu5.2 amd64 [installed,automatic]
libavahi-common-data/jammy-security,jammy-updates,now 0.8-5ubuntu5.2 i386 [installed,automatic]
libavahi-common3/jammy-security,jammy-updates,now 0.8-5ubuntu5.2 amd64 [installed,automatic]
libavahi-common3/jammy-security,jammy-updates,now 0.8-5ubuntu5.2 i386 [installed,automatic]
libavahi-core7/jammy-security,jammy-updates,now 0.8-5ubuntu5.2 amd64 [installed,auto-removable]
libavahi-glib1/jammy-security,jammy-updates,now 0.8-5ubuntu5.2 amd64 [installed,automatic]
python3-avahi/jammy-security,jammy-updates,now 0.8-5ubuntu5.2 amd64 [installed]

fuse3/jammy,now 3.10.5-1build1 amd64 [installed,automatic]
fuseiso/jammy,now 20070708-3.2build1 amd64 [installed,automatic]
libfuse2/jammy,now 2.9.9-5ubuntu3 amd64 [installed,automatic]
libfuse3-3/jammy,now 3.10.5-1build1 amd64 [installed,automatic]

#also udisks as a simple way to mount is very useful, and windows users would feel kinda at home. It lacks fuse, and the plug-n-play for usb, but it can mount for you. You do have to place your creds in the box to complete though. Please know that many applications have a focus issue with the creds box and you cannot complete the creds, it was fixed at one point on ubuntu 22.04 and I do not recall at what version or package, then they updated and I think had accidentally merged an old bug back in, and then fixed again, and then somehow at one point has found a middle ground, meaning it is fixed, but it is optional if the new UI components are using the corrected version or the broken version, guessing somehow old version still needed, this is observation only as it is not happening everywhere, its just whatever gtk app calls the authentication screen. I am pretty sure I can fully replace this with the PAM, Kuberos UI interface, though I am afraid of locking myself out and messing with it. Also not sure if there is a gtk version.

gir1.2-udisks-2.0/jammy,now 2.9.4-1ubuntu2 amd64 [installed,automatic]
libudisks2-0/jammy,now 2.9.4-1ubuntu2 amd64 [installed,automatic]
udisks2-bcache/jammy,now 2.9.4-1ubuntu2 amd64 [installed]
udisks2-btrfs/jammy,now 2.9.4-1ubuntu2 amd64 [installed]
udisks2-doc/jammy,jammy,now 2.9.4-1ubuntu2 all [installed]
udisks2-lvm2/jammy,now 2.9.4-1ubuntu2 amd64 [installed]
udisks2-zram/jammy,now 2.9.4-1ubuntu2 amd64 [installed]
udisks2/jammy,now 2.9.4-1ubuntu2 amd64 [installed]

```

Here’s the revision with the first-person perspective for the "Try Containers for IoT" section:

---

### Avahi-UI Removal Impact:
Removing **avahi-ui** should not affect much in your daily use. At most, you’ll lose an icon or some graphical feature in Nautilus, but those are likely broken or limited in functionality anyway. The core **avahi libraries** and **client** remain intact, meaning they can still be used directly in code, by other applications more aligned with smart device management (than Nautilus or GNOME), or via the command line.

### Containers and Isolation Options:
This is a great opportunity to explore more powerful container and isolation solutions for managing IoT devices. **uml**, which I love due to its history as the virtualization platform for Linode before they switched to Xen Hypervisor, comes first. Next is **chroot**, a simpler but highly effective way to isolate processes by changing the root directory. After that, consider **lxc**, **kubernetes**, and **docker**, which provide robust container environments for isolating tasks and managing IoT or home automation.

If you’re looking for simpler options, **flatpak** or **snap packages** virtualize file systems but offer less control than uml or chroot. However, they are still useful for isolating certain apps.

### Avahi Core Still Works:
Even though the UI portion of avahi is removed, the **avahi libraries** and **client tools** are still available for direct code use, more appropriate applications, or command-line implementations. My system retains core networking functionality without relying on Nautilus, and more specialized apps will continue to work as expected.

### Trying Containers for IoT:
I think I might try running apps like **home assistant** in containers. **uml**, **chroot**, **lxc**, **kubernetes**, or **docker** provide the flexibility and control I need to manage IoT devices without cluttering my desktop environment. Containers isolate tasks and offer a more reliable way to handle device management than integrating IoT into Nautilus. While **flatpak** or **snap** are useful for certain isolated apps, they don’t give me the same level of control as full container solutions.

---
