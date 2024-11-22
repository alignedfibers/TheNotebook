#!/bin/bash
```bash
#messed up a branch and want to just put it on a dirty branch and start over?
git status
git add . 
git commit -m "Pre move to dirtybranch"
git push
git branch -m master dirtybranch
git commit -allow-empty -m "Post move to dirtybranch"
git push
git rm -rf .
git checkout --orphan master
ls -al 
git commit --allow-empty -m "Initialize clean master branch"
git push --force origin master

#Okay now lets fully merge current upstream with all comments
git fetch upstream
git checkout master
git reset --hard upstream/master
git push --force origin master

#We have something from dirtybranch we still need to add.
#Since I could see it in github when looking at commit history on dirty easy to see the commit id
#git log origin/dirtybranch | grep -C 5 6ed51bb // there might be better way to see if in terminal only.
git cherry-pick 6ed51bb
git add .
git commit -m "increased vm memory on surefire configuration"


#Lets set a new remote, fetch it and commit it new new branch on my side
#https://android.googlesource.com/platform/external/nanohttpd
git add .
git commit -m "general commit"
git push
git remote add external https://android.googlesource.com/platform/external/nanohttpd
git remote -v
git fetch external
git branch gsource-main
git branch -a
git worktree add ../googlesource-nanohttpd gsource-main
cd ../googlesource-nanohttpd
git checkout --orphan gsource-main
git rm -rf . 
git commit --allow-empty -m "Initialize clean gsource-main branch"
git push --force origin gsource-main
git fetch external
git reset --hard external/main
git push --force origin gsource-main


external/android10-release


Other git commands I keep using over and over.
 git cherry-pick 03dd1ea
 git cherry-pick --continue

git worktree add ../nanohttpd-gsource-android10-rel external/android10-release
git status
git branch
git branch gsource-android10-rel
git switch gsource-android10-rel
git push origin gsource-android10-rel
git remote -v
git branch -a

### Need to be able to reorigin easy
#Create a new repository without intializing it first
#Make sure the upstream origin is currently set and pulled
#If intended to use the upstream remote add upstream or remote add external excetera
#So once that prereq done, then go ahead and push to the new repo, and start making your changes
#Basically I keep a dirty master I am working on, and then will create another and merge what I want.
#I try to commite on a full feature or full change bases isolated to a specific.
#Committing with intent of minimal change only for the one need which should easy merge to upstream later if cherry-pick
git status
git remote -v
git branch -a
git pull master
#git remote remove origin
git remote set url origin https://github.com/alignedfibers/antenna-pod-plus.git
git push -u origin <branch-name>
#git branch --set-upstream-to=origin/develop develop

```



