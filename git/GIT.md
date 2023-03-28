


### Checkout with new branch <naming convention>

 git checkout -b 20201209_IvanIlkov_adding_additional_attributes_GMPSM-7126
 
 date_Name_description_jira-task





### Edit the required

cd roles/additional-attributes/

ls

cp vmp_saas_vlab-jpaas-staging-cs-2-bladelogic-sles12sp5.json vmp_saas_vlab-jpaas-staging-cs-2-bladelogic-2-sles12sp5.json
vi vmp_saas_vlab-jpaas-staging-cs-2-bladelogic-2-sles12sp5.json

cp vmp_saas_vlab-jpaas-staging-cs-2-bladelogic-sles12sp2.json vmp_saas_vlab-jpaas-staging-cs-2-bladelogic-2-sles12sp2.json
vi vmp_saas_vlab-jpaas-staging-cs-2-bladelogic-2-sles12sp2.json




### COMMIT   ! separate commit for each file !


git add vmp_saas_vlab-jpaas-staging-cs-2-bladelogic-2-sles12sp2.json
git commit -m "Adding additional attributes for new VM pool VLAB-JPaaS-Staging-CS-2-BladeLogic-2-SLES12SP2 ( GMPSM-7126 )"

git status

git add vmp_saas_vlab-jpaas-staging-cs-2-bladelogic-2-sles12sp5.json
git commit -m "Adding additional attributes for new VM pool VLAB-JPaaS-Staging-CS-2-BladeLogic-2-SLES12SP5 ( GMPSM-7126 )"

git status





### Run test

rake list

Once test ready




### git status

- check if there are some files added




### git push -n 

fatal: The current branch 20201209_IvanIlkov_adding_additional_attributes_GMPSM-7126 has no upstream branch.
To push the current branch and set the remote as upstream, use

    git push --set-upstream origin 20201209_IvanIlkov_adding_additional_attributes_GMPSM-7126




### git push

git push --set-upstream origin 20201209_IvanIlkov_adding_additional_attributes_GMPSM-7126



###  it create a pull request

c5317137@vsa0815:~/workspace/chef/roles/additional-attributes> git push --set-upstream origin 20201209_IvanIlkov_adding_additional_attributes_GMPSM-7126
Enter passphrase for key '/home/c5317137/.ssh/id_rsa':
Enumerating objects: 18, done.
Counting objects: 100% (18/18), done.
Delta compression using up to 10 threads
Compressing objects: 100% (11/11), done.
Writing objects: 100% (14/14), 3.85 KiB | 1.92 MiB/s, done.
Total 14 (delta 9), reused 5 (delta 3), pack-reused 0
remote: Resolving deltas: 100% (9/9), completed with 4 local objects.
remote:
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
remote: Create a pull request for '20201209_IvanIlkov_adding_additional_attributes_GMPSM-7126' on GitHub by visiting:
remote:      https://github.wdf.sap.corp/CloudChef/chef/pull/new/20201209_IvanIlkov_adding_additional_attributes_GMPSM-7126
<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<
remote:
To github.wdf.sap.corp:CloudChef/chef.git
 * [new branch]            20201209_IvanIlkov_adding_additional_attributes_GMPSM-7126 -> 20201209_IvanIlkov_adding_additional_attributes_GMPSM-7126
Branch '20201209_IvanIlkov_adding_additional_attributes_GMPSM-7126' set up to track remote branch '20201209_IvanIlkov_adding_additional_attributes_GMPSM-7126' from 'origin'.



### copy and paste in the browser

https://github.wdf.sap.corp/CloudChef/chef/pull/new/20201209_IvanIlkov_adding_additional_attributes_GMPSM-7126

# Format Title

# Add description

# Reviewers
A-Team
D-Team

# Assignees
Me

# Scroll down and check Jenkins checks

jenkins@saas/branch — This commit looks good !






### Once the pull is approved



git checkout master

git pull --all

git branch -l -r |grep Ivan

git checkout 20201209_IvanIlkov_adding_additional_attributes_GMPSM-7126

git rebase master

git push origin 20201209_IvanIlkov_adding_additional_attributes_GMPSM-7126 --force


### Go to Web. Wait for test to pass. And select Merge , Confirm. Delete Branch


### Go locally and delete the local branch

git branch -l

git checkout master

git branch -d 20201209_IvanIlkov_adding_additional_attributes_GMPSM-7126






# if some commits landed erroneous in a PR on github
# this commands help to start fresh from master
# replace `my_pr_branch` with the name of the branch of your pull request

git checkout my_pr_branch
git diff master > ../pr.patch
git checkout master
git branch -D my_pr_branch 

# make sure it is the *SAME* name than the branch you deleted
git checkout -b my_pr_branch
patch -p1 < ../pr.patch 

# git add ...  if necessary 
git commit -a -m "Describe the PR commit"
git push -f --set-upstream origin my_pr_branch