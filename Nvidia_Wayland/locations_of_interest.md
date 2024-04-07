```plaintext
https://superuser.com/questions/994356/backing-up-kernel-modules-from-a-gnu-linux-installation/
1832869#1832869 

/usr/src 
/lib/modules/ 
/lib/firmware 
/var/lib/dkms 

Sorry for the briefness here, but I am plugging into this conversation pointers to 
important directories and considerations for backing up the modules and keeping 
your modules separate and ready for deployment as need, but though it direct to 
the areas of concern, it is lacking a full solution, and as I am working on this, 
I consider this a stub for the future home of the full solution. I understand a 
possible consideration to move this but, I am working it, and current provide has 
beneficial overview, I shall try answer my own questions below and give more useful 
details. 

Backstory of the issue: Some of my kernel modules like evdi, nvidia, amd have 
dkms installer that are working for me when the kernel updates, and when I change 
versions between different hardware, I need to be able to easily determine lsmod 
see the current version installed, then complete the modprobe steps to remove that 
version then re-add the other one needed as long as it is still supported by the 
current version of glibc and kernel I am using. Also important is when I change the 
modules installed I really need the old or alternative modules to continue living 
in the filesystem so I do not have to go through a lot of trouble to install. 
Specifically useful superpower to switch easily by running the build and doing and 
lsmod, modprobe and retaining the src directory for the old drivers until I am sure 
I do not need them anymore, it is helpful in running a test bench, hot swapping, or 
just using the right driver for the device 

Other uses such as multigpu setups using the correct driver for the duty the 
hardware is performing, since this is a challenge tools like driverctl exist and 
help but without the precompiled modules available or the src to compile them it is 
hard to do.  

**Note: NVIDIA proprietary drivers keep uninstalling the old driver completely in 
install a new one, and the package manager in this case apt keeps trying to tell me 
I need their packages for NVIDIA installed directly from the package manager, 
unfortunately the package manager is not very good yet with this - Even though 
NVIDIA open sourced the driver, and it is installed via the distributions package 
manager, they still try to install the driver for the Consumer Card and I run Teslas 
which are Commercial Cards and have a slightly different driver version than the 
consumer cards, thus hoping that this part catches up and we can keep allowing me to 
keep my drivers, because if I don't then things don't work, and I much prefer 
working, and also easily switching between drivers.  

Anyways, as I was asking a question relating to the OPs question which is partially 
answered in the comments detailing where the modules are located, not all the 
information was provided, can anyone provide additional information about what we 
can find under /usr/src/ and what we can find under /var/lib/dkms as this is 
important also and relates to keeping your module src and modules handy for install.  

**Other considerations: The AMD drivers use symlinks to point to the libs, and the 
same location for the symlinks is used with the proprietary driver and the open 
source driver, and they do keep multiple places to point to for those libraries for 
AMD, so having the file locations and the symlinks where everything goes is awesome. 
Thus, the possibility of drivers having conflict because of a shared install 
location could be encountered, not sure if I need the original installer, or if 
whatever is in the current /var/lib/dkms and /usr/src directory is enough, I am 
thinking in some cases, every time I want to switch to a different driver version, I 
will have to rebuild from src, but if I can avoid I would like to, and not sure what 
if anything happens the /usr/src directory if I install a newer 
displaylink/evdi or a newer AMDDriver, and I know that nvidias installer uninstalls 
the old driver if there is an older one, not sure though if the src directories are 
removed or not, so looking for a little help here too..  

**Other considerations continued: 
I understand that installs of these drivers, also include libraries that interface 
or work with the drivers/modules which introduces special need for consideration for 
the libraries. The versions of the libraries may have different support between the 
driver versions, however it is extra work to determine exactly how each package 
especially if they are proprietary, decides to handle versioning and learn if it is 
even possible for two versions of driver to work side by side. How the files are 
versioned where they are located, and where the operating system expects them to be 
located also helps determine what we can actually keep a backup of. 
 
**Other considerations continued: 
Debian, ubuntu users may be familiar with Alternatives, which is awesome and I use 
it for a few things like : (gcc,g++,jvac,java)  This allows you to easily switch 
the default libraries a host system sees in the default location using symlinks and 
works good for setting the default gcc,g++,jvac,java, but is not something you can 
use for changing the libraries the graphic windows on your computer are currently 
using to display, thus restoring any sort of backup may require a reboot or best be 
completed once windowing system and graphical display has been exited. (If you're 
doing full system backups, it is okay to back this up as it should be current and 
working with whole system, but if you back up individually and try to restore, 
especially alongside, or over top of an existing main library that accompanies a 
driver, if not exactly the same version, can break things in variety of places. 
Something like a full system backup should be okay though. Something like timeshift 
or timemachine, or even windows restore or other backup methods can restore your 
system to the previous drivers, and is very helpful when a update that breaks 
something is installed.)  
 
**Other considerations continued: 
The case of drivers and libraries for two separate AMD cards same time, drivers part 
is probably pretty easy, the different libraries part may be interesting. The 
locations of the libraries that install with a driver are placed in directories 
according to where they should be to maintain a working system, yet the developers 
sometimes use symlink or other method similar to the debian, ubuntu alternatives by 
placing them in another directory like /usr/run/lib but can be any location in 
theory. If the directories are created with versioning in the directory name, then 
it helps a lot, and should be okay to keep a backup of those, and may even be able 
to use different minor version changes in the libraries with the same driver/module 
just by repointing the main library location to that version. You may already have 
multiple version directories of some libraries on system since they install of new 
version may not have remove the old libraries and instead just repointed to the new 
ones. This helps with backing up the versions of libraries if the developers did 
create the packaged libraries to install with that method of keeping the versions 
apart.  
 
**Other considerations continued: 
Ensuring a library is available in a specific location is not a big deal for all 
libraries especially if they are run in the command line or are application 
specific, there are many different ways we can make libraries visible we can always 
use environment variables, call an application with correct parameters to specify 
the location to use to use. However a library and driver for a gpu is system 
dependent and essential, having the libraries in a well-known location is important. 

 

**Other considerations continued: 

Example of apps where it doesn't matter much where the libraries are placed: 
Pytorch, if I am not mistaken I can give it the location of the libraries and choose 
the driver version when I build it, In the case of blender, blenders graphics 
encoding functions are dependent on what is set in my python environment, but is 
uses GTK for windowing and presenting graphics on my screen  (Screen rendering 
performed on one gpu via which ever one is user by GTK or the windowing system, and 
the math being done on the 3d models do the calculation portion on my system is 
using a completely different gpu, library and driver), and this is one instance that 
shows two different driver can also coexist but have similar jobs. Keep in mind 
though one gpu in this case is nvidia, and the driver gave the option not to install 
the opengl drivers, and I specifically had to add a value in a configuration file to 
tell the display manager and windowing system not to use the nvidia card, and use 
one of the other cards for rendering the window system an output to monitors. 
 
**Other considerations continued: 

Thus for some things, the primary driver installed for video which actually is 
running opengl,egl,webgl, and provides the interface to the display, should be the 
default for either wayland or xserver, and the GTK system or other windowing systems 
should be using that, but the other stuff is configurable for most applications. The 
graphics libraries used by your actual monitor outputs and your windowing system 
should be in the default location thus if you do want and old backup. I am pretty 
sure the above will become more and more less true, and I hope so in the case where 
I want a single gpu with open source drivers for my output and displays, and 
different set for compute, and also the ability to pass through a portion of the 
same card. This abstraction I think will come in the form of more "safe" 
functionality in userland, and I am excited to see how awesome single gpu life can 
be. Until then, best way, is 3 GPU, or 2 GPU and one iGPU. (What every your window 
manager is using for graphics libraries provided by your gpu driver install, 
requires some careful consideration before restoring, so if backing up driver, and 
the libraries that go with it, recommend source code, precompile libraries and 
binaries work, but should ensure to keep the versions and if possible somehow note 
the version of compiler and kernel and architecture, and any switches used to 
configure the build if any.) 

```
