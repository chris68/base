## PHP5 ##
### Caching ###
Best use PHP 5.5 native OPcache.

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
-r--------  1 root root 2618 Sep 25 00:38 mailwitch.com.crt
```
Create the ssl.key folder with the keys
```
/etc/apache2$ ls -al ssl.key/
total 12
drwxr-xr-x  2 root     root 4096 Jul 21  2013 .
drwxr-xr-x 10 root     root 4096 Jan 28 12:46 ..
-r--------  1 root root 3243 Jan 28 12:47 myserver.key
```
### OAUTH ###
Create oauth.crt folder with the certificates
```
/etc/apache2$ ls -al oauth.key/
total 16
drwxr-xr-x  2 root root 4096 Jan 28 14:27 .
drwxr-xr-x 10 root root 4096 Jan 28 12:46 ..
-r--------  1 root root 2618 Sep 25 00:38 mailwitch.com.ini
```
## Cron Jobs ##
Edit the crontab (``via crontab -e``) and add 
```
0 4 * * * cpulimit -i -l 60 /home/mailwitch/base/pg_backup_rotated.sh
```
> The -i option (include children) works only with a rather new version of cpulimit
> Make sure to test whether your distribution already includes this version

cpulimit restricts the resource consumption to a reasonable degree (install it ``sudo apt-get install cpulimit``)
