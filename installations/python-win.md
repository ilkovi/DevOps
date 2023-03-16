

1. Downoad python 3.8.5 and install it under c:\Program files\python



2. follow this guide https://medium.com/@rajesh.r6r/deploying-a-python-flask-rest-api-on-iis-d8d9ebf886e9

A )
download it under : C:\Program Files\python\wfastcgi-3.0.0
install wfastcgi : py.exe setup.py install
...
Installed c:\program files\python\lib\site-packages\wfastcgi-3.0.0-py3.8.egg
Processing dependencies for wfastcgi==3.0.0
Finished processing dependencies for wfastcgi==3.0.0


B ) enable it

cd C:\Program Files\python\Scripts

pplied configuration changes to section "system.webServer/fastCgi" for "MACHINE/WEBROOT/APPHOST" at configuration commit path "MACHINE/WEBROOT/APPHOST"
""C:\Program Files\python\python.exe"|"C:\Program Files\python\lib\site-packages\wfastcgi-3.0.0-py3.8.egg\wfastcgi.py"" can now be used as a FastCGI script processor



3. follow this guide https://www.server-world.info/en/note?os=Windows_Server_2019&p=iis&f=12

POWERSHELL

Install-WindowsFeature Web-CGI,Web-ISAPI-Ext 

Restart-Service W3SVC 



> Get-Website

Name             ID   State      Physical Path                  Bindings
----             --   -----      -------------                  --------
Default Web Site 1    Started    %SystemDrive%\inetpub\wwwroot  http *:15555:




# add Python to ISAPI/CGI extension
PS C:\Users\Administrator> Add-WebConfiguration -Filter '/system.webServer/security/isapiCgiRestriction' -Value @{description="Python";path="C:\Program Files\python\python.exe %s %s";allowed="true"} 


# for example, set [*.py] scripts as executable under [/] folder on [Default Web Site] site
PS C:\Users\Administrator> Set-WebConfigurationProperty -Filter '/system.webServer/handlers' -Location "Default Web Site" -Name accessPolicy -Value "Read, Script" 


PS C:\Users\Administrator> Add-WebConfiguration -Filter '/system.webServer/handlers' -Location "Default Web Site" -Value @{name="Python Interpreter";path="*.py";verb="*";modules="CgiModule";scriptProcessor="C:\Program Files\python\python.exe %s %s";resourceType="File"} 


# restart Web site
PS C:\Users\Administrator> Restart-WebItem -PSPath 'IIS:\Sites\Default Web Site' 



# create test script
PS C:\Users\Administrator> $str_document = @'
print("Content-type: text/html\n\n")
print("Python Script Test Page on IIS")
'@ 
PS C:\Users\Administrator> Write-Output $str_document | Out-File C:\inetpub\wwwroot\test.py -Encoding Default 



# verify to access
PS C:\Users\Administrator> curl.exe https://trackercicd01.sbtech.com/test.py 


































