## original note on subject

python3 -m pip install torch==1.12.1+cu113 torchvision==0.13.1+cu113 torchaudio==0.12.1 --extra-index-url https://download.pytorch.org/whl/cu113

pip install transformers==4.19.2 diffusers invisible-watermark

git clone xformers - check the instructions and do the submodules thing.
cd xformers
sudo update-alternatives --config gcc
sudo update-alternatives --config g++
#Requires GCC 10 and G++ 10 for compiling transformers.
#Might require commenting out some parts of the files to allow gcc10, but it is only a warning.
#plus stable diffusion is still using cuda 11.3, why shouldn’t xformers anyways.
pip install -r requirements.txt  —-- Look at the file before doing anything.
pip install -e .
pip show transformers
pip show xformers

#by this point you should have a fork.
https://github.com/AUTOMATIC1111/stable-diffusion-webui.git
git clone stable-diffusion-web-ui - check the instructions and do the submodules thing
cd stable-diffusion-web-ui
gedit webui-user.sh
#Update line for command arguments and add flag –ignore-version-check (I think)
#Add command line arguments –xformers if you are using a card that can be improved with xformers.
#Download the checkpoints.

You need to make wayland work from here:  /usr/lib/udev/rules.d/61-gdm.rules.


IGNORE_CC_MISMATCH > /usr/src/targetdriversrc_version/dkms.conf
DKMS STATUS
Disable any that should not be, and enable any that should be.


MAIN COURSE: Install Automatic1111 WebUI on Ubuntu Linux WSL2
We have to download (clone) Automatic1111 repository from GitHub, use regular user (wsluser) to set it up.
# use regular user (wsluser)
# (you should see something like this: wsluser@DESKTOP-A1111WU:~$ )

# create a1111 folder
cd ~
mkdir a1111
cd /home/wsluser/a1111

# clone stable latest official repository
git clone https://github.com/AUTOMATIC1111/stable-diffusion-webui.git

# go to the cloned repo folder
cd stable-diffusion-webui
Edit the ‘arguments’ parameter to set various conditions and values, such as xformers, medvram, gradio-auth, listen, etc. In Linux, the file is named webui-user.sh, so make the necessary edits in that file. For a comprehensive explanation of each argument, you can refer to the complete wiki here.
To activate the export COMMANDLINE_ARGS= line, uncomment it by removing the ‘#’ sign from the front. Then, add specific arguments to it. For GPUs with 12GB or 8GB of VRAM, use "--xformers" to make it more optimized. If you have a GPU with less than that, use "--xformers --medvram" for 6GB of VRAM, and "--xformers --lowvram" for 4GB of VRAM.
# edit args parameters to run it better
nano webui-user.sh

###find-for-this-line###
# Commandline arguments for webui.py, for example: export COMMANDLINE_ARGS="--medvram --opt-split-attention"
export COMMANDLINE_ARGS="--xformers"
###find-for-this-line###

# dont forget to save file in nano
# (Exit Ctrl+X, then Y for Yes Exit and Save, then Enter to confirm exit)
Let’s create a simple script to automate the clean-up process after exiting the WebUI server. The purpose is that when we press Ctrl+C to exit on the console terminal, it will trigger the clean-up of the /tmp folder, which contains numerous temporary generated images from Stable Diffusion Automatic1111 WebUI.






