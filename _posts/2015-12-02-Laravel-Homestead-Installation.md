---
layout: post
title: Laravel Homestead Installation
---

Came across Laravel while looking for a PHP framework to build a custom web site project. First PHP framework I found was CodeIgniter, which lead me to Laravel. As Laravel is a newer framework, I decided to give it a try.

### Setting up 

There are two methods of setting Laravel. First is to have Linux, Apache, Mysql and PHP (plus Composer - PHP dependency manager) installed on your machine. Second is to install a virtual box to run Laravel. Virtual Box is similar to programs that allow you to use your Windows machine to run MacOs.

I chose to use virtual box method on my ElemenataryOs Linux Box. This way I don't have to configure my Linux Box with additional software installs. Below are the software required to setup: 

1. Virtual Box - [https://www.virtualbox.org/wiki/Downloads][1]
2. Vagrant (manage, set up one or more virtual machines)  - [http://www.vagrantup.com/downloads.html][2]
3. Homestead - git clone https://github.com/laravel/homestead.git Homestead



[1]: https://www.virtualbox.org/wiki/Downloads
[2]: http://www.vagrantup.com/downloads.html

Homestead is pre packaged Vagrant box that contains all the software required for Laravel development. Such as:

Ubuntu 14.04
Git
PHP 5.6 / 7.0
Xdebug
HHVM
Nginx
MySQL
Sqlite3
Postgres
Composer
Node (With PM2, Bower, Grunt, and Gulp)
Redis
Memcached (PHP 5.x Only)
Beanstalkd
Laravel Envoy
Blackfire Profiler

### Installing

Click on the downloaded file for Virtual Box and Vagrant to install.

Clone Homestead into your home directory. more info: [Cloning The Homestead Repository][3]


[3]: http://laravel.com/docs/5.1/homestead

### Add Vagrant Box

Once you have Vagrant installed, use console to:

```bash
vagrant box add laravel/homestead    
```

Setup your SSH keys. more info: [Setting Your SSH Key][3]

### Setup Homestead

Clone in to your home directory. Then run hash init.sh to initialize Homestead

```bash
git clone -b php-7 https://github.com/laravel/homestead.git Homestead
bash init.sh
```

#### Folders
Now you need to setup your folders for Laravel Development. Lets say folder is ~/webdev/laravel. Open up to edit Homestead.yaml - you can find it under ~/.homestead

Homestead.yaml
```vim
folders:
    - map: ~/webdev/laravel/
      to: /home/vagrant/laravel/

sites:
    - map: firstapp.app
      to: /home/vagrant/Laravel/firstapp/public
```

Edit folders:
This is where you tell how to find your app folders. You can create as many projects folders as you want - each folder as an app or web domain. 

Edit sites:
You would add entries here after you have generated your Laravel framework. So lets create our first project ...

### Create First Project

Change to your Homestead folder, Start up Vagrant ...

```bash
vagrant up
```
Note: To destroy the machine, you may use the vagrant destroy --force command

Login to Homestead Virtual Box

```bash
vagrant ssh
```

You should be able to login into the VirtualBox and greeted with a prompt below ...

```bash
vagrant@homestead:~/laravel$
```

Now you are in virtual linux box inside your linux machine. Inside the virtual box contains all the software packaged into Homestead ie Nginx, Mysql, Composer and etc ...

To create a new project, change into your desired directory. Lets call the project, firstapp.app

```bash
laravel new firstapp.app
```

Wait fro the process to finish and when done, congratulations you have created your new project ... Final steps to view your project on a browser.

### Final Steps

Edit Homestead.yaml, map your new app path

```
sites:
    - map: firstapp.app
      to: /home/vagrant/Laravel/firstapp/public

```

Edit hosts to tell browser where to find your new project. 

hosts
```vim
192.168.10.10   firstapp.app
192.168.10.10   mytodo.app
```

Fire up your browser and you should see the Laravel welcome screen!!! 

