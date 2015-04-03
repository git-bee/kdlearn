---
title: Drupal Setup and Installation for Beginners
author: Thien
date: 2014-07-15
categories: [content management systems, drupal, php]
---

# Drupal Setup and Installation for Beginners

Before you continue reading this guide why not try a more easy way, using the [Koding Package Manager (kpm)](http://learn.koding.com/guides/getting-started-kpm/) and install Drupal using just one command:

```
kpm install drupal
```

***

This article will cover the basic steps on how to setup a blog using 
Drupal.

## What's Drupal?

Drupal is a content management system (also refered to as CMS), meaning Drupal is used to manage content on informational sites, social media sites, member sites, intranets and web applications.

But how do you set up Drupal on [Koding](https://koding.com)? Well it's simple, you just follow the simple tutorial below step by step. After you've finished with the tutorial you'll have a brand new website that runs Drupal.

## Step-by-step Drupal

### Step 1.

Open up your [Koding](https://koding.com) Terminal and type in the following command to get started.

	cd Web

### Step 2.

Next up, you must download Drupal version that you're planing on using, in this case we are going to use [Drupal 8](https://www.drupal.org/node/572834). Git clone the latest dev (8.0.x) release in the current directory.

```
	git clone --branch 8.0.x http://git.drupal.org/project/drupal.git .
```

### Step 3

In order to Drupal working on Nginx, open up the default virtual host(block) file.

	sudo vi /etc/nginx/sites-available/default

The configuration should include the changes below (the details of the changes are under the config information):

```
	server {
	 
	    listen   80;
      server_name hostname.duythien.koding.io;
	 
	    index index.php index.html index.htm;
	    set $root_path '/home/duythien/Web/drupal-8.x-dev';
	    root $root_path;
	 
	    gzip_static on;

	    location = /favicon.ico {
	            log_not_found off;
	            access_log off;
	    }

	    location = /robots.txt {
	            allow all;
	            log_not_found off;
	            access_log off;
	    }

	    # Very rarely should these ever be accessed outside of your lan
	    location ~* \.(txt|log)$ {
	            allow 192.168.0.0/16;
	            deny all;
	    }
	    location ~ \..*/.*\.php$ {
	            return 403;
	    }

	    # No no for private
	    location ~ ^/sites/.*/private/ {
	            return 403;
	    }

	    # Block access to "hidden" files and directories whose names begin with a
	    # period. This includes directories used by version control systems such
	    # as Subversion or Git to store control files.
	    location ~ (^|/)\. {
	            return 403;
	    }

	    location / {
	            # This is cool because no php is touched for static content
	            try_files $uri @rewrite;
	    }
	    location @rewrite {
	        # You have 2 options here
	        # For D7 and above:
	        # Clean URLs are handled in drupal_environment_initialize().
	        rewrite ^ /index.php;
	        # For Drupal 6 and bwlow:
	        # Some modules enforce no slash (/) at the end of the URL
	        # Else this rewrite block wouldn't be needed (GlobalRedirect)
	        #rewrite ^/(.*)$ /index.php?q=$1;
	    }
	    location ~ \.php$ {
	        try_files $uri =404;
	        fastcgi_split_path_info ^(.+\.php)(/.+)$;
	        fastcgi_pass unix:/var/run/php5-fpm.sock;
	        fastcgi_index index.php;
	        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
	        include fastcgi_params;
	    }
	}
```

Save and Exit. If you want to run Drupal on Apache, you need to edit the Apache configuration file configuration Apache. If you cloned Drupal under the Web directory you can just skip this. More about Virtual Host files http://httpd.apache.org/docs/2.4/vhosts/examples.html.

```
	sudo vi /etc/apache2/sites-available/default
```

Drupal requires the gd library to be insalled.
```
	sudo apt-get install php5-gd
	sudo service apache2 restart
```

Then you'll need to create the database for Drupal, by running the following code.

```
	echo 'CREATE DATABASE drupal8' | mysql -u root -p
```

If you haven't installed MySQL, head on over to [this tutorial 
here](/guides/installing-mysql).

### Step 4

Return in the Drupal root directory if needed (~/Web in this example).

```
cd ~/Web
```

Make your own copy of the settings files, to be writed by the Drupal installer (single site install).

```
	cd sites/default
	cp default.services.yml services.yml
	cp default.settings.php settings.php
	sudo chmod a+w services.yml settings.php
```

Create the files directory and make it writeable. If you see this error  ```Writable (public download method)```, you need to run following code in the command line, on the Drupal directory (~/Web by default).

```
	mkdir files
 	sudo chmod a+w -R files 
```

Head now to your Koding domain (http://username.koding.io). If everything goes fine, you should see 
the following screen in your browser:

![](welcome.png)

Drupal will then ask you the database credentials to finish the installation.

That's it! You're now running Drupal on Koding! Have fun!

This has been a beginner guide from [Koding](https://koding.com/). As always, more Quick Tips and beginner friendly guides coming soon, so stay tuned!