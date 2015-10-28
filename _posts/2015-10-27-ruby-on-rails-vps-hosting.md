---
layout: post
title: Upgrade Ruby on Rails on WHM / CPanel VPS hosting
---

I have ruby application that I need to deploy on-line. As I already have a VPS hosting account, it would save me some money if I can have my ruby application running on it. After running the setup script below to install ruby on Cpanel, I realized it only supports Ruby versions before 1.9

```bash
/scripts/installruby 
```

Also the Cpanel installed ruby on supports Mongrel but I needed it to work with Passenger. After searching on-line. I found this excellent guide: [How to use cPanel > Ruby on Rails area with Ruby 2 and Rails 4][1]. I followed the guide and did encounter a few problems. In this post, I will include solutions that I found apart from the guide, that has helped me successfully deploy my ruby application.

### Login to VPS
SSH as root to VPS server.

### Install Ruby
Replace Ruby with a newer version.
Goto [https://www.ruby-lang.org/en/downloads/][2] to get the latest version. Replace the URL in wget, tar and cd with the downloaded version.

```bash
cd /root
wget http://cache.ruby-lang.org/pub/ruby/2.1/ruby-2.1.3.tar.gz
tar xzf ruby-2.1.3
cd ruby-2.1.3
./configure
make && make install
ln -s /usr/local/bin/ruby /usr/bin/ruby
ln -s /usr/local/lib/ruby /usr/lib/ruby
```

### Install RubyGem
Replace RubyGem with a newer version.
Goto [https://rubygems.org/pages/download][3] to get latest version.
Replace the URL in wget, tar and cd with the downloaded version.

```bash
cd /root
wget [url]http://production.cf.rubygems.org/rubygems/rubygems-2.4.2.tgz[/url]
tar zxvf rubygems-2.4.2.tgz
cd rubygems-2.4.2
ruby setup.rb
ln -s /usr/local/bin/gem /usr/bin/gem
```

### Install Rails

```bash
gem install rails
ln -s /usr/local/bin/rails /usr/bin/rails
```

When running gem install rails, my VPS is stuck at rdoc. So I use this command: gem install rails --no-ri --no-rdoc

### Install Passenger
To install Phusion Passenger for Rails applications

```bash
gem install passenger
yum -y install libcurl-devel
passenger-install-apache2-module
```

When running gem install passenger, my VPS is stuck at rdoc. So I use this command: gem install passenger --no-ri --no-rdoc

Edit /usr/local/apache/conf/includes/pre_main_global.conf file:

```apacheconf
LoadModule passenger_module /usr/local/lib/ruby/gems/2.1.0/gems/passenger-4.0.53/buildout/apache2/mod_passenger.so
<IfModule mod_passenger.c>
     PassengerRoot /usr/local/lib/ruby/gems/2.1.0/gems/passenger-4.0.53
     PassengerDefaultRuby /usr/local/bin/ruby
</IfModule>
```

Restart Apache:

```bash
/etc/init.d/httpd restart
```

### Install NodeJS
Run commands below. Replace cd node-v* with the downloaded version.

```bash
cd /usr/local/src
wget http://nodejs.org/dist/node-latest.tar.gz
tar zxvf node-latest.tar.gz
cd node-v*
./configure
make && make install
```

At the make && make install, I encounter compile error. My VPS installed compiler version is outdated. 

[WARNING: C++ compiler too old, need g++ 4.8 or clang++ 3.4 (CXX=g++)]

Luckily I found a solution by [Kris Reeves][4] to update the C++ compiler. Run make && make install again.

## Install Additional Gems
These gems help run Ruby applications better, so I installed them.

```bash
gem install spring
gem install uglifier
gem install turbolinks
```

## Deploy ROR (Ruby on Rails) 
After all the above installation, creating ruby applications from Cpanel does not work anymore, it has to be done manually. I will be setting up a sub domain to run the application. For this guide, the sub domain will be rails.domain.com

The rails_apps folder created by the Cpanel install script still exist and we will use it to upload the code. My home directory looks like this:

```bash
public_html
rails_apps/myapp
public_html/myapp
```

Create .htaccess, change ownership and create symbolic link. Replace $USER with your username.

```bash
echo "RailsBaseURI /" >> /home/$USER/rails_apps/myapp/public/.htaccess
chown -R $USER:$USER /home/$USER/rails_apps/myapp/public
ln -s /home/$USER/rails_apps/myapp/public /home/$USER/public_html/myapp
```

## Enable Cpanel Compiler
If you have error running bundle install, make sure to enable all access to complier. Login to WHM panel as root, Search for compiler at the panel search box. Then enable access. Run bundle install again.

## Success!
I have successful deploy my rails application on my Cpanel/WHM server. It has been running without any down time since then. It is a [web analytics dashboard][5] using Google Analytics data. You can see it live here: [http://dash00.ukiki.me/][6]



[1]: https://forums.cpanel.net/threads/how-to-use-cpanel-ruby-on-rails-area-with-ruby-2-and-rails-4.433192/
[2]: https://www.ruby-lang.org/en/downloads/
[3]: https://rubygems.org/pages/download
[4]: http://krisreeves.com/code/compile-node-js-4-0-on-centos-6/
[5]: http://clchang.net/blog/internet-marketing/web-analytics-dashboard/
[6]: http://dash00.ukiki.me/