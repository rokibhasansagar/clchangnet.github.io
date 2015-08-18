---
layout: post
title: Nginx, Wordpress on Amazon EC2
---

This guide shows how to setup nginx web server on Amazon EC2 and all related software for running wordpress with a contact form page. Using Postfix to deliver contact form submission through another SMTP server.

## Setup applications

    sudo apt-get update
    sudo apt-get upgrade

    sudo apt-get install nginx

    sudo apt-get install mysql-server
    sudo apt-get install php5-mysql

    sudo apt-get install php5-fpm

    sudo apt-get install unzip

<!-- sudo apt-get install php5-mcrypt -->

    # enable php5-gd for captcha
<!-- sudo /etc/init.d/php5-fpm restart -->
    sudo apt-get install php5-gd

## Configure nginx
<!-- error logs - /var/log/nginx -->

Edit default
    sudo vi /etc/nginx/sites-enabled/default

Change 'root /usr/share/nginx/html;' to 'root /var/www/html;'
Add index.php to 'index index.php index.html index.htm;'

Uncomment

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    location ~ \.php$ {
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
    #   # NOTE: You should have "cgi.fix_pathinfo = 0;" in php.ini
    #
    #   # With php5-cgi alone:
        fastcgi_pass 127.0.0.1:9000;
    #   # With php5-fpm:
    #   fastcgi_pass unix:/var/run/php5-fpm.sock;
        fastcgi_index index.php;
        include fastcgi_params;
    }

Fix bad gateway error

    sudo vi /etc/php5/fpm/pool.d/www.conf

Change listen = /var/run/php5-fpm.sock to listen = 127.0.0.1:9000

    sudo service php5-fpm restart

Create directory
    
    sudo mkdir -p /var/www/html

Give correct ownership and permissions
<!-- check by ls -la in the directory -->

    sudo chown -R www-data:www-data /var/www/html

Make sure your user is a member of the www-data group
<!-- check by groups user -->

    sudo usermod -a -G www-data $USER

Then give group permissions

    sudo chmod -R 775 /var/www/html

Restart nginx

    sudo service nginx restart

## Setup mysql

Login to mysql

    mysql -u root -p

Create database

    create database databasename;
    show database;

Create user

    create user 'newuser'@'localhost' identified by 'password';
    grant all privileges on databasename.* to newuser@localhost;

Check database connection
<!-- type exit; to logout if logged in -->

    mysql -u newuser -p'password' databasename

## DNS settings

Setup DNS in your domain registrar to point to Amazon EC2

url forwarding <-- namecheap
@ --> elastic ip --> A
www --> elastic ip --> A

## Transfer files and database

Transfer public_html
<!-- zip oldsite public_html, move zip file to newsite public_html dir -->

    wget http://site_url/public_html.zip
    sudo chown -R www-data:www-data /var/www/html
    sudo chmod -R 775 /var/www/html

<!-- move dir -->

    mv --help
    mv -f /unzipdir/* /newdir

Import database

    mysql -u username -p databasename < database.sql

## Setup wordpress

Setup database name and password in your wp-config.php

Permalinks 404 error - edit /etc/nginx/sites-available/default

Look for 'location / {'
    
    # comment
    # try_files $uri $uri/ =404;

add

    try_files $uri $uri/ /index.php?$args;

## Setup Postfix email
<!-- for wordpress wp_mail to work -->
<!-- For this function to work, the settings SMTP and smtp_port (default: 25) need to be set in your php.ini file. -->

Install postfix

    $ sudo apt-get update
    $ sudo apt-get install postfix

Open port 25 on Amazon EC2

Setup password file

    sudo nano /etc/postfix/sasl_passwd

Add line

    [mail.isp.example] username:password

    sudo postmap /etc/postfix/sasl_passwd
    sudo chown root:root /etc/postfix/sasl_passwd /etc/postfix/sasl_passwd.db
    sudo chmod 0600 /etc/postfix/sasl_passwd /etc/postfix/sasl_passwd.db

Setup SMTP

    sudo vi /etc/postfix/main.cf

Edit

    relayhost = [mail.domain.com]

Add after inet_protocols

    # enable SASL authentication
    smtp_sasl_auth_enable = yes
    # disallow methods that allow anonymous authentication.
    smtp_sasl_security_options = noanonymous
    # where to find sasl_passwd
    smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
    # Enable STARTTLS encryption
    smtp_use_tls = no
    # where to find CA certificates
    smtp_tls_CAfile = /etc/ssl/certs/ca-certificates.crt

    sudo service postfix restart

    sudo mailq - see mail queue
    sudo postsuper -d ALL - remove mail in queue

## FINAL - File permissions 

    sudo find /var/www/html -type f -exec chmod 0644 {} \;
    sudo find /var/www/html -type d -exec chmod 0755 {} \;


