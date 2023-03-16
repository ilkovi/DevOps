### View exparation date of cert

openssl x509 -enddate -noout -in /etc/ssl/certs/nginx-selfsigned.crt
`example:
notAfter=Aug 18 18:18:18 2018 GMT


### Decriypt certificate into text

openssl x509 -text -noout -in /etc/ssl/certs/nginx-selfsigned.crt


### Generate new certificate

openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/nginx-selfsigned.key -out /etc/ssl/certs/nginx-selfsigned.crt
Country Name (2 letter code) [AU]:
State or Province Name (full name) [Some-State]:
Locality Name (eg, city) []:
Organization Name (eg, company) [Internet Widgits Pty Ltd]:
Organizational Unit Name (eg, section) []:Tracker
Common Name (e.g. server FQDN or YOUR name) []:
Email Address []:


### restart kibana and nginx

systemctl restart kibana
systemctl restart nginx