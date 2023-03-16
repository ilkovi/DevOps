1. Installation

add perforce.repo
[perforce]
name=Perforce
baseurl=http://package.perforce.com/yum/rhel/7/x86_64/
enabled=1
gpgcheck=1

2. Install the following
perforce-cli-2015.1-1446864.x86_64.rpm
perforce-cli-base-2015.1-1446864.x86_64.rpm
perforce-p4dctl-2015.1-1446864.x86_64.rpm
perforce-server-2015.1-1446864.x86_64.rpm
perforce-server-base-2015.1-1446864.x86_64.rpm

3. Edit the conf file /etc/perforce/p4dctl.conf
p4d main
{
    Owner          =        perforce
    Execute        =        /opt/perforce/sbin/p4d
    Prefix         =        /var/lib/perforce/p4-main/main
    Environment
    {
       P4ROOT      =        /opt/perforce/p4_server_root
       P4JOURNAL   =        journal
       P4PORT      =        ssl:1666
       P4SSLDIR    =        /opt/perforce/p4_server_root/ssl/
       PATH        =        /bin:/usr/bin:/usr/local/bin:/opt/perforce/sbin
    }
}


NOTE: SSL dir must be 700 permissions and empty !

4. log on as perforce
su - perforce

5. Check for any errors on the conf
p4d -Gc

6. Start perforce
service perforce-p4dctl start