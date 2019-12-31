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
## Certbot / Letsencrypt ##
### SSL ###
The certificates are managed via letsencrypt.org and are created via certbot. They can be found here:
```
/etc/letsencrypt/live/<server>
```

Add the restart of the services to the end of ``/etc/letsencrypt/cli.ini``:
```
deploy-hook = "service postfix reload ; service dovecot restart ; service apache2 reload"
```

Adapt ``/etc/letsencrypt/options-ssl-apache.conf`` and forbid the old versions ``-TLSv1 -TLSv1.1`` of TLS:
```
SSLProtocol             all -SSLv2 -SSLv3 -TLSv1 -TLSv1.1
```

## Apache ##
### SSL ###
The certificates are managed via letsencrypt.org and are created via certbot. They can be found here:
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

## Backups ##
### MCRYPT ###
Install mcrypt via
```
sudo apt-get install mcrypt
```
Create the rcfile and fix the access rights
```
touch /home/mailwitch/.mcryptrc; chmod 600 /home/mailwitch/.mcryptrc; nano /home/mailwitch/.mcryptrc
```
Copy the following into the config file; replace the <secret key> with the actual key
```
algorithm twofish
key <secret key>
```

Test it via mcrypt <file> and mdecrypt <file.nc>
### RSYNC ###
Install mcrypt via
```
sudo apt-get install rsync
### Install gdrive link and link to it ###
Install google-drive-ocamlfuse
```
sudo add-apt-repository ppa:alessandro-strada/ppa
sudo apt install google-drive-ocamlfuse
```
Then get the credentials. The -label gives the option to store more than one profile. If you do not enter a label the default profile will be used.
You must use the profile mailwitch for the scripts to work!
```
google-drive-ocamlfuse -label <google-account/profile>
```
For a headless server you need to do this on a server with a browser and then copy the credentials which are stored in ~/.gdfuse/<profile>/state.

Create an empty folder for mounting.
```
mkdir ~/gdrive_<profile>
```
Mounting and unmounting goes as follows.
```
google-drive-ocamlfuse -label <profile> ~/gdrive_<profile>
fusermount -u /home/mailwitch/gdrive_<profile>
```
Finally symlink to the gdrive subfolder where the backups should end up
```
ln -s /home/mailwitch/gdrive-<profile>/<backupfolder-in-gdrive> backups.remote
```
### Test ###
Execute /home/mailwitch/base/pg_backup_rotated.sh once. Check whether the files go encrypted to gmail
## Monit ##
Monitoring is done with monit. Use the two monit files to set it up.
## Cron Jobs ##
Install cpu-limit
```
sudo apt install cpulimit
```
Edit the crontab (``via crontab -e``) and add 
```
0 4 * * * { /home/mailwitch/base/pg_backup_rotated.sh ; }
0 2 * * 1 { /usr/bin/sudo /usr/bin/certbot renew ; }
```

## Deployment ##
The necessary files for deployment can be retrieved via:
```
wget https://raw.githubusercontent.com/chris68/mwes2/master/deploy-mwes2.sh -O deploy-mwes2.sh; chmod +x deploy-mwes2.sh
wget https://raw.githubusercontent.com/chris68/pnauw/master/deploy-pnauw.sh -O deploy-pnauw.sh; chmod +x deploy-pnauw.sh
```
