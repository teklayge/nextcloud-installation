# nextcloud-installation

Nextcloud installation on a new Ubuntu 24.04 server

Nextcloud Hub is the industry-leading, fully open-source, on premise content collaboration platform. Teams access, share and edit their documents, chat and participate in video calls and manage their mail and calendar and projects across mobile, desktop and web interfaces. More information can be found at (https://nextcloud.com/about/)

I am new to nextcloud and followed the the steps presented at [Install Nextcloud on Ubuntu 24.04 LTS – Complete Guide](https://mailserverguru.com/install-nextcloud-on-ubuntu-24-04-lts/). I have outlined the steps as follows.

The core system of Nextcloud requires **Apache**, **MariaDB**, and **PHP** to be installed. Their installation and configuration procedures are presented in the following sections.

## Step 1: Update Ubuntu packages

`sudo apt update && apt upgrade -y`

## Step2: Install Apache2

```
sudo apt install apache2 -y
```
## Step3: Install PHP modules and dependencies

- Visit [PHP Modules & Configuration](https://docs.nextcloud.com/server/latest/admin_manual/installation/php_configuration.html/) for more info. Use `php -m` from CLI to list all the installed PHP modules.
```
sudo apt install php php-common libapache2-mod-php php-bz2 php-gd php-mysql \
     php-curl php-mbstring php-imagick php-zip php-common php-curl php-xml \
     php-json php-bcmath php-xml php-intl php-gmp zip unzip wget -y
```

## Step 4: Enable required Apache modules

For Nextcloud to work correctly, we need `mod_env`, `mod_rewrite`,`mod_headers`, `mod_env`, `mod_dir` and `mod_mime` modules.
Enable them by running:
```
sudo a2enmod env
sudo a2enmod rewrite
sudo a2enmod dir
sudo a2enmod mime
sudo a2enmod headers
sudo a2enmod setenvif
sudo a2enmod ssl
```
Now, restart, enable and check if Apache is running.
```
sudo systemctl restart apache2
sudo systemctl enable apache2
sudo systemctl status apache2
```
Check modules are loaded on Apache (Output included)
```
tek@tek-OptiPlex-3020:~# sudo apache2ctl -M
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
## Step 5: Install and configure MariaDB server
```
sudo apt install mariadb-server -y
```
To start the MySQL command line mode use the following command:
`sudo mysql`

Create batabase and user for Nextcloud and set user permissions. Here, the database, the username and the password are all`nextcloud`.
```
CREATE USER 'nextcloud'@'localhost' IDENTIFIED BY 'nextcloud';
CREATE DATABASE nextcloud CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
GRANT ALL PRIVILEGES ON nextcloud.* TO 'nextcloud'@'localhost';
FLUSH PRIVILEGES;
quit;

```
Now, restart and enable MariaDB service. Also type `systemctl status mariadb` to check MariaDB is running.
```
sudo systemctl restart mariadb
sudo systemctl enable mariadb
```

## Step 6: Install Nextcloud

Three steps are neede to install NextCloud from CLI.

**Download NextCloud and unpack nextcloud**

Download the archive of the latest Nextcloud version from https://download.nextcloud.com/server/releases/latest.zip. Visit [releases](https://download.nextcloud.com/server/releases/) for all releases of NextCloud. I downloaded to and unzip it from my web directory.
```
cd /var/www/html
wget https://download.nextcloud.com/server/releases/latest.zip
unzip latest.zip
```

**Change the ownership**

Change the ownership of the nextcloud directory to the HTTP user.
```
sudo chown -R www-data:www-data /var/www/html/nextcloud/
```
**Install nextcloud server**

Use the occ command to complete the installation. This takes the place of running the graphical Installation Wizard.
The `admin-user` and `admin-pass` are used to login to the NextCloud server. These credentials can be configured from the graphical installation if required.
```
cd /var/www/html/nextcloud
sudo -u www-data php occ  maintenance:install --database \
"mysql" --database-name "nextcloud"  --database-user "nextcloud" --database-pass \
'nextcloud' --admin-user "admin" --admin-pass "admin"
```
Nextcloud’s `occ` command is Nextcloud’s command-line interface. We can perform many common server operations with occ, such as installing and upgrading Nextcloud, manage users, encryption, passwords, LDAP setting, and more. There is a lot more about `occ` command. Visit [Using the occ command](https://docs.nextcloud.com/server/latest/admin_manual/occ_command.html#command-line-installation-label/) of the official documentation for more details.

## Step 7: Configure Apache

Configure Apache to load Nextcloud from the /var/www/html/nextcloud folder. I edited `/etc/apache2/sites-enabled/000-default.conf` file to add the following Virtual Host information for my nextcloud server. 
```  
<VirtualHost *:80>
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
Now restart Apache for the change to take effect 
```
sudo service apache2 restart
```

Now NextCloud server can be accessed at `http://localhost/nextcloud`



  




