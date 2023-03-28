
#### Configure GIT

git init .
Initialized empty Git repository in /root/git_repository/.git/


vi .git/config

git config --global user.name "Ivan Ilkov"
git config --global user.email ivan.ilkov@domain.com


git config --global credential.helper manager

git config --list

git config --global --edit


git config --global --unset user.email


git config --global color.diff auto
git config --global color.status auto
git config --global color.branch auto
git config --global color.interactive true
git config --global help.format html
git config --global credential.helper manager
git config --global rebase.autosquash=true
