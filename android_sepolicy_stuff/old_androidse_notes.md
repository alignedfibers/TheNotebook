 **SE Linux for Android Tools** 
---

# How to Examine Android SELinux Policy

**Published: Mon 15 August 2016 in Cookbook**

---

### In this article:

1. Build a clean environment
2. Fetch Android source code
3. Compile and install Android’s SELinux toolset and libraries
4. Supplementary tools
5. Conclusion

---

## Build a Clean Environment

### Environment Properties

- **Virtual Machine**: Use a 64-bit system, preferably VMware for better USB device handling. Qemu doesn’t handle USB connections well. 
- **Ubuntu**: Recommended version is Ubuntu 14.04 (Trusty) for Android 6.0. You can use Xubuntu for a lighter desktop environment.
- **Disk Space**: At least 50GB for SELinux-related tasks, and 100GB for a full Android build.
- **CPU and Memory**: For building, CPU and memory mainly affect time. Ideally, use 2-4 cores and 4GB of RAM.

### Environment Installation Procedure

1. Download the Ubuntu/Xubuntu 14.04 netboot installer [here](http://archive.ubuntu.com/ubuntu/dists/trusty/main/installer-amd64/current/images/netboot).
2. Skip VMware's “Easy Install” by selecting **“I will install the operating system later”**.
3. Set the guest OS as **Linux** -> **Ubuntu 64-bit**.
4. The virtual machine will need:
   - **40GB disk space** minimum (source code takes more than 20GB).
   - **2048MB RAM** or higher (4096MB recommended).
   - **At least 2 CPU cores**.
   - Set the **USB to 2.0** and disable automatic USB connection/sharing.
5. Ensure you do not upgrade Ubuntu on the first boot.

Once installed, install the guest tools:

```bash
sudo apt-get install open-vm-tools
```

Restart to complete installation.

---

## Fetch Android Source Code

1. Choose a ROM:
   - **CyanogenMod**: [Find your device here](https://wiki.cyanogenmod.org/w/Devices), select the vendor, and follow the “How to build CyanogenMod” link.
   - **AOSP**: [Follow this guide](https://source.android.com/source/initializing.html).
   
2. CyanogenMod includes a tool to unpack `boot.img` files, useful for accessing the `sepolicy` file. Google’s AOSP doesn’t provide this tool.

### Example for CyanogenMod 13.0 (Android 6.0):

```bash
sudo apt-get install bison build-essential curl flex git gnupg gperf \
libesd0-dev liblz4-tool libncurses5-dev libsdl1.2-dev libwxgtk2.8-dev libxml2 \
libxml2-utils lzop maven openjdk-7-jdk pngcrush schedtool squashfs-tools \
xsltproc zip zlib1g-dev g++-multilib gcc-multilib lib32ncurses5-dev \
lib32readline-gplv2-dev lib32z1-dev

mkdir -p ~/bin
mkdir -p ~/android/system
PATH=~/bin:$PATH
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
chmod u+x ~/bin/repo
cd ~/android/system/
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
repo init -u https://github.com/CyanogenMod/android.git -b cm-13.0
repo sync
source ./build/envsetup.sh
breakfast
```

---

## Compile and Install Android’s SELinux Toolset and Libraries

Now it's time to compile the SELinux libraries and tools.

### Step-by-step Procedure

1. Install dependencies:

```bash
sudo apt-get install libapol-dev libaudit-dev libdbus-glib-1-dev libgtk2.0-dev \
libustr-dev python-dev python-networkx swig xmlto
```

2. Set the `ANDROID_BUILD_TOP` environment variable to your source directory:

```bash
ANDROID_BUILD_TOP=~/android/system
```

3. Compile and install various components:

```bash
cd $ANDROID_BUILD_TOP/external/bzip2/
make -f Makefile-libbz2_so
sudo make install

cd $ANDROID_BUILD_TOP/external/libcap-ng/libcap-ng-0.7/
./configure
make
sudo make install

cd $ANDROID_BUILD_TOP/external/selinux/
make -C ./libsepol/
sudo make -C ./libsepol/ install

EMFLAGS=-fPIC make -C ./libselinux/
sudo make -C ./libselinux/ install

make -C ./libsemanage/
sudo make -C ./libsemanage/ install

make
sudo make install
make swigify
sudo make install-pywrap
sudo cp ./checkpolicy/test/{dispol,dismod} /usr/bin/
```

### Generic Procedure

If the procedure fails at any point, follow these steps to troubleshoot:

1. **Search for missing headers** in Android’s external directory:

```bash
find $ANDROID_BUILD_TOP/external -name filename.h
```

2. **Search system-wide**:

```bash
find / -name filename.h 2>/dev/null
```

3. **Search package system** for missing development packages:

```bash
apt-cache search filename-dev
```

4. Use **apt-file** to search for uninstalled files:

```bash
sudo apt-get install apt-file
sudo apt-file update
apt-file search filename.h | grep -w filename.h
```

---

## Supplementary Tools

Now that you can inspect SELinux policies, you might want to modify them. The `sepolicy` file cannot be directly replaced, as it’s part of the boot image. You’ll need to:

1. **Modify the boot image**.
2. **Use the `sepolicy-inject` tool** to modify binary policy.

To install `sepolicy-inject`:

```bash
cd ~/android/
git clone https://bitbucket.org/joshua_brindle/sepolicy-inject.git
cd ./sepolicy-inject/
LIBDIR=/usr/lib make
sudo cp ./sepolicy-inject /usr/bin/
```

### Example

To inject a permission:

```bash
sepolicy-inject -s kernel -t media_rw_data_file -c file -p read -P ./sepolicy
```

---

## Conclusion

After following these steps, you should have a fully functional environment for investigating and modifying Android SELinux policies. You can inspect policies, make changes, and repackage the boot image for deployment.

For further details and updates, check out [the full guide](https://www.whitewinterwolf.com/posts/2016/08/15/examine-android-selinux-policy/).

---
