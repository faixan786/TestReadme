# Setup CloudTDMS Free

* [Installing dependencies](#installing-dependencies)
* [Setup PHP](#Setup-PHP)
* [Installing MySQLServer](#Installing-MySQLServer)
* [Downloading and installing PHPMyAdmin](#Downloading-and-installing-PHPMyAdmin)
* [Removing downloaded folders](#Removing-downloaded-folders)
* [Installing composer](#Installing-composer)
* [Installing symfony](#Installing-symfony)
* [Installing CloudTDMS](#Installing-CloudTDMS)

## Installing dependencies

```
sudo apt update -y
```
```
sudo apt upgrade -y
```
```
sudo apt install apache2 unzip software-properties-common -y
```

## Setup PHP

```
sudo add-apt-repository ppa:ondrej/php -y
```
```
sudo apt update -y
```
```
sudo apt install php8.0 libapache2-mod-php8.0 -y
```
```
sudo apt install php8.0-mysql php8.0-xml -y
```

## Installing MySQLServer
```
sudo apt install mysql-server -y
```
```
echo "ALTER USER 'root'@'localhost' IDENTIFIED WITH caching_sha2_password BY 'password';" | sudo mysql
```

## Downloading and installing PHPMyAdmin
```
wget https://files.phpmyadmin.net/phpMyAdmin/5.1.1/phpMyAdmin-5.1.1-all-languages.zip
```
```
unzip phpMyAdmin-5.1.1-all-languages.zip
```
```
cd phpMyAdmin-5.1.1-all-languages/
```
```
sudo mkdir /var/www/html/phpmyadmin/
```
```
sudo mv * /var/www/html/phpmyadmin/
```
```
sudo systemctl restart apache2
```

## Removing downloaded folders
```
cd ~
```
```
sudo rm -rf phpMyAdmin-5.1.1-all-languages.zip
```
```
sudo rm -rf phpMyAdmin-5.1.1-all-languages
```

## Installing composer
```
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
```
```
php composer-setup.php
```
```
php -r "unlink('composer-setup.php');"
```
```
sudo mv composer.phar /usr/local/bin/composer
```

## Installing symfony
```
wget https://get.symfony.com/cli/installer -O - | bash
```
```
sudo mv /home/${USER}/.symfony/bin/symfony /usr/local/bin/symfony
```

## Installing CloudTDMS
```
cd /var/www/html/
```
```
sudo git clone https://github.com/Cloud-Innovation-Partners/CloudTDMS_SaaS_Free.git
```
```
cd CloudTDMS_SaaS_Free/
```
```
sudo echo "DATABASE_URL=\"mysql://root:password@127.0.0.1:3306/CloudTDMS?serverVersion=mariadb-10.4.21\"" >> .env.local
```
```
sudo composer install
```
```
symfony console app:setup-database
```
