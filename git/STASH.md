
### GIT STASH
 
#  Saved working directory and index state WIP on master

git stash  # save changes and we could checkout other branch


git stash list # list all our stashes. We could have many


git stash list -p  # list exact files/lines/content we have saved


git stash apply  # apply most recent stash


git stash pop # apply most recent stash and remove it from the git stash list


### apply specifyc stash
git stash list
git stash apply stash@{0} 


### add label to more clear stash
git stash save "Add Yellow vlan"
git stash list

