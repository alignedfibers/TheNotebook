## Doing a sparsecheckout (most likely from gitlab sources)
***Just an example w/ less talk***
```bash
mkdir uboot_src
git init uboot_src
cd uboot_src
git config core.sparseCheckout true
git  remote add origin https://source.denx.de/u-boot/u-boot.git
git fetch origin
#git branch -r
git checkout origin/master
#git read-tree -mu HEAD
#git ls-tree -r --name-only origin/master
#git ls-tree -r --name-only origin/master | grep drivers
touch .git/info/sparse-checkout
#echo "drivers/" > .git/info/sparse-checkout
#git sparse-checkout set drivers/
```