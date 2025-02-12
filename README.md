# nextcloud-installation
Nextcloud installation on Ubuntu 24.04 server

# OPtion 1: [Example installation on Ubuntu 20.04 LTS](https://docs.nextcloud.com/server/20/admin_manual/installation/example_ubuntu.html/)
- install packages required by nextcloud
```
sudo apt update
sudo apt install apache2 mariadb-server libapache2-mod-php7.4
sudo apt install php7.4-gd php7.4-mysql php7.4-curl php7.4-mbstring php7.4-intl
sudo apt install php7.4-gmp php7.4-bcmath php-imagick php7.4-xml php7.4-zip
```
- Now you need to create a database user and the database itself by using the MySQL command line interface. The database tables will be created by Nextcloud when you login for the first time.
```
sudo /etc/init.d/mysql start
sudo mysql -uroot -p
```
```
CREATE USER 'nextcloud'@'localhost' IDENTIFIED BY 'nextcloud';
CREATE DATABASE IF NOT EXISTS nextcloud CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
GRANT ALL PRIVILEGES ON nextcloud.* TO 'nextcloud'@'localhost';
FLUSH PRIVILEGES;
```
# OPtion 2: [Install Nextcloud on Ubuntu 24.04 LTS – Complete Guide] (https://mailserverguru.com/install-nextcloud-on-ubuntu-24-04-lts/)
### Install Apache2 and PHP Modules.
- install apache2
```
apt install apache2 -y
```
- install php modules
```
apt install php php-common libapache2-mod-php php-bz2 php-gd php-mysql \
php-curl php-mbstring php-imagick php-zip php-common php-curl php-xml \
php-json php-bcmath php-xml php-intl php-gmp zip unzip wget -y
```
- Enable required Apache modules
```
a2enmod env rewrite dir mime headers setenvif ssl
```
- Now, Restart, Enable and Check Apache is Running Properly.
```
systemctl restart apache2
systemctl enable apache2
```
- Check modules are loaded on Apache (Output included)
```
root@tek-OptiPlex-3020:~# apache2ctl -M
AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.1.1. Set the 'ServerName' directive globally to suppress this message
Loaded Modules:
 core_module (static)
 so_module (static)
 watchdog_module (static)
 http_module (static)
 log_config_module (static)
 logio_module (static)
 version_module (static)
 unixd_module (static)
 access_compat_module (shared)
 alias_module (shared)
 auth_basic_module (shared)
 authn_core_module (shared)
 authn_file_module (shared)
 authz_core_module (shared)
 authz_host_module (shared)
 authz_user_module (shared)
 autoindex_module (shared)
 deflate_module (shared)
 dir_module (shared)
 env_module (shared)
 filter_module (shared)
 headers_module (shared)
 mime_module (shared)
 mpm_prefork_module (shared)
 negotiation_module (shared)
 php_module (shared)
 reqtimeout_module (shared)
 rewrite_module (shared)
 setenvif_module (shared)
 socache_shmcb_module (shared)
 ssl_module (shared)
 status_module (shared)
root@tek-OptiPlex-3020:~#
```
### Install and Configure MariaDB Server
- install mariadb-server
```
apt install mariadb-server -y
```
- type `mysql` to Login to MariaDB
- Create Database and User for Nextcloud and Provide User Permissions.
```
CREATE USER 'nextcloud'@'localhost' IDENTIFIED BY 'nextcloud';
CREATE DATABASE nextcloud CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
GRANT ALL PRIVILEGES ON nextcloud.* TO 'nextcloud'@'localhost';
FLUSH PRIVILEGES;
quit;

```
- Now, restart and enable MariaDB service.
   ```
   systemctl restart mariadb
   systemctl enable mariadb
   ```
- type `systemctl status mariadb` to check MariaDB is running.
### Download Nextcloud, Unzip and Permission
- Download and unzip in the /var/www/html folder
  ```
  cd /var/www/html
  wget https://download.nextcloud.com/server/releases/latest.zip
  unzip latest.zip
  ```
- Change the ownership of the nextcloud directory to the HTTP user.
  ```
  chown -R www-data:www-data /var/www/html/nextcloud/
  ```
### Install Nextcloud From Command Line
- Run the below command to install nextcloud (provide your own credentials)
  ```
  cd /var/www/html/nextcloud
  sudo -u www-data php occ  maintenance:install --database \
  "mysql" --database-name "nextcloud"  --database-user "nextcloud" --database-pass \
  'nextcloud' --admin-user "tek" --admin-pass "1qazxsw2"
  ```
- Nextcloud allows access only from localhost, add a trusted ip or domain
  ```
  vi /var/www/html/nextcloud/config/config.php

  'trusted_domains' =>
  array (
    0 => 'localhost',
    1 => 'nc.mailserverguru.com',   // we Included the Sub Domain
  ),
  
 :x
 ```
- Configure Apache to load Nextcloud from the /var/www/html/nextcloud folder. I used port `8080`.
```
vi /etc/apache2/sites-enabled/000-default.conf

<VirtualHost *:8080>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html/nextcloud
        
        <Directory /var/www/html/nextcloud>
            Options Indexes FollowSymLinks
            AllowOverride All
            Require all granted
	      </Directory>
        
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
- Now restart Apache
- access nextcloud (http://localhost:8080)


  




