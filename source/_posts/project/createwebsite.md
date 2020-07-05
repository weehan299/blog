---
title: My first (static) website
date: 2020-06-28 20:56:13
tags: 
- webdev
categories: 
- Project
intro: "Here are some steps you can take to create your own static website too"
comments: false
---

## Here was how I get started on creating my first static website!

I used Nginx to create a private server to host my static webpage on it. For web framework, I decided on using hexo as I wanted to focus more on writing prose instead of code. Hexo's support of using markdown as a default way to create posts is very convenient for me.

### Setting up a VPS Server
I use an VPS server to store the static files of my website. All you need to do is to find any VPS hosting services online. I use [vultr](https://www.vultr.com/) which has a very user friendly set up process. 

All you have to do is to specify the type of server that you want I chose a Debian instance of the server. Then I selected the cheapest server size, you do not need a large server if you do not anticipate a lot of traffic to your website. After that, you just need to specify the username which you will use to ssh into the server and you are done. 

### Getting a domain name
Next you will need a domain name for your website. This service is also provided by a slew DNS providers online. 

I chose [epik.com](https://www.epik.com/) As it is also very easy to configure and link the domain name to your VPS server. Find a domain name that you like and buy it. Common domain suffixes like ".com" or ".net" are usually quite expensive. Therefore i chose an unusual one ".xyz" which only costs me $2 per year. 

After that, link your VPS server to your domain name by going to the DNS records and edit which IP address the domain name points to. If you try to enter the URL now, you should see a Page Not Found error (instead of seeing an advertisement for Epik). Page Not Found error that indicates that you have successfully linked the domain name to your VPS server, given that you have not added anything to your server. 

### Hexo Installation process

Installing hexos is quite easy if you are familiar with Nodejs. First, you can install the command line interface. 

I downloaded hexo-server and hexo-browsersync too which are tools for automatic browser reloading.

```bash
$ npm install hexo-cli -g
$ npm install hexo-server —-save
$ npm install hexo-browsersync —-save
```

### Creating a new post

There are three different types of default layout for hexo's files:

1. Posts
2. Drafts
3. Pages

Since post is the default layout that axios uses, to create a new post to create a post, you can simply do:

```bash
$ hexo new <title of file>
```

Hexo has good documentation of all the basics of creating posts which can be found [here](https://hexo.io/docs/writing.html)

To create files of other layout you can just specify the layout type before the title.

```bash
$ hexo new page <title of file>
```

You can also look at your webpage by using in localhost by using:

```bash
$ hexo server
```


After you have finished creating your posts, you can generate your static files into the public folder using the command:
```bash
$ hexo generate
```

### Deploying your static folder to Nginx

In order to deploy the static folder to Nginx, we have to push the static folder onto github first, then pull the folder from Nginx. First, we have to edit the \_config.yml file that can be found in your blog's root directory.

Go to the bottom of the \_config.yml file
Under the repo section, add the http link that you use to clone the repository as shown: 

```yml
# Deployment
Docs: https://hexo.io/docs/deployment.html
deploy: 
    type: git
    repo: https://github.com/<username>/<repo name>.git
    branch: master     
```

After that, you can deploy the static folder to the stipulated repo link. But we first have to install hexo-deployer-git package to allow the static folder to be deployed to git. Run these series of commands to deploy the folder successfully.

```bash
$ npm install hexo-deployer-git --save
$ hexo clean
$ hexo generate
$ hexo deploy
```

### configure your ssh server and set up Nginx

First, ssh into your private server and create a new directory. After that pull your static folder repo into your blog's folder.
```bash
$ mkdir -p /var/www/<blogname>
$ cd /var/www/blogname
$ git clone <repo link>
```
Then, add a basic server block to your nginx configuration to the directory: /etc/nginx/sites-available/ 
For convenience, you can just copy the format of the server block file under:  /etc/nginx/sites-available/default

The server block should look something like this: 
```
server {
        listen 80;
        listen [::]:80;

        root /var/www/<blogname>/<reponame>;
        index index.html index.htm index.nginx-debian.html;

        server_name <yourdomainname>.com www.<yourdomainname>.com;

        location / {
                try_files $uri $uri/ =404;
        }
}
```
After this, all you need to do is to restart Nginx, and you are done!

```bash
sudo systemctl restart nginx
```

Actually setting up your own static website is not as hard as it seems. A lot of the low level configuration have been abstracted away. All you need to do is to key in some details and you can get your own website for a resonable price.


