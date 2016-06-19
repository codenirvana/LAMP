# LAMP

> Tested on Ubuntu 14.04 Server 

## First things first

* Update Server
```
sudo apt-get update
sudo apt-get upgrade
```

* Installing AMP
```
sudo apt-get install apache2
sudo apt-get install php5 libapache2-mod-php5 php5-mcrypt
sudo apt-get install mysql-server libapache2-mod-auth-mysql php5-mysql
sudo mysql_install_db
sudo mysql_secure_installation
```

* Make New Directory
```
sudo mkdir /opt/www
sudo mkdir /opt/www/WEBSITE
```

* Set Directory Permissions
```
sudo chown -R root:www-data /opt/www/
```

---

## Apache Setup

* Disable default and create new site
```
cd /etc/apache2/sites-available/
sudo a2dissite 000-default
sudo cp 000-default.conf MY_WEBSITE.conf
```

* Edit and enable site
```
sudo vim MY_WEBSITE.conf 
sudo a2ensite MY_WEBSITE.conf
```
> MY_WEBSITE.conf
```
<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /opt/www/WEBSITE
        ErrorLog ${APACHE_LOG_DIR}/WEBSITE.error.log
        CustomLog ${APACHE_LOG_DIR}/WEBSITE.access.log combined
</VirtualHost>
```

*  Configure apache
```
cd /etc/apache2/
sudo vim apache2.conf 
```
> apache2.conf
```
...
<Directory /opt/www/WEBSITE/>
        Options FollowSymLinks
        AllowOverride All
        Require all granted
</Directory>
...
```

* Enable mods as per need
```
sudo a2enmod rewrite
sudo a2enmod headers
sudo a2enmod expires
```

### Apache Security

* Hide Server Information from Header
```
sudo vim /etc/apache2/conf-available/security.conf
```
> security.conf
```
ServerTokens Prod
ServerSignature Off
```

* Hide PHP Information from Header
```
sudo vim /etc/php5/apache2/php.ini
```
> php.ini
```
display_errors = Off
expose_php = Off	
```

### Restart Apache

```
sudo service apache2 reload
```

---

## MySQL Setup

* Create New DataBase
```
CREATE DATABASE WEBSITE_DB;
```

* Create New User
```
CREATE USER 'USERNAME'@'localhost' IDENTIFIED BY 'PASSWORD';
GRANT ALL PRIVILEGES ON WEBSITE_DB.* TO 'USERNAME'@'localhost';
FLUSH PRIVILEGES;
```

---

## phpMyAdmin Setup

```
sudo apt-get install phpmyadmin
sudo php5enmod mcrypt
sudo service apache2 restart
```

### Secure your phpMyAdmin

* Allow .htaccess Overrides
```
sudo vim /etc/phpmyadmin/apache.conf
sudo service apache2 restart
```
> apache.conf
```
<Directory /usr/share/phpmyadmin>
    Options FollowSymLinks
    DirectoryIndex index.php
    AllowOverride All
    . . .
```

* Create an .htaccess File
```
sudo vim /usr/share/phpmyadmin/.htaccess
```
> .htaccess
```
AuthType Basic
AuthName "Restricted Files"
AuthUserFile /etc/phpmyadmin/.htpasswd
Require valid-user
```

* Create the .htpasswd file for Authentication
```
sudo apt-get install apache2-utils
sudo htpasswd -c /etc/phpmyadmin/.htpasswd USERNAME
```
> You will be prompted to select and confirm a password for the user you are creating.
