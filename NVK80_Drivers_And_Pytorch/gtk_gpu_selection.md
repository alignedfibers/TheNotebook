```bash
#!/bin/bash
#It looks like this only applies if you are using x11 and does not apply if using wayland.

#if you're using NVIDIA GPUs w/ proprietary drivers,set the __GLX_VENDOR_LIBRARY_NAME environment var to nvidia.
export __GLX_VENDOR_LIBRARY_NAME=nvidia

Similarly, for AMD GPUs, you can set the DRI_PRIME environment variable to specify the AMD GPU.
export DRI_PRIME=1

```
