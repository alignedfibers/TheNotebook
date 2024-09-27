 
 
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

As for **fuse**, it's tied to how Nautilus allows user-space applications to handle mounting (without needing root access). So by removing **gvfs-fuse**, I’ll lose that ease-of-use feature, meaning I’ll have to manage mount points via `fstab` or manually through `mount`. That’s fine for me, but for others who prefer the simplicity of automatic mounts and easy access in Nautilus, this could feel cumbersome, I would say it is okay for a laptop that reboot often and does not run anything important you need access to on the network as it does cause nautilus to freeze and crash and has seemingly caused full crashes and though not proven, issues with not being able to type into the lock screen to login have gone away, though this change likely improved that, maybe other precipitating factors or root causes were involved and until the branches for gnome, nautilus get the bugs out of the sytem, and what I mean by that is they don't merge them back in because of bad merges.

---

### Cleaned-up Commands

Here’s the exact list of packages I removed, ensuring we only target what’s necessary without breaking any critical dependencies:


```bash
#The force-dependends tells apt ignore the Gnome-Core dependency - It soes no harm
sudo dpkg --force-depends --purge gvfs-fuse* avahi-ui-utils libavahi-ui-gtk3-dev libavahi-ui-gtk3-0

```

```bash
#Chat GPT recommends, but I have not yet tried or verifiedm usually these gsettings it thinks exist, do not at all exist, ill update later.
#Since my mouse icon still acts like it is searching (shows the clock) but obviously functions perfectly, and nautilus is not waiting, these commands might correct that, but I have my doubts.
gsettings set org.gnome.nautilus.preferences enable-interactive-search false
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

This version keeps the rest of your flow intact while fixing that piece you called out. Let me know if it fits your vision now!