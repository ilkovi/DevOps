
### Documentation
https://kitchen.ci/docs/getting-started/adding-platform/




# on the workstation

cd chef-repo/cookbooks

chef generate cookbook first_cookbook




### KITCHEN TEST

`zypper in vagrant
`zypper in virtualbox
` enable VT-x for the virtual machine ( in powershell on the host )
Set-VMProcessor -VMName <VMName> -ExposeVirtualizationExtensions $true


check kitchen.yml


kitchen list  - list all instances defined in the kitchen test



kitchen create default-ubuntu-2004	- create that instance


# run test in the specify machine
kitchen converge
!! will leave the machine running and kitchen automatically uploads changes each converge so that one can iterate rapidly on configuration code

- chef infar client was installed on the instance
- git_cookbook files and minimal chef infra client configuration were built adn uploaded to the instance
- chef infra client run was initiated



# manual test 
kitchen login 

kitchen login <instance>

ubuntu> which git
ubuntu> git --version
ubuntu> exit




# add test

vi test/integration/default/default_test.rb
describe package('git') do
  it { should be_installed }
end


# why this file
vi kitchen.yml


# run the test

kitchen verify default-ubuntu-2004






### Do all of the Above with one command

kitchen test


What this do:
    Destroys the instance if it exists
    Creates the instance
    Converges the instance
    Verifies the instance with InSpec
    Destroys the instance








