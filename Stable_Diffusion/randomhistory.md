## I know it is nuts I am keeping this anywhere right, stuff here helps though.
***I am going through and looking at what all I should specify for my pytorch compilation on my own with cuda support and since cuda 11.4 looks like we need to ensure no ABI mismatches between the two but the python3 binary can be on the latest gcc11.4 for me and not be an issue, and the version of pytorch I have now built with gcc9.6 is in same abi with gcc10 so it works with cuda11 and thats got to be in a table somewhere, anyhow, looked at history,lddconfig and strings, and other commands helped today, I know I had to do some special digging around and setting up mya alternatives,and creating the cudnn_samples folder which require gcc10 just because the code does not support newer gcc, and yeah, I have some history files I went through some of this is good archive for me and pretty important as i build another script to compile my torch libraries jax libraries and xformers libraries directly and support up to what I have instead of a little bit older. Since cuda 11.4 is suppose to work with compute capability up to 8 meaning Ampere arch, this is still staying as my base, but with downside that possibly still newer drivers for some hw may not support cuda 11.4 but from what I read everyone is supported (for the most part) but that could be wrong and no driver issue encounter but my memory of what I have read into time ago begs to differ and probably 11.4 for these compute compatibilities which I have to list in my build variables might not work with all drivers.***


```bash
 1178  cd /usr/src/cudnn_samples_v8/
 1179  ls
 1180  cd conv_sample/
 1181  ls
 1182  make clean & make
 1183  sudo bash
 1184  history | grep ldconfig
 1185  ldconfig -p | grep cuda
 1186  ldconfig -p | grep libcudnn
 1187  ldconfig -p | grep libcuda
 1188  ls -al /lib/x86_64-linux-gnu/ | grep libcuda
 1189  ls -al /lib/i386-linux-gnu/ | grep libcuda
 1190  cd /lib/i386-linux-gnu/
 1191  ln -sfn libcuda.so.1 libcuda.so.470.199.02 
 1192  sudo ln -sfn libcuda.so.1 libcuda.so.470.199.02 
 1193  ls -al /lib/i386-linux-gnu/ | grep libcuda
 1194  ls -l
 1195  ls -l | grep cuda
 1196  rm libcuda.so.470.199.02 
 1197  sudo rm libcuda.so.470.199.02 
 1198  ls -al /lib/i386-linux-gnu/ | grep libcuda
 1199  sudo bash
 1200  cd /usr/src/cudnn_samples_v8/
 1201  ls
 1202  cd conv_sample/
 1203  ls
 1204  mv conv_sample conv_sample_old
 1205  sudo mv conv_sample conv_sample_old
 1206  sudo bash
 1207  echo $LD_LIBRARY_PATH
 1208  export LD_LIBRARY_PATH=$LD_LIBRARY_PATH/lib/x86_64-linux-gnu/
 1209  echo $LD_LIBRARY_PATH
 1210  ls
 1211  ./conv_sample
 1212  ls -al /lib/x86_64-linux-gnu/ | grep libcudnn_ops
 1213  export LD_LIBRARY_PATH=$LD_LIBRARY_PATH/lib/x86_64-linux-gnu/libcudnn_ops_infer.so.8
 1214  ./conv_sample
 1215  ldconfig -p | grep cuda
 1216  ldconfig -p | grep libcuda
 1217  cd /lib/x86_64-linux-gnu/
 1218  ls -al | grep libcuda
 1219  ln -sfn libcuda.so.1 libcuda.so
 1220  sudo ln -sfn libcuda.so.1 libcuda.so
 1221  cd /usr/src/cudnn_samples_v8/conv_sample/
 1222  ls
 1223  ./conv_sample

```

## another random history

```bash
1448  nvcc -V
 1449  which nvcc
 1450  history | grep nvcc
 1451  cd /usr/local/cuda/
 1452  ls
 1453  cd lib64/
 1454  ls
 1455  ls | grep libcud
 1456  cd ../
 1457  ls
 1458  cd include
 1459  ls
 1460  ls | grep cudnn
 1461  history | grep cudnn
 1462  cd /opt/
 1463  ls
 1464  cd nvidia/
 1465  ls
 1466  cd cudnn/
 1467  ls
 1468  apt-files list libcudnn8-samples_8.2.4.15-1+cuda11.4_amd64.deb
 1469  apt-file list libcudnn8-samples_8.2.4.15-1+cuda11.4_amd64.deb
 1470  ls
 1471  ldd libcudnn_*

```

## Another random history

```bash
1448  nvcc -V
 1449  which nvcc
 1450  history | grep nvcc
 1451  cd /usr/local/cuda/
 1452  ls
 1453  cd lib64/
 1454  ls
 1455  ls | grep libcud
 1456  cd ../
 1457  ls
 1458  cd include
 1459  ls
 1460  ls | grep cudnn
 1461  history | grep cudnn
 1462  cd /opt/
 1463  ls
 1464  cd nvidia/
 1465  ls
 1466  cd cudnn/
 1467  ls
 1468  apt-files list libcudnn8-samples_8.2.4.15-1+cuda11.4_amd64.deb
 1469  apt-file list libcudnn8-samples_8.2.4.15-1+cuda11.4_amd64.deb
 1470  ls
 1471  ldd libcudnn_*

```

## Another random history
```bash
1019  pip show cudatoolkit
 1020  pip show transformers
 1021  cd ../
 1022  ls
 1023  cd stable_diffusion/stable-diffusion-webui/
 1024  ls
 1025  ./webui.sh 
 1026  rm -fr venv/
 1027  history | grep venv
 1028  pip install cudatoolkit=11.4
 1029  history | grep pip
 1030  pip install cudatoolkit==11.4
 1031  print(torch.version.cuda)
 1032  print torch.version.cuda
 1033  python
 1034  python3
 1035  ls
 1036  ls modules/
 1037  cd /usr/src/cudnn_samples_v8/
 1038  ls
 1039  cd conv_sample/
 1040  ls
 1041  make clean & make
 1042  sudo bash
 1043  history | grep ldconfig
 1044  ldconfig -p | grep cuda
 1045  ldconfig -p | grep libcudnn
 1046  ldconfig -p | grep libcuda
 1047  ls -al /lib/x86_64-linux-gnu/ | grep libcuda
 1048  ls -al /lib/i386-linux-gnu/ | grep libcuda
 1049  cd /lib/i386-linux-gnu/
 1050  ln -sfn libcuda.so.1 libcuda.so.470.199.02 
 1051  sudo ln -sfn libcuda.so.1 libcuda.so.470.199.02 
 1052  ls -al /lib/i386-linux-gnu/ | grep libcuda
 1053  ls -l
 1054  ls -l | grep cuda
 1055  rm libcuda.so.470.199.02 
 1056  sudo rm libcuda.so.470.199.02 
 1057  ls -al /lib/i386-linux-gnu/ | grep libcuda
 1058  sudo bash
 1059  cd /usr/src/cudnn_samples_v8/
 1060  ls
 1061  cd conv_sample/
 1062  ls
 1063  mv conv_sample conv_sample_old
 1064  sudo mv conv_sample conv_sample_old
 1065  sudo bash
 1066  echo $LD_LIBRARY_PATH
 1067  export LD_LIBRARY_PATH=$LD_LIBRARY_PATH/lib/x86_64-linux-gnu/
 1068  echo $LD_LIBRARY_PATH
 1069  ls
 1070  ./conv_sample
 1071  ls -al /lib/x86_64-linux-gnu/ | grep libcudnn_ops
 1072  export LD_LIBRARY_PATH=$LD_LIBRARY_PATH/lib/x86_64-linux-gnu/libcudnn_ops_infer.so.8
 1073  ./conv_sample
 1074  ldconfig -p | grep cuda
 1075  ldconfig -p | grep libcuda
 1076  cd /lib/x86_64-linux-gnu/
 1077  ls -al | grep libcuda
 1078  ln -sfn libcuda.so.1 libcuda.so
 1079  sudo ln -sfn libcuda.so.1 libcuda.so
 1080  cd /usr/src/cudnn_samples_v8/conv_sample/
 1081  ls
 1082  ./conv_sample
 1083  cd /genvol/stable_diffusion/stable-diffusion-webui/
 1084  ls
 1085  history | grep venv
 1086  python3 -c 'import venv'
 1087  python3 -m venv venv/
 1088  clear
 1089  ls
 1090  ./webui.sh 
 1091  ls
 1092  ls outputs/
 1093  ls log
 1094  ls log/images/
 1095  gedit log/images/log.csv 
 1096  ls log/images/
 1097  ls
 1098  ls __pycache__/
 1099  ls venv/
 1100  ls configs/
 1101  ls test
 1102  ls outputs/
 1103  ls outputs/txt2img-images/
 1104  ls outputs/txt2img-images/2023-08-23
 1105  ./webui.sh 
 1106  ./webui.sh
 1107  rm -fR venv/
 1108  pip show xformers
 1109  history | grep venv
 1110  python3 -c 'import venv'
 1111  python3 -m venv venv/
 1112  source venv/bin/activate
 1113  ./webui.sh
 1114  source bin/deactivate
 1115  source venv/bin/deactivate
 1116  rm venv/
 1117  deactivate
 1118  rm venv/
 1119  rm -fr venv/
 1120  ./webui.sh
 1121  history | grep import
 1122  python3 -c 'import xformers'
 1123  ./webui.sh
 1124  history | grep import
 1125  python3 -c 'import xformers'
 1126  history | grep python3 -c
 1127  history | grep "python3 -c"
 1128  clear
 1129  ./webui.sh
 1130  python3 -c 'import xformers'
 1131  ./webui.sh
 1132  history | grep xformers
 1133  pip show xformers
 1134  cd /genvol/stable_diffusion/stable-diffusion-webui/
 1135  pip show xformers
 1136  python3 -c 'import xformers'
 1137  ./webui.sh
 1138  python3 -c 'import torch'
 1139  python3 -c 'import xformers'
 1140  ./webui.sh
 1141  python3 -c 'import torch'
 1142  python3 -c 'import xformers'
 1143  ./webui.sh
 1144  cd /genvol/stable_diffusion/stable-diffusion-webui/
 1145  ls
 1146  gedit webui-user.sh 
 1147  nano webui-user.sh 
 1148  sudo apt purge gedit
 1149  apt install gedit
 1150  sudo apt install --reinstall gedit
 1151  nvtop
 1152  nvcc --version
 1153  history | grep smi
 1154  watch -n 0.5 nvidia-smi
 1155  history | grep pip
 1156  history | grep clone
 1157  history grep xformers
 1158  history | grep xformers
 1159  history | grep 1846
 1160  history | grep 1847
 1161  pip show cudatoolkit
 1162  code
 1163  snap install code
 1164  cd /genvol/
 1165  cd stable_diffusion/stable-diffusion-webui/
 1166  ls
 1167  cd models/
 1168  ls
 1169  cd Stable-diffusion/
 1170  ls
 1171  mv v1-5-pruned-emaonly.ckpt v1-5-pruned-emaonly.ckpt.backup
 1172  wget https://cdn-lfs.huggingface.co/repos/6b/20/6b201da5f0f5c60524535ebb7deac2eef68605655d3bbacfee9cce0087f3b3f5/cc6cb27103417325ff94f52b7a5d2dde45a7515b25c255d8e396c90014281516?response-content-disposition=attachment%3B+filename*%3DUTF-8%27%27v1-5-pruned-emaonly.ckpt%3B+filename%3D%22v1-5-pruned-emaonly.ckpt%22%3B&Expires=1693532256&Policy=eyJTdGF0ZW1lbnQiOlt7IkNvbmRpdGlvbiI6eyJEYXRlTGVzc1RoYW4iOnsiQVdTOkVwb2NoVGltZSI6MTY5MzUzMjI1Nn19LCJSZXNvdXJjZSI6Imh0dHBzOi8vY2RuLWxmcy5odWdnaW5nZmFjZS5jby9yZXBvcy82Yi8yMC82YjIwMWRhNWYwZjVjNjA1MjQ1MzVlYmI3ZGVhYzJlZWY2ODYwNTY1NWQzYmJhY2ZlZTljY2UwMDg3ZjNiM2Y1L2NjNmNiMjcxMDM0MTczMjVmZjk0ZjUyYjdhNWQyZGRlNDVhNzUxNWIyNWMyNTVkOGUzOTZjOTAwMTQyODE1MTY%7EcmVzcG9uc2UtY29udGVudC1kaXNwb3NpdGlvbj0qIn1dfQ__&Signature=nSs9cOjRwnXBYSsfTuuu5Ntu-pG%7EyhTbT7WF8WGUl6XklsnLXxXx3JapARfdHi1OucFUN7qEvKS1C4snR-%7EGOJpo%7EZe%7EathrAQv5GnFuncBsXjD7gZNDGXAN6sZpjYPhaJv3Ddbf26iytYuwTqVXyqmQNjMuXv6Mb8Th8lFrthJaZyRyUHXfZAe8PWIIzubPGvmsC9ADC16rusi4S6GWnfCmWThCEIViCR3OSPMqZR9JvfKU1Bd0Gu0RBauO0g%7E68o-eElQ%7Ea4bPZmyzzFjTLJQo%7Ez8y6NWV6E7gGyKdJBVr8UvIc9h9IR4O3vMxe1M1aFAZdjfLWYuH7hv8MKs0PA__&Key-Pair-Id=KVTP0A1DKRTAX
 1173  ls
 1174  rm wget-log 
 1175  ls
 1176  echo $XDG_CONFIG_HOME
 1177  touch README.txt
 1178  gedit README.txt 
 1179  cd ../../
 1180  cd ../
 1181  pip install opencv-python
 1182  pip install numpy
 1183  history | grep blender
 1184  sudo bash
 1185  blender --version
 1186  man blender 
 1187  echo $BLENDER_SYSTEM_PYTHON 
 1188  blender --help
 1189  which blender
 1190  gedit /usr/bin/blender
 1191  cd /bin/
 1192  ls | grep python
 1193  cd /usr/bin/
 1194  ls | grep python
 1195  python --version
 1196  python3 --version
 1197  ls -al | grep python
 1198  clear
 1199  history | grep "pip"
 1200  history | grep show
 1201  python3 -m pip show numpy
 1202  ls
 1203  cd ../
 1204  cd lib
 1205  ls
 1206  sudo blender
 1207  blender --help
 1208  clear
 1209  blender --python-use-system-env
 1210  sudo bash
 1211  blender --python-use-system-env
 1212  touch README.txt
 1213  gedit README.txt 
 1214  touch steps.py
 1215  gedit steps.py 
 1216  cd /genvol/stable_diffusion/stable-diffusion-webui/
 1217  ls
 1218  ./webui.sh
 1219  pip install accelerate
 1220  accelerate config
 1221  ./webui.sh
 1222  ls
 1223  gedit webui.user.sh
 1224  gedit webui-user.sh 
 1225  ./webui.sh
 1226  ls
 1227  cd repositories/
 1228  ls
 1229  cd CodeFormer/
 1230  ls
 1231  gedit requirements.txt 
 1232  pip install addict
 1233  ls
 1234  cd scripts/
 1235  ls
 1236  cd ../
 1237  ls
 1238  cd inputs/
 1239  ls
 1240  cd whole_imgs/
 1241  ls
 1242  phot 06.png 
 1243  phot0 06.png 
 1244  photo 06.png 
 1245  photoviewer 06.png 
 1246  shotwell 06.png 
 1247  ls
 1248  shotwell 00.jpg 
 1249  cd ../
 1250  ls
 1251  cd ../
 1252  ls
 1253  cd basicsr/
 1254  ls
 1255  cd ../
 1256  ls
 1257  cd generative-models/
 1258  ls
 1259  cd ../
 1260  ls
 1261  cd k-diffusion/
 1262  ls
 1263  cd ../
 1264  ls
 1265  cd stable-diffusion-stability-ai/
 1266  ls
 1267  cd ldm/
 1268  ls
 1269  cd ../
 1270  ls
 1271  cd ../
 1272  ls
 1273  mkdir gfpgan
 1274  cd gfpgan
 1275  cd /genvol
 1276  ls
 1277  mkdir gfpgan
 1278  cd gfpgan
 1279  history | grep "python3"
 1280  python3 -m pip install basicsr
 1281  python3 -m pip show torch
 1282  python3 -m pip show torchsde
 1283  python3 -m pip install trampoline
 1284  python3 -m pip install boltons
 1285  python3 -m pip install facexlib
 1286  git clone https://github.com/TencentARC/GFPGAN.git
 1287  ls
 1288  cd GFPGAN
 1289  ls
 1290  gedit requirements.txt 
 1291  python3 -m pip show basicsr
 1292  python3 -m pip show facexlib
 1293  ls
 1294  gedit setup.py 
 1295  python3 -m pip show torch
 1296  ls
 1297  gedit setup.cfg 
 1298  python3 -m pip install lmdb tb-nightly tqdm yapf pyyaml scipy
 1299  ls
 1300  cd options
 1301  ls
 1302  cd ../
 1303  ls
 1304  cat VERSION 
 1305  cat README.md 
 1306  python3 -m pip install realesrgan
 1307  clear
 1308  python3 setup.py develop
 1309  python3 -m pip install gfpgan
 1310  pip show gfpgan
 1311  cd ../
 1312  mkdir nodejsclient
 1313  cd nodejsclient/
 1314  ls
 1315  npm install replicate
 1316  sudo apt install npm
 1317  npm install replicate
  1318  export REPLICATE_API_TOKEN=

```

## Another random bash history
```bash
 1320  touch apikey
 1321  gedit apikey 
 1322  gedit package.json 
 1323  cd ../
 1324  mkdir replicate-nodejs
 1325  cd replicate-nodejs/
 1326  echo "{\"type\": \"module\"}" > package.json
 1327  touch index.js
 1328  npm install replicate
 1329  gedit index.js 
 1330  node index.js
 1331  gedit index.js 
 1332  node index.js
 1333  gedit package
 1334  ls
 1335  gedit package.json 
 1336  node index.js
 1337  ls
 1338  cd node_modules/
 1339  ls
 1340  cd replicate
 1341  ls
 1342  gedit package.json 
 1343  ls
 1344  gedit index.js 
 1345  cd ../
 1346  node index.js 
 1347  ls
 1348  node index.js 
 1349  history | grep npm
 1350  apt purge npm
 1351  sudo apt purge npm
 1352  cd ../
 1353  ls
 1354  rm replicate-nodejs/
 1355  rm -fR replicate-nodejs/
 1356  rm -fR nodejsclient/
 1357  ls
 1358  cd ../
 1359  cd stable_diffusion/stable-diffusion-webui/
 1360  clear
 1361  ./webui.sh
 1362  clear
 1363  ./webui.sh
 1364  clear
 1365  history | grep blender
 1366  blender --python-use-system-env
 1367  mount | grep timeshift
 1368  mount | grep nbd0p1
 1369  history | grep glass
 1370  looking-glass-client win:fullScreen opengl:amdpinnedmem=1
 1371  history | grep rescue
 1372  sudo bash
 1373  history | grep blender
 1374  blender --python-use-system-env
 1375  cd /genvol/stable_diffusion/stable-diffusion-webui/
 1376  ./webui.sh
 1377  history | grep glass
 1378  looking-glass-client win:fullScreen opengl:amdpinnedmem=1
 1379  sudo timeshift --create --comments "Set last create time to 10:15am CST" --tags D
 1380  looking-glass-client win:fullScreen opengl:amdpinnedmem=1
 1381  cd /genvol/stable_diffusion/stable-diffusion-webui/
 1382  clear
 1383  ls
 1384  clear
 1385  history | grep web
 1386  clear
 1387  ./webui.sh
 1388  traceroute speedtest.zscaler.com
 1389  sudo bash
 1390  history | grep extension
 1391  gnome-extensions
 1392  exit
 1393  sudo bash
 1394  ping 1.0.0.1
 1395  ping 1.1.1.3
 1396  sudo bash
 1397  exit
 1398  history | grep smu
 1399  history | grep smi
 1400  watch -n 0.5 nvidia-smi
 1401  cd /genvol/stable_diffusion/stable-diffusion-webui/
 1402  ls
 1403  history | grep ui
 1404  clear
 1405  ./webui.sh
 1406  history | grep glass
 1407  clear
 1408  looking-glass-client win:fullScreen opengl:amdpinnedmem=1
 1409  history | grep resolv
 1410  sudo bash
 1411  exit
 1412  cd /genvol/stable_diffusion/stable-diffusion-webui/
 1413  ./webui.sh
 1414  sudo bash
 1415  exit
 1416  exit
 1417  looking-glass-client win:fullScreen opengl:amdpinnedmem=1
 1418  watch -n 0.5 nvidia-smi
 1419  looking-glass-client win:fullScreen opengl:amdpinnedmem=1
 1420  sudo bash
 1421  exit
 1422  ifconfig
 1423  looking-glass-client win:fullScreen opengl:amdpinnedmem=1
 1424  history | grep sensors
 1425  clear
 1426  watch sensors
 1427  watch -n 0.5 nvidia-smi
 1428  watch -n 0.5 nvidia-smi
 1429  sudo bash
 1430  history | grep cudnn
 1431  cd /usr/src/cudnn_samples_v8/
 1432  ls
 1433  cd conv_sample/
 1434  ls
 1435  ./conv_sample
 1436  ls
 1437  cd ../
 1438  ls
 1439  cd ../
 1440  ls
 1441  mkdir nvidia_opencl_samples
 1442  sudo mkdir nvidia_opencl_samples
 1443  ls
 1444  net usershare info
 1445  cd media/shawnstark/3tbhd
 1446  cd /media/shawnstark/3tbhd
 1447  ls
 1448  cd ../
 1449  rm 3tbhd
 1450  rm -fr 3tbhd
 1451  sudo rm -fr 3tbhd
 1452  cd var/lib/samba/
 1453  cd /var/lib/samba/
 1454  ls
 1455  cd DriverStore/
 1456  ls
 1457  cd FileRepository/
 1458  ls
 1459  cd ../
 1460  cd Temp/
 1461  ls
 1462  cd../
 1463  cd ../
 1464  ls
 1465  cd ../
 1466  ls
 1467  cd usershares/
 1468  ls
 1469  rm 3tbhd audacity 
 1470  sudo bash
 1471  looking-glass-client win:fullScreen opengl:amdpinnedmem=1
 1472  pytorch --version
 1473  pip show pytorch torchaudio transformers diffusers invisible-watermark xformers
 1474  clear
 1475  pip show torch torchaudio transformers diffusers invisible-watermark xformers
 1476  clear
 1477  cd /genvol/stable_diffusion/stable-diffusion-webui/
 1478  clear
 1479  ls
 1480  clear
 1481  history | grep web
 1482  c;ear
 1483  clear
 1484  ./webui.sh
 1485  net info
 1486  net domain
 1487  ip link show
 1488  clear
 1489  nmcli device status
 1490  nmcli connection show
 1491  ip a
 1492  man nmcli
 1493  nmcli device status
 1494  nmcli delete br0
 1495  nmcli help
 1496  ip link show
 1497  nmcli device status
 1498  nmcli connection show
 1499  man nmcli | grep delete
 1500  nmcli connection delete ce6bd194-671c-4f9a-ac14-d1cf6251710a
 1501  nmcli connection delete 61792bcf-a784-4ca8-b016-6be46ae261aa
 1502  nmcli connection show
 1503  nmcli device status
 1504  nmcli connection show
 1505  sudo bash
 1506  nslookup nist.com 10.42.53.91
 1507  nslookup nist.com 127.0.0.1
 1508  nslookup nist.com 1.1.1.1
 1509  man nslookup
 1510  nslookup host nist.com 10.42.5391
 1511  nslookup host nist.com 10.42.53.91
 1512  man nslookup
 1513  nslookup help
 1514  nslookup -help
 1515  man nslookup
 1516  nslookup nist.gov
 1517  sudo bash
 1518  nmcli connection show
 1519  nmcli device status
 1520  nmcli connection delete 9295bb48-a612-4caf-9715-6f56e90a9cba
 1521  sudo bash
 1522  exit
 1523  history | grep syslog
 1524  sudo bash
 1525  looking-glass-client win:fullScreen opengl:amdpinnedmem=1
 1526  passwd
 1527  sudo bash
 1528  looking-glass-client win:fullScreen opengl:amdpinnedmem=1
 1529  history | grep syslog
 1530  sudo bash
 1531  sudo bash
 1532  ls /etc/cron.*
 1533  gedit /etc/cron.hourly/
 1534  ls /etc/cron.hourly/
 1535  clear
 1536  crontab -l
 1537  sudo bash
 1538  adb
 1539  clear
 1540  adb devices
 1541  adb connect HT7460201149
 1542  adb -s HT7460201149 shell
 1543  fastboot devices
 1544  sudo apt install fastboot
 1545  fastboot devices
 1546  adb devices
 1547  fastboot devices
 1548  fastboot flashing unlock
 1549  fastboot oem unlock
 1550  fastboot flashing unlock
 1551  fastboot devices
 1552  fastboot flashing unlock
 1553  adb devices
 1554  adb install Cx_File_Explorer_base.apk 
 1555  adb devices
 1556  sudo bash
 1557  exit
 1558  looking-glass-client win:fullScreen opengl:amdpinnedmem=1
 1559  sudo bash
 1560  exit
 1561  sudo bash
 1562  history | grep watch
 1563  watch -n 0.5 nvidia-smi
 1564  nvtop
 1565  history | grep apt
 1566  sudo bash
 1567  clear
 1568  watch sensors
 1569  sudo bash
 1570  history | grep web
 1571  cd /genvol/stable_diffusion/stable-diffusion-webui/
 1572  ls
 1573  ./webui.sh
 1574  exit
 1575  sudo bash
 1576  hostname
```

## Another random history

```bash
1594  watch -n 0.5 nvidia-smi
 1595  cd /genvol/stable_diffusion/stable-diffusion-webui/
 1596  ls
 1597  ./webui.sh
 1598  sudo bash
 1599  cd /genvol/stable_diffusion/stable-diffusion-webui/
 1600  export CUDA_VISIBLE_DEVICES=1; ./webui.sh --port 7861
 1601  passwd
 1602  cd /genvol/stable_diffusion/stable-diffusion-webui/
 1603  export CUDA_VISIBLE_DEVICES=1; ./webui.sh  --port 7861
 1604  cd /genvol/stable_diffusion/stable-diffusion-webui/
 1605  export CUDA_VISIBLE_DEVICES=0; ./webui.sh  --port 7860
 1606  clear
 1607  export CUDA_VISIBLE_DEVICES=0; ./webui.sh  --port 7860
 1608  export CUDA_VISIBLE_DEVICES=0; ./webui.sh  --port 7863
 1609  export CUDA_VISIBLE_DEVICES=1; ./webui.sh  --port 7863
 1610  history | grep glass
 1611  looking-glass-client win:fullScreen opengl:amdpinnedmem=1
 1612  history | grep shmem
 1613  sudo bash
 1614  cd /
 1615  ls
 1616  cd /etc/systemd/system/
 1617  ls
 1618  sudo systemctl start lg_start
 1619  sudo systemctl status lg_start
 1620  looking-glass-client win:fullScreen opengl:amdpinnedmem=1
 1621  cd /genvol/stable_diffusion/stable-diffusion-webui/
 1622  export CUDA_VISIBLE_DEVICES=1; ./webui.sh  --port 7861
 1623  cd ../
 1624  cd buildagain/
 1625  export CUDA_VISIBLE_DEVICES=1;
 1626  echo $CUDA_VISIBLE_DEVICES
 1627  ./webui.sh  --port 7861
 1628  ls
 1629  cd stable-diffusion-webui/
 1630  ./webui.sh  --port 7861
 1631  cd ../
 1632  cd stable-diffusion-webui/
 1633  ./webui.sh  --port 7861
 1634  looking-glass-client win:fullScreen opengl:amdpinnedmem=1
 1635  gedit ~/.profile
 1636  sudo bash
 1637  sudo snap install outlook-for-linux –edge
 1638  sudo snap install outlook-for-linux --edge
 1639  looking-glass-client win:fullScreen opengl:amdpinnedmem=1
 1640  watch -n 0.5 nvidia-smi
 1641  sudo bash
 1642  exit
  1862  sudo bash
 1863  apt-files --list mesa-utils-extra
 1864  apt-file --list mesa-utils-extra
 1865  apt-file -list mesa-utils-extra
 1866  apt-file list mesa-utils-extra
 1867  apt-file list mesa-utils
 1868  es2gears_wayland
 1869  es2gears_x11
  1939  sudo e2fsck -pf /dev/nbd0
 1940  sudo badblocks /dev/nbd0
 1941  sudo sgdisk --info=5 /dev/nbd0
 1942  sudo sgdisk --info=1 /dev/nbd0
 1943  man btrfs
 1944  sudo btrfs-check /dev/nbd0
 1945  sudo btrfs check /dev/nbd0
 1946  sudo e2fsck /dev/nbd0
 1947  sudo dumpe2fs /dev/nbd0 | grep -i superblock
 1948  sudo fsck -y /dev/nbd0
 1949  sudo dumpe2fs /dev/nbd0 | grep -i superblock
 1950  sudo e2fsck /dev/nbd0
 1951  sudo e2fsck -pf /dev/nbd0
 1952  sudo e2fsck -pf -b 8193 /dev/nbd0
 1953  sudo e2fsck -pf -b 32768 /dev/nbd0
 1954  sudo bash
  1957  opensnitch
 1958  which opensnitch
 1959  apt-files opensnitch
 1960  apt-file opensnitch
 1961  apt-file list opensnitch
 1962  apt --list-installed | grep snitch
 1963  apt list installed | grep snitch
 1964  sudo apt list installed | grep snitch
 1965  sudo apt list --installed | grep snitch
 1966  apt-file opensnitch
 1967  apt-file list opensnitch
 1968  sudo apt-file list opensnitch
 1969  sudo bash
 1970  cd /opt
 1971  ls
 1972  cd opensnitch/
 1973  ls
 1974  sudo apt install ./python3-opensnitch-ui_1.6.0.1-1_all.deb 
 1975  ls
 1976  apt install --reinstal ./opensnitch_1.6.0.1-1_amd64.deb 
 1977  sudo apt install --reinstall ./opensnitch_1.6.0.1-1_amd64.deb 
 1978  sudo apt install --reinstall ./python3-opensnitch-ui_1.6.0.1-1_all.deb 
 1979  sudo bash
 1980  exit
 1991  gsettings get org.gnome.nautilus.list-view default-visible-columns
 1992  gsettings set org.gnome.nautilus.list-view default-visible-columns "['name', 'size', 'type', 'owner', 'group', 'permissions', 'date_modified', 'accessed']"
 1993  man kernel.lockdown.7
 xprop

```