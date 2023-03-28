
### List remote Branch
	git branch -l -r |grep pattern


### create new local branch and checkout to it
	git checkout -b Update_docker_file


### Just create local branch
	git branch Update_docker_file


### delete local branch 
# only deletes the branch if it has already been fully merged in its upstream branch
git branch -d the_local_branch      

# --delete --force, which deletes the branch "irrespective of its merged status
git branch -D the_local_branch      


### Delete remote branch

 git push origin -d releaseCandidate/Some-App-v0.2.0


### push new branch

git push origin hotfix/ivan.il-test