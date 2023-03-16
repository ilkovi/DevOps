### Search for file in a dir

dir secret.txt /s /p
where /r . *.sln
dir /b/s *.csproj


### print env

ENV variables: System > Advanced System Settings > Environment Variables.
CMD: refreshenv	- to update the env variables
CMD: set







### CMD find uptime

systeminfo | find "System Boot Time"
systeminfo | findstr /B /C:"OS Name" /C:"OS Version"		#### Winodws VERSION







### Check DNS

CMD >    nslookup @dns_ip domain/ip_address to check


### dump traffic / but BEST is WireShark

https://www.nirsoft.net/x64_download_package.html





### TELNET

dism /online /Enable-Feature /FeatureName:TelnetClient



### Kill processes from CMD

taskkill /IM dotnet* /F


