# Validate

puppet parser validate cleanuppc/manifests/init.pp


# Test class

puppet apply --noop your_file.pp 


### Running commands

exec { 'resource title':
 command     => # (namevar) The actual command to execute. 
 creates     => # A file to look for before running the command. 
 path        => # The directory of command. 
 logoutput   => # Whether to log command output 
 refreshonly => # The command should only be run as a refresh. 
 returns     => # The expected exit code(s).  Any different exit 
				# code will return error. 
 timeout     => # The maximum time the command should take. 
}

### List bukced files

puppet filebucket -l -f 2015-01-01 -t 2017-07-17 list     
or
puppet filebucket -l list


`Diff between the file in the filebucket and a local file

$ puppet filebucket -l diff d43a6edfasdf34234fsdf /tmp/testFile

`windows file location

C:\ProgramData\PuppetLabs\puppet\cache\clientbucket
