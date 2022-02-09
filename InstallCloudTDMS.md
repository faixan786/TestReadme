# Setup CloudTDMS Free

* [Installing dependencies](#installing-dependencies)
* [Setup PHP](#Setup-PHP)
* [Installing MySQLServer](#Installing-MySQLServer)
* [Downloading and installing PHPMyAdmin](#Downloading-and-installing-PHPMyAdmin)
* [Removing downloaded folders](#Removing-downloaded-folders)
* [Installing composer](#Installing-composer)
* [Installing symfony](#Installing-symfony)
* [Installing CloudTDMS](#Installing-CloudTDMS)
* [Setup VHost](#Setup-VHost)

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
sudo mysql
```
Setup user and password for your datatbase
```
ALTER USER '<user>'@'<host>' IDENTIFIED WITH caching_sha2_password BY '<password>';
```
```
exit
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
cd 
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
cd
```
```
sudo mv .symfony/bin/symfony /usr/local/bin/symfony
```

## Installing CloudTDMS
```
cd
```
Create a new Symfony project
```
symfony new CloudTDMS --version=5.4
```
```
cd CloudTDMS
```
Install the dependencies for the project
```
composer require ext-json
composer require doctrine/orm
composer require doctrine/doctrine-bundle
composer require doctrine/doctrine-migrations-bundle
composer require knpuniversity/oauth2-client-bundle
composer require league/oauth2-google
composer require sensio/framework-extra-bundle
composer require symfony/asset
composer require symfony/expression-language
composer require symfony/form
composer require symfony/intl
composer require symfony/mime
composer require symfony/proxy-manager-bridge
composer require symfony/security-bundle
composer require symfony/twig-bundle
composer require symfony/validator
composer require twig/extra-bundle
composer require league/oauth2-github
```
Install the dev dependencies of the project for PreProd
```
composer require --dev doctrine/doctrine-fixtures-bundle
composer require --dev symfony/web-profiler-bundle
```
```
cd
```
Unzip the downloaded project
```
unzip CloudTDMS.zip -d TDMS
```
```
cp -R TDMS/CloudTDMS/. CloudTDMS/
```
```
cd CloudTDMS
```
Open the .env file
```
vi .env
```
Change the following line
```
DATABASE_URL="postgresql://symfony:ChangeMe@127.0.0.1:5432/app?serverVersion=13&charset=utf8"
```
to
```
DATABASE_URL="mysql://<user>:<password>@127.0.0.1:3306/CloudTDMS"
```
where user is the username and password is the password for your MySQL database. <br /><br /><br />


Setup the database by running the following command
```
symfony console app:setup-database
```

Moving the project to apache2
```
cd
```
```
sudo cp -R CloudTDMS /var/www/CloudTDMS
```
```
cd /var/www/
```
Change the permission to allow apache to read and write
```
sudo chown -R www-data:www-data CloudTDMS/
```


## Setup VHost
```
sudo vi /etc/apache2/sites-available/cloudtdms.conf
```

Change <your_dns> to your DNS in the following snippet and paste it in the file and save it
```
<VirtualHost *:80>
    ServerName <your_dns>
    ServerAlias <your_dns>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/CloudTDMS/public
    <Directory /var/www/CloudTDMS/public>
        FallbackResource /index.php
    </Directory>
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

Enable the apache config
```
sudo a2ensite cloudtdms.conf
```

Restart the apache server
```
sudo systemctl reload apache2
```
