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
`a2enmod env rewrite dir mime headers setenvif ssl`
- Now, Restart, Enable and Check Apache is Running Properly.
```
systemctl restart apache2
systemctl enable apache2
```
- 


