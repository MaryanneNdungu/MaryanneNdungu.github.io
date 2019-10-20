---
layout: post
title: Install Laravel and its prerequisites from scratch on Ubuntu 18.04
image: /img/laravel.png
tags: [Laravel, install, php]
---
<br>
This is how I went about installing Laravel in my Ubuntu 18.04

<br>
## Install PHP(php 7.3) with Nginx
 
 To install the PHP and PHP FPM packages run the following command:
 >sudo apt install php-fpm
 
 Check the status of the PHP FPM service with:
 >systemctl status php7.3-fpm
 
 For more details, check out the tutorial [here](https://linuxize.com/post/how-to-install-php-on-ubuntu-18-04/)

<br>
## Install Nginx
 Update your local package index:
 >sudo apt update
 
 Install Nginx
 >sudo apt install nginx

Adjust Firewall:
>sudo ufw allow 'Nginx HTTP'

Check installation:
 >systemctl status nginx

For more information, checkout the tutorial [here](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-18-04-quickstart)

<br>
## Install mysql
Run the following commands: 

> sudo apt update

> sudo apt install mysql-server

> sudo mysql_secure_installation

For more info, checkout the tutorial [here](https://www.digitalocean.com/community/tutorials/how-to-install-mysql-on-ubuntu-18-04)

<br>
## Download composer

Download Composer

> php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"

> php -r "if (hash_file('sha384', 'composer-setup.php') === 'a5c698ffe4b8e849a443b120cd5ba38043260d5c4023dbf93e1558871f1f07f58274fc6f4c93bcfd858c6bd0775cd8d1') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"

> php composer-setup.php

>php -r "unlink('composer-setup.php');"

For more info, check out:
[https://getcomposer.org/download/](https://getcomposer.org/download/)

<br>
**Install composer locally/Globally**

Follow the instructions given at: [https://getcomposer.org/doc/00-intro.md](https://getcomposer.org/doc/00-intro.md)

<br>
## Install Laravel - via Laravel installer

Follow the instructions given at: [https://laravel.com/docs/6.x/installation](https://laravel.com/docs/6.x/installation)


**Add composer to $PATH**

Run the following command:

> export PATH=$PATH:$HOME/.config/composer/vendor/bin

<br>
## Ready? Run!

Create Laravel Project
> laravel new {project_name}

Run
> Run 'composer install'

> Run 'php artisan serve'

<br>
## Problems I encountered
I encountered this problem when I run 'composer install'

<code>
Your requirements could not be resolved to an installable set of packages
</code>

<br>
## Problem 1:
<code>Laravel/framework v6.3.0 requires ext-mbstring * -> the requested PHP extension mbstring is missing from your system.
 </code>
 
<br>
## Solution
Go ahead and install the php7.3 extension:

> sudo apt-get install php7.3-mbstring

<br>
## Problem 2:
<code>phpunit/phpunit 8.4.1 requires ext-dom * -> the requested PHP extension dom is missing from your system.
</code>

<br>
## Solution
Go ahead and install the php7.3 extension:
> sudo apt-get install php7.3-dom

**Run project again**
> Run 'compose install' again

> Run 'php artisan serve'

<br>
# More problems?
If you still encounter problems or get the server error 500 page (like I did), check the log files located at: projectFolder/storage/logs

**Error:** 
<code>No application encryption specified</code>

**Solution:**
 Chances are that you don't have .env file in your project.
 Go ahead and run 
 
 > cp .env.example .env
  
 Then generate the encryption key. Run the following commands:
 
 > php artisan key:generate
 
 > php artisan config:cache
  
**Ready? Run!**

Run

> php artisan serve

<br>
**Hopefully everything runs now!**
