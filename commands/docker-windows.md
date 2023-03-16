### View logs

dockerd -D

### powershesll

Get-EventLog -LogName Application -Source Docker -After (Get-Date).AddMinutes(-5)


# save in a file 

get-EventLog -LogName Application -Source Docker  |  format-table -autosize | out-file c:\temp\logs.txt

(get-EventLog -LogName Application -Source Docker -Newest 1).Message


https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/get-eventlog?view=powershell-5.1


Get-EventLog -LogName Application -Source Docker -After (Get-Date).AddMinutes(-15) | ft -a -wr




### Clear mess docker folder on windows ( sometimes works/sometimes not )
Download
https://github.com/jhowardmsft/docker-ci-zap/blob/master/docker-ci-zap.exe

Stop Docker

Run
.\docker-ci-zap.exe -folder "C:\ProgramData\docker