## PHP5 ##
### XCACHE ###
**Do not use xcache!** It has a bug (http://xcache.lighttpd.net/ticket/348) that will hinder multisite usage. You will only be able to access the first site while the second site will only return the first site.
## Postgres users ##
The password can be the same as the user since we are in a trusted hosting environment
```
sudo -i -u postgres psql -c "alter user postgres password 'postgres'"
sudo -i -u postgres createuser --no-inherit --superuser mailwitch
sudo -i -u postgres psql -c "alter user mailwitch password 'mailwitch'"
```

## Server directories ##
```
# Main variable directory
sudo mkdir /var/opt/mailwitch
sudo chown mailwitch:mailwitch /var/opt/mailwitch
sudo chmod 755 /var/opt/mailwitch

# Web directory
sudo mkdir /var/opt/mailwitch/www
sudo chown mailwitch:mailwitch /var/opt/mailwitch/www
sudo chmod 755 /var/opt/mailwitch/www
```

## Apache ##
### SSL ###
Create ssl.crt folder with the certificates
```
/etc/apache2$ ls -al ssl.crt/
total 16
drwxr-xr-x  2 root root 4096 Jan 28 14:27 .
drwxr-xr-x 10 root root 4096 Jan 28 12:46 ..
-r--------  1 root root 2212 Sep 25 00:38 cert.startssl.org-sub.class1.server.ca.pem
-r--------  1 root root 2618 Sep 25 00:38 www.mailwitch.com-server.crt
```
Create the ssl.key folder with the keys
```
/etc/apache2$ ls -al ssl.key/
total 12
drwxr-xr-x  2 root     root 4096 Jul 21  2013 .
drwxr-xr-x 10 root     root 4096 Jan 28 12:46 ..
-r--------  1 root root 3243 Jan 28 12:47 www.mailwitch.com-server.key
```
