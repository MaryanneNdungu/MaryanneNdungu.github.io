---
layout: post
title: Basic Configuration Set Up for your new VPS (Ubuntu 18.04)
image: /img/cloud_vps.png
tags: [VPS, virtual private server, vps hosting, web hosting]
---
<br>


<br>
## Prerequisites

 >VPS and its root credentials
 
 >Domain Name(s) (Optional at this stage)

<br>
## Create a non root user
Run the following commands: 

>sudo adduser <username>
>sudo adduser jane

>sudo usermod -aG sudo jane // add to sudo group

>su - jane // test

>exit // exit to go back to root user

<br>

## Install nginx
Run the following commands: 

>sudo apt-get update

>sudo apt-get install nginx

>sudo ufw allow 'Nginx HTTP'

>sudo systemctl enable nginx

**Start Nginx

>systemctl start nginx

<br>
## Allow Nginx through the firewall

Check if HTTP and HTTPS ports are allowed by your firewall rules with the following command:

>firewall-cmd --list-all

If TCP ports 80 and 443 are not in the list of open ports, allow them in firewalld rules via the following commands:

>firewall-cmd --permanent --zone=public --add-port=80/tcp

>firewall-cmd --permanent --zone=public --add-port=443/tcp

<br>
## Configure Virtual host in Nginx to host your domain(/etc/nginx/conf.d/virtual.conf)

<code>
server {
    listen 80;
    root /opt/htdocs/binhong;
    index index.html index.htm;
    server_name binhong.me;
    location / {
        try_files $uri $uri/ =404;
    }
}
</code>

**All the best with your new VPS!**
