## Added a script with the commands to set the GPU visible env var and run ./webui.sh port 7860
***Requires that the end of webui.sh is changed to see the GPU 0,1 -- This is usefull since you can run with the CUDA_VISIBLE_DEVICES as a different device in two seperate bash scripts allowing both gpus to be used but directly with one of the gpu devices*** 

```bash
#!/bin/bash
export CUDA_VISIBLE_DEVICES=0; ./webui.sh  --port 7860
```

***Future I might have multiple "virtual gpus" available from one, but depends how I approach sharing a gpu if a get a newer one.***