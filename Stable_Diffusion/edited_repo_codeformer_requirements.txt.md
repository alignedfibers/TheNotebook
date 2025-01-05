## Using older gpu means using CUDA 11.4 which means Stable Diffusion WebUI will not work without specifying the correct version of torch we compiled with the supported version of xformers for torch 1.12 with CUDA 11.3 since 11.3 ships with the torch version and no other options, and will not use my system version even though it exists.

***The main system version of torch and cuda is hard locked to this and will not be changing, once I finally install a better gpu, the global version of torch on the main system will stay as this so that it stays compatible with broad range of devices as long as they still work with Cuda 11.3. Intend that venv is used to setup newer version of torch and run from there for new features.***

```text
addict
future
lmdb
numpy
opencv-python
Pillow
pyyaml
requests
scikit-image
scipy
tb-nightly
torch>=1.12
torchvision
tqdm
yapf
lpips
gdown # supports downloading the large file from Google Drive
# cmake
# dlib
# conda install -c conda-forge dlib
```