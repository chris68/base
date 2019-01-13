## PHP ##
### Caching ###
Best use PHP native OPcache.

**Do not use xcache!** It has a bug (http://xcache.lighttpd.net/ticket/348) that will hinder multisite usage. You will only be able to access the first site while the second site will only return the first site.
## Postgres users ##
The password can be the same as the user since we are in a trusted hosting environment
```
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
The certificates are managed via letsencrypt.org and are created via certbot. The can be found here:
```
/etc/letsencrypt/live/<server>
```
### OAUTH ###
Create oauth.key folder with the certificates and copy the files from the relevant/old server
```
sudo mkdir /etc/apache2/oauth.key
chgrp www-data /etc/apache2/oauth.key
sudo chmod 750 /etc/apache2/oauth.key
```
Make sure files are read only and have the correct group so that apache/php can access it
```
chgrp www-data /etc/apache2/oauth.key/*.ini
chmod 330 /etc/apache2/oauth.key/*.ini
```
Looks as follows:
```
/etc/apache2$ ls -al oauth.key/
total 16
drwxr-xr-x  2 root root 4096 Jan 28 14:27 .
drwxr-xr-x 10 root root 4096 Jan 28 12:46 ..
-r--------  1 root root 2618 Sep 25 00:38 mailwitch.com.ini
-r--------  1 root root 2618 Sep 25 00:38 parke-nicht-auf-unseren-wegen.ini
```
## Cron Jobs ##
Edit the crontab (``via crontab -e``) and add 
```
0 4 * * * cpulimit -i -l 60 /home/mailwitch/base/pg_backup_rotated.sh
```
> The -i option (include children) works only with a rather new version of cpulimit
> Make sure to test whether your distribution already includes this version

cpulimit restricts the resource consumption to a reasonable degree (install it ``sudo apt-get install cpulimit``)

## Deployment ##
The necessary files for deployment can be retrieved via:
```
wget https://raw.githubusercontent.com/chris68/mwes2/master/deploy-mwes2.sh -O deploy-mwes2.sh; chmod +x deploy-mwes2.sh
wget https://raw.githubusercontent.com/chris68/pnauw/master/deploy-pnauw.sh -O deploy-pnauw.sh; chmod +x deploy-pnauw.sh
```
