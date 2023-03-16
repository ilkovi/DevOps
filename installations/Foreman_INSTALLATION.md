### Installation ubuntu 18

https://geekflare.com/puppet-installation-ubuntu/

# foreman

https://computingforgeeks.com/how-to-install-foreman-on-ubuntu-18-04-lts-bionic-beaver/


### Restart foreman

service httpd restart
service dynflowd restart
service foreman-proxy restart





### Install / UnInstall the agent win

`download

https://downloads.puppetlabs.com/windows/puppet/

` Install

msiexec /qn /norestart /i puppet-agent-

` Uninstall

CMD as Administrator

wmic

product get name

product where name="Puppet Agent (64-bit)" call uninstall

Y



### UnInstall - puppet.msi

msiexec /qn /x [puppet.msi|product-code]

` manually remove the data directory







### Node bad connection

on the server -> smart-proxy revoke certificate

on the host -> delete : C:\ProgramData\PuppetLabs\puppet\etc\ssl

puppet agent --server servername --waitforcert 60

