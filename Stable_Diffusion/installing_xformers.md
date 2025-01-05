## Older version of Torch requires and older version of transformers.

```ini
https://huggingface.co/docs/diffusers/en/optimization/xformers
https://github.com/facebookresearch/xformers#installing-xformers
```

***This is the steps I used to compile xformers after installing a version of pytorch that supports either cuda 11.3 or 11.4, and there was only option of 1.12.1-113cuda for pytorch, "torch" as it called in code.

```bash
# (Optional) Makes the build much faster
pip install ninja
# Set TORCH_CUDA_ARCH_LIST if running and building on different GPU types
pip install -v -U git+https://github.com/facebookresearch/xformers.git@main#egg=xformers
# (this can take dozens of minutes)
```

***Takes a little while, it is exciting and scary, downloads a lot of stuff, runs pretty quick on 40 threads micht take a wil on something else, then once complete you should be able to run the below to see your xformers information (My memory is faint, not sure if version of C received any complaints.)***
```bash
python3 -m xformers.info
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
