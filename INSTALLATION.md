## Postgres users ##

```
sudo -i -u postgres psql -c "alter user postgres password 'postgres'"
sudo -i -u postgres createuser --no-inherit --superuser mailwitch
sudo -i -u postgres psql -c "alter user mailwitch password 'mailwitch'"
```

## Apache server directories ##
```
sudo mkdir -p /opt/mailwitch/www

sudo chown mailwitch:mailwitch /opt/mailwitch
sudo chmod 755 /opt/mailwitch
sudo chown mailwitch:mailwitch /var/opt/mailwitch
sudo chmod 755 /var/opt/mailwitch || check_error
```
