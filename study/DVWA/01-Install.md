# Install
```bash
dnf -y install mariadb-server php-fpm nginx php php-gd
git clone https://github.com/digininja/DVWA.git
mv DVWA dvwa
cd dvwa/config
/bin/cp -rf config.inc.php.dist config.inc.php
vim config.inc.php # edit config.inc.php file and set database and key
```
