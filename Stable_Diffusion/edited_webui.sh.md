## Completed changes to the webui.sh file as it comes default to use the cpu with accelerate.
***With the original config it does not automatically see the secondary gpus that are non-gui or video output. May be wrong about accelerate as I tried to add it but think it was default I found***

### Lines 221-257 end of file at time after I cloneds and changed
```diff
--- webui.sh.backup
+++ webui.sh
@@ -1,24 +1,27 @@
 # Try using TCMalloc on Linux
 prepare_tcmalloc() {
     if [[ "${OSTYPE}" == "linux"* ]] && [[ -z "${NO_TCMALLOC}" ]] && [[ -z "${LD_PRELOAD}" ]]; then
         TCMALLOC="$(PATH=/usr/sbin:$PATH ldconfig -p | grep -Po "libtcmalloc(_minimal|)\.so\.\d" | head -n 1)"
         if [[ ! -z "${TCMALLOC}" ]]; then
             echo "Using TCMalloc: ${TCMALLOC}"
             export LD_PRELOAD="${TCMALLOC}"
         else
             printf "\e[1m\e[31mCannot locate TCMalloc (improves CPU memory usage)\e[0m\n"
         fi
     fi
 }

 KEEP_GOING=1
 export SD_WEBUI_RESTART=tmp/restart
 while [[ "$KEEP_GOING" -eq "1" ]]; do
-    if [[ ! -z "${ACCELERATE}" ]] && [ ${ACCELERATE}="True" ] && [ -x "$(command -v accelerate)" ]; then
+    if [[ ! -z "${ACCELERATE}" ]] && [ ${ACCELERATE}="True" ] && [ -x "$(command -v accelerate)" ]]; then
         printf "\n%s\n" "${delimiter}"
         printf "Accelerating launch.py..."
         printf "\n%s\n" "${delimiter}"
         prepare_tcmalloc
-        accelerate launch --num_cpu_threads_per_process=6 "${LAUNCH_SCRIPT}" "$@"
+        CUDA_VISIBLE_DEVICES="0, 1"
+#        accelerate launch --num_cpu_threads_per_process=6 "${LAUNCH_SCRIPT}" "$@"
+        accelerate launch --multi_gpu --num_machines 1 --gpu_ids 0,1 "${LAUNCH_SCRIPT}" "$@"
     else
         printf "\n%s\n" "${delimiter}"
         printf "Launching launch.py..."
         printf "\n%s\n" "${delimiter}"
         prepare_tcmalloc
         "${python_cmd}" "${LAUNCH_SCRIPT}" "$@"
     fi

     if [[ ! -f tmp/restart ]]; then
         KEEP_GOING=0
     fi
 done
```