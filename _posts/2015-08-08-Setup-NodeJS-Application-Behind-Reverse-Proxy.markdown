---
layout: post
title: "Setup Node.js Application And Use Nginx As Reverse Proxy"
data:   2015-08-08
categories: jekyll update
permalink: NodejsWithNgnix
comments: True
language: English
---

This is an introduction tutorial on how to setup Nginx as a Reverse Proxy and use PM2 to manage running Node.js application.


## Why we need these?

#### Reverse Proxy:

>A reverse proxy can distribute incoming requests across a group of servers as a "Load Balancer".
>Also it can be used to compress data and cache frequent requested content to improve the performance.
>Sitting between the outside network and your local private network, a reverse proxy
>protects your back-end servers' identities from the outside world against attacks.

#### PM2:

>PM2 is a production process manager for Node.js/io.js Applications with built-in load balancer.
>It's very easy to use and give you a very intuitive of your application's running condition. It will restart if your applications crash or being killed.

#### Node Application:

>Node.js is a JavaScript runtime environment that provides event-driven architecture and non-blocking I/O API. It's best used for I/O bound real-time web application.


## How to setup Node.js app and Reverse Proxy?

### Setup Node environment

I recommend using `nvm` to manage different Node version, it's like `rvm` for ruby.

- Install `nvm` :

      url -o- https://raw.githubusercontent.com/creationix/nvm/v0.26.0/install.sh | bash

- Then you can use `nvm` to install latest stable node version:

      nvm install stable

- You can always use `nvm help` to play around with `nvm`, and `nvm list` will show you what versions you have and what you are using right now.

### Start with Node Application

>For those of you who are not quite familiar with Node.js, I found a very concise and intuitive tutorial from [express](http://expressjs.com) to checkout out:
>
>Just follow the instruction to create a directory and install express: **[Installing](http://expressjs.com/starter/installing.html)**
>
>And here's a very simple **Hello World** example: **[Hello Node World](http://expressjs.com/starter/hello-world.html)**

### Using PM2 as a Process Manager to take care of Node App

- Install PM2, we need to use npm to install pm2.

      npm install pm2 -g

- Start your node.js app with pm2

      pm2 start app.js

- And you can also get current running node.js application's info and condition by

      pm2 list | pm2 monit

### Setup Nginx as a Reverse Proxy

- Install nginx on Ubuntu server

      sudo apt-get install nginx

- Modify nginx configuration file, I recommend to backup previous default file in order you need it later.

      sudo vim /etc/nginx/sites-available/default

  your file will look something like this, you can change listen port to any port you want:

      server {
         listen 80; # any port you want to use

         server_name your_server_name_or_ip

         location / {
             proxy_pass http://localhost:3000;    # assume your node.js app listening on port 3000
             proxy_http_version 1.1;
             proxy_set_header Upgrade $http_upgrade;
             proxy_set_header Connection 'upgrade';
             proxy_set_header Host $host;
             proxy_cache_bypass $http_upgrade;
         }
      }

- Restart Nginx Server:

      sudo service nginx restart

Now you are good to go. Just open your browser and type in the server name or ip (and port number if it's not 80 in nginx). You will see you Node.js running.

Thank you for reading, feel free to leave question or any feedbacks.

#### References:

* [https://en.wikipedia.org/wiki/Node.js](https://en.wikipedia.org/wiki/Node.js)
* [https://www.digitalocean.com/community/tutorials/how-to-set-up-a-node-js-application-for-production-on-ubuntu-14-04](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-node-js-application-for-production-on-ubuntu-14-04)
* [https://www.nginx.com/resources/glossary/reverse-proxy-server/](https://www.nginx.com/resources/glossary/reverse-proxy-server/)
* [https://github.com/Unitech/pm2](https://github.com/Unitech/pm2)
* [https://github.com/creationix/nvm](https://github.com/creationix/nvm)
