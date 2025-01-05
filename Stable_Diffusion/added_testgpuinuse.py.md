## Quick Test if GPU and CUDA are available. Needs improved to show GPU 0,1,2 avail.

```bash
import torch

if torch.cuda.is_available():
    device = torch.device("cuda")
    print("GPU is available and being used")
else:
    device = torch.device("cpu")
    print("GPU is not available, using CPU instead")
```