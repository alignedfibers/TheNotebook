## Added print statements to repositories/stable-diffusion-stability-ai/diffusionmodules/model.py and repositories/generative-models/sgm/modules/attention.py -- this research was conclusive that the nv driver 470.239.06 plus cuda 11.3 does not support the features for tensor cores, and although xformers was properly compiled against pytorch 1.12.1+cu113 (for clarity xformers is facebook ai attention optimizations, memory optimizations and other optimizations) xFormers 0.0.22+a24a6e3.d20230821 uing the latest C compiler version it supports, it seems to load and has features listed the xformers can use with the K80 per the xformer.info when I run it, but seems the loading and unloading of it gets stuck somewhere giving errors, and if I correct the loading issue, then stable diffusion ai give error that tensor core support does not exist, but in conclusion xformer optimizations still can be used, but the area in the code where the optimizations are enable will need a switch statement detecting support and only enable the needed optimizations, since xformers only optimizes in a later pass it seemed since it fails midway through generating a single image after it is mostly done. It is in theory the older card will still be supported, and the value of the K80s will go back up once I am done, in fact I have them working and can make them work but not with all the new features, and since the k80 has sufficient vram they still seem to be competitive with other cards in the AI image generation area though it cannot use new memory management features the latest cards can, and does not have tensor cores. Thus, I could patch the main code base or do a pull to conditionally handle that GPU accordingly, but require a special flag you need to set so it never tries and just uses the normal unless your trying to do "older card / driver / pytorch / cuda" compatibility after all I removed the steps to force load the xformers, and restored the logical portion of the code to the way it was before, but left the debugging in it as shown below, in order to force it to load, you have to unload it before you load it in model.py by setting it to none under sys.modules['xformers'] any further research into this may find that xformers requires that blocksparse and will compile and build against cuda 11.4 which offers this, but if trying with a card that does not support it likely kicks back a not supported error where ever it is called because the GPU or Driver does not support it, and without detailed knowledge on the xformer codebase in addition to the stability-ai codebase, it is hard to know for sure if any optimizations would be able to be used from xformers without this, or if using any of the other features shown as available at the end of this reading showing the xformers.info that built and loads supported features will beneficial above and beyond the doggettx method currently in use. With direct advisement from someone on that, I would back out my looking at it further, but as stated I still have questions. If nothing else, I could find a way to improve the readability of the code and xformer handling especially in a multi-project type scenerio where there are a mix of different projects all as submodules under a web interface in this case just to make everyone talk with eachother better. The fact they all load python modules onto the python runtime and share it, is at least one cause to kinda know what eachother are doing.

***Reason is because it wraps a try/exception around the import inside of generative-models and inside of stability-ai both, and has the same error print message and was hard to determine where it was errored from.***

***attention.py lines 1-86 after my changes
```python
import math
from inspect import isfunction
from typing import Any, Optional

import sys
import traceback
import importlib.util

import torch
import torch.nn.functional as F
from einops import rearrange, repeat
from packaging import version
from torch import nn

print("========== After All to force Xformers to load with CUDA 11.3 it errors while it is running then =============")
print("Re-enabled the import check on it, and some how it tests and stops it loading, but not sure exactly where")
print("Runtime Error gets this with the K80 and CUDA 11.3 - Blocksparse is not available: the current GPU does not expose Tensor cores████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 30/30")

if version.parse(torch.__version__) >= version.parse("2.0.0"):
    SDP_IS_AVAILABLE = True
    from torch.backends.cuda import SDPBackend, sdp_kernel

    BACKEND_MAP = {
        SDPBackend.MATH: {
            "enable_math": True,
            "enable_flash": False,
            "enable_mem_efficient": False,
        },
        SDPBackend.FLASH_ATTENTION: {
            "enable_math": False,
            "enable_flash": True,
            "enable_mem_efficient": False,
        },
        SDPBackend.EFFICIENT_ATTENTION: {
            "enable_math": False,
            "enable_flash": False,
            "enable_mem_efficient": True,
        },
        None: {"enable_math": True, "enable_flash": True, "enable_mem_efficient": True},
    }
else:
    from contextlib import nullcontext

    SDP_IS_AVAILABLE = False
    sdp_kernel = nullcontext
    BACKEND_MAP = {}
    print(
        f"No SDP backend available, likely because you are running in pytorch versions < 2.0. In fact, "
        f"you are using PyTorch {torch.__version__}. You might want to consider upgrading."
    )

def line_info():
    # `f_back` to go one frame up, so we're getting the caller's line number
    # `__file__` is the filename of this script
    return f"(File: {__file__}, Line: {sys._getframe(1).f_lineno})"

print(f"==== Before the try wrapping the import xformers ==== {line_info()}")
try:
    print(f"==== Inside the try block for xformers import ==== {line_info()}")
    import xformers
    import xformers.ops
    import xformers.info
    XFORMERS_IS_AVAILABLE = True
except Exception as e:
    print(f"==== Exception occurred while importing xformers ==== {line_info()}")
    #print(f"Exception: {e}")
    traceback.print_exc()
    print(f"No module 'xformers'. Processing without it... {line_info()}")
    XFORMERS_IS_AVAILABLE = False

print(f"==== After the try/except wrapping the import xformers ==== {line_info()}")
spec = importlib.util.find_spec("xformers")
if spec is not None:
    print(f"==== xformers is found at:", spec.origin ,f"==== {line_info()}")
else:
    print(f"==== xformers not found by importlib. ==== {line_info()}")

if "xformers" in sys.modules:
    print(f"==== xformers is in sys.modules: sys.modules['xformers'] = {sys.modules['xformers']} ==== {line_info()}")
    #print(f"sys.modules['xformers'] = {sys.modules['xformers']} ==== {line_info()}")
else:
    print(f"==== xformers is NOT in sys.modules at all. ==== {line_info()}")

xformers.info.print_info()

from .diffusionmodules.util import checkpoint
```


***model.py lines 1-43 after my changes.
```python
# pytorch_diffusion + derived encoder decoder
import math

import sys
import traceback
import importlib.util

import torch
import torch.nn as nn
import numpy as np
from einops import rearrange
from typing import Optional, Any

from ldm.modules.attention import MemoryEfficientCrossAttention

def line_info():
    # `f_back` to go one frame up, so we're getting the caller's line number
    # `__file__` is the filename of this script
    return f"(File: {__file__}, Line: {sys._getframe(1).f_lineno})"

spec = importlib.util.find_spec("xformers")
if spec is not None:
    print(f"xformers is found at:", spec.origin ,f"==== {line_info()}")
else:
    print(f"xformers not found by importlib. ==== {line_info()}")

if "xformers" in sys.modules:
    print(f"xformers is in sys.modules:")
    print(f"sys.modules['xformers'] = {sys.modules['xformers']} ==== {line_info()}")
else:
    print(f"xformers is NOT in sys.modules at all. ==== {line_info()}")

print(f"==== Before the try wrapping the import xformers ==== {line_info()}")
try:
    print(f"==== Inside the try block for xformers import ==== {line_info()}")
    import xformers
    import xformers.ops
    XFORMERS_IS_AVAILBLE = True
except Exception as e:
    print(f"==== Exception occurred while importing xformers ==== {line_info()}")
    traceback.print_exc()
    print(f"No module 'xformers'. Processing without it... {line_info()}")
    XFORMERS_IS_AVAILBLE = False
```
***xformers.info output**
```bash
shawnstark@visual-headsup-jammy:~$ python3 -m xformers.info
Blocksparse is not available: the current GPU does not expose Tensor cores
xFormers 0.0.22+a24a6e3.d20230821
memory_efficient_attention.cutlassF:               available
memory_efficient_attention.cutlassB:               available
memory_efficient_attention.decoderF:               available
memory_efficient_attention.flshattF@v2.0.8:        available
memory_efficient_attention.flshattB@v2.0.8:        available
memory_efficient_attention.smallkF:                available
memory_efficient_attention.smallkB:                available
memory_efficient_attention.tritonflashattF:        unavailable
memory_efficient_attention.tritonflashattB:        unavailable
indexing.scaled_index_addF:                        available
indexing.scaled_index_addB:                        available
indexing.index_select:                             available
swiglu.dual_gemm_silu:                             available
swiglu.gemm_fused_operand_sum:                     available
swiglu.fused.p.cpp:                                available
is_triton_available:                               True
is_functorch_available:                            False
pytorch.version:                                   1.12.1+cu113
pytorch.cuda:                                      available
gpu.compute_capability:                            3.7
gpu.name:                                          Tesla K80
build.info:                                        available
build.cuda_version:                                1104
build.python_version:                              3.10.12
build.torch_version:                               1.12.1+cu113
build.env.TORCH_CUDA_ARCH_LIST:                    None
build.env.XFORMERS_BUILD_TYPE:                     None
build.env.XFORMERS_ENABLE_DEBUG_ASSERTIONS:        None
build.env.NVCC_FLAGS:                              None
build.env.XFORMERS_PACKAGE_FROM:                   None
build.nvcc_version:                                11.4.100
source.privacy:                                    open source
```