## Doing a sparsecheckout (most likely from gitlab sources)
***Just an example w/ less talk***
```bash
mkdir uboot_src
git init uboot_src
cd uboot_src
git config core.sparseCheckout true
git  remote add origin https://source.denx.de/u-boot/u-boot.git
git fetch origin
#Next command list the branches we are interested in likely will be main or master
git branch -r
#This next command helps us select wich directories we want to pull
#git ls-tree -r --name-only origin/master
git ls-tree -r --name-only origin/master | grep drivers
#Next command sets the one folder we are intersted in for the sparse-checkout, not sure if it is additive, or rewrites the sparse-checkout file
git sparse-checkout set drivers/
#Show the sparse_checkout file contents to be sure it is actually containin what we want.
cat .git/info/sparse-checkout
#Show status should be empty
#git status
#The next command will pull the directory specified only, and use any kconfig, makefile to select other dirs as dependencies if any.
git pull origin master
#you can add ignores for other directory locations if you do not want to pull the dependencies directly in sparse-checkout

#Not using the next commands actually but keeping them for clarity, 
#It seems if you checkout before setting sparse-checkout correctly, commands below will not help fully.
#git checkout origin/master
#This next command for some reason only works after a pull or checkout
#git read-tree -mu HEAD
#Create the sparse-checkout file just for funzies
#touch .git/info/sparse-checkout
#Next command manually sets the folders to pull, you can repeat it over using >> to add additional lines.
#echo "/drivers/" > .git/info/sparse-checkout
#Next command reads the tree into index for sparse-checkout without actually checking out or pulling
#git read-tree -mu origin/master
#If you oops and read the tree into index before using the sparse-checkout to properly track what you want next command
#git clean -fdx
#If git status command still lists a bunch of files or directories you are not expecting to track
#git sparse-checkout init --cone
#git sparse-checkout set drivers/
##That is why git is hard, because when you screw up then you basically have to start over or remember weird commands.
```