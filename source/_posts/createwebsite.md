---
title: My first (static) website
date: 2020-06-28 20:56:13
tags: [web dev]
Category: 
- Code
comments: false
---
## Here was how I get started on creating my first static website!

This was my first trial at creating a website.

The I used Nginx to create a private server to host my static webpage on it. For web framework, I decided on using hexo as I wanted to focus more on writing prose instead of code. Hexo's support of using markdown as a default way to create posts is very convenient for me.

### Installation process

Installing hexos is quite easy if you are familiar with Nodejs.

First, you can install the command line interface. I also downloaded hexo-server and
hexo-browsersync for automatic browser reloading

```bash
$ npm install hexo-cli -g
$ npm install hexo-server —-save
$ npm install hexo-browsersync —-save
```

There are three different types of default layout for hexo's files:

1. Posts
2. Drafts
3. Pages

### Creating a new post

Since post is the default layout that axios uses, to create a new post to create a post, you can simply do:

```bash
$ hexo new <title of file>
```

Hexo has good documentation of all the basics of creating posts which can be found [here](https://hexo.io/docs/writing.html)

To create files of other layout you can just specify the layout type before the title

```bash
$ hexo new page <title of file>
```

You can also look at your webpage by using in localhost by using:

```bash
$ hexo server
```

After you have finished creating your posts, you can generate your static files into the public folder using the command
```bash
$ hexo generate
```
### Deploying your static folder to Nginx

In order to deploy the static folder to Nginx, push the static folder onto github first, then pulling the folder from Nginx. First, we have to edit the  _config.yml file that can be found in your blog's root directory.

Go to the bottom of the _config.yml file and add these information
Under the repo section, add the http link that you use to clone the repository as shown: 
```yml
# Deployment
Docs: https://hexo.io/docs/deployment.html
deploy: 
    type: git
    repo: https://github.com/<username>/<repo name>.git
    branch: master     
```

After that, you can deploy the static folder to the stipulated repo link. But we first
have to install hexo-deployer-git package to allow the static folder to be deployed to
git. Run these series of commands to deploy the folder successfully.

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
Then, add a basic server block to your nginx configuration to the directory /etc/nginx/sites-available/ 
For convenience, you can just copy the format of the server block file under /etc/nginx/sites-available/default

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

I hope this first blog post of mine was informative and have fun hacking!



