## Yii2 ##

### Translate Messages ###

Extract message via
```
./yii message/extract frontend/messages/config.php 
./yii message/extract console/messages/config.php 
```

Then edit the translations

### Incorporate new releases (Yii2  and other libs) ###

Execute
```
composer update
./init
```

Then check the composer.lock for the changes