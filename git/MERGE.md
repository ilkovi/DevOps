
### MERGE

git mergetool

### First type merge - fast forward
* there is a direct path between two branches
* git will just move the master where the other commit is

git checkout master

# NICE
git diff master..branch_name
* what we will change while we merge

git merge branch_name


# This will show what is merged in our (master) branch
git branch --merge




### Second type merge - 3-way merge
* there is no direct path between branches




### Merge Conflicts

git merge DEV_Branch

#Sometimes there are conflicts # Git says

You have unmerged paths.

# Easy way out - Escape
git merge --abort

# Fix

<<<<<HEAD
pink:
=====		--> separete the content of two files
green:
>>>>>DEV_Branch

We should decide wiht wich text we go.
Keep it and delete everything else


green:

git add file	# the file that we fixed

git status

git commit

# Done