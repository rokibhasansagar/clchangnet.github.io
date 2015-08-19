---
layout: post
title: IonCube Loader on Amazon EC2
---

Installing IonCube Loader on Amazon EC2. This guide will set Fresh Store Builder which requires IonCube Loader to run. Fresh Store Builder is an Amazon Affiliate Store. The Amazon Machine Image will be Ubuntu 14.04


## Install apache, php, mysql, curl

```bash
sudo apt-get update
sudo apt-get upgrade
```

	sudo apt-get install apache2

	sudo apt-get install mysql-server
	sudo apt-get install php5-mysql

	sudo service apache2 restart
	sudo apt-get install php5 libapache2-mod-php5 php5-mcrypt

	sudo apt-get install php5-cgi php5-cli php5-common php5-curl php5-dbg php5-dev php5-gd

	sudo apt-get install unzip

## Install IonCube

	cd /usr/local/src
	sudo wget http://downloads3.ioncube.com/loader_downloads/ioncube_loaders_lin_x86-64.tar.gz
	sudo tar xvfz ioncube_loaders_lin_x86-64.tar.gz

	mkdir /usr/local/ioncube

	# still in /usr/local/src
	PHP_VERSION=$(php -r "echo PHP_MAJOR_VERSION.'.'.PHP_MINOR_VERSION;")
	sudo cp "ioncube/ioncube_loader_lin_${PHP_VERSION}.so" /usr/local/ioncube

	# modify php.ini
	sudo vi /etc/php5/apache2/php.ini

	# search for /Dymnamic
	<!-- using vi, type at :/Dynamic -->
	
	# Add
	zend_extension = /usr/local/ioncube/ioncube_loader_lin_5.5.so

	sudo service apache2 restart

	# to test if ioncube installed
	vi /var/www/html/testion.php

	# Edit testion.php
	<?php
	echo var_export(extention_loaded('ionCube Loader') ,true);

## Setup mod_rewrite

	sudo vi /etc/apache2/sites-available/000-default.conf
	
	#add
	<Directory /var/www/html>
	    AllowOverride All
	</Directory>

	sudo a2enmod rewrite
	sudo service apache2 restart

## Setup directories

```bash
# give correct ownership and permissions
# check by ls -la in the directory
sudo chown -R www-data:www-data /var/www/html
```

	# make sure your user is a member of the www-data group
	<!-- check by groups user -->
	sudo usermod -a -G www-data $USER

	# then give group permissions
	sudo chmod -R 775 /var/www/html

## Create database, user and password

	# login to mysql
	mysql -u root -p

	# create database
	create database databasename;
	show databases;

	# create user
	create user 'newuser'@'localhost' identified by 'password';
	grant all privileges on databasename.* to newuser@localhost;

	# check database connection
	<!-- type exit; to logout if logged in -->
	mysql -u newuser -p'password' databasename

## Fresh Store Builder installation

Upload freshstorebuilder zip files to /var/www/html/

	# unzip
	sudo unzip filename.zip

	# change ownership
	sudo chown -R www-data:www-data /var/www/html

Goto to url http://www.domain.com/install to complete Fresh Store Builder installation.
