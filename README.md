# Linux Configuration Project

The linux configuration project consists of configuring a virtual Linux machine and enabling it to host the item catalog project.

This README file displays the information necessary to access the virtual machine or public web site and the information necessary to complete the software installation and configuration steps performed.

## Table of Contents

* [IP Address and SSH Port](#ip-address-and-ssh-port)
* [Hosted Web Application](#hosted-web-application)
* [Summary of Softare Installed and Configuration Changes](#summary-of-softare-installed-and-configuration-changes)
* [Third-Party Resources](#third-party-resources)

## IP Address and SSH Port

* IP Address: 18.224.57.11 Port: 2200

## Hosted Web Application

* Web URL: http://18.224.57.11.xip.io 

## Summary of Softare Installed and Configuration Changes

1. Get your server.
    * Start a new Ubuntu Linux server instance on Amazon Lightsail.
    * Follow the instructions provided to SSH into your server.

2. Secure your server.
    * Update all currently installed packages.
        ```
        $ sudo apt-get update
        $ sudo apt-get upgrade
        $ sudo apt-get autoremove
        $ sudo apt-get dist-upgrade
        ```
    * Change the SSH port from 22 to 2200 (/etc/ssh/sshd_config)
    * Configure the Uncomplicated Firewall (UFW)
        ```
        $ sudo ufw status
        $ sudo ufw default deny incoming
        $ sudo ufw default allow outgoing
        $ sudo ufw allow 2200/tcp
        $ sudo ufw allow www
        $ sudo ufw allow ntp
        $ sudo ufw enable
        $ sudo ufw status
        ```

3. Give `grader` access.
    * Create a new user account named `grader`.
        ```
        $ sudo apt-get install finger
        $ sudo adduser grader
        $ finger grader
        ```
    * Give `grader` the permission to `sudo` (/etc/sudoers and /etc/sudoers.d/grader)
    * Create an SSH key pair for `grader` using the `ssh-keygen` tool (add to .ssh/authorized_keys)

4. Prepare to deploy your project.
    * Configure the local timezone to UTC.
    * Install and configure Apache to serve a Python mod_wsgi application.
        ```
        $ sudo apt-get install apache2
        $ sudo apt-get install libapache2-mod-wsgi
        $ sudo vi /etc/apache2/sites-enabled/0000-default.conf
        $ sudo apache2ctl restart
        $ sudo vi /var/www/html/myapp.wsgi
        ```
    * Install and configure PostgreSQL
        ```
        $ sudo apt-get install postgresql
        ```
    * Create a new database user named `catalog` that has limited permissions to your catalog application database.
        ```
        $ create database catalogdb;
        $ create user catalog with encrypted password '<PASSWORD>';
        $ grant all privileges on database catalogdb to catalog;
        ```
    * Install git
        ```
        $ sudo apt-get install git-core
        ```

5. Deploy the Item Catalog project.
    * Clone and setup your Item Catalog project from the Github repository
        ```
        $ cd /var/www/catalog
        $ git clone https://github.com/dwagg456/catalog.git
        $ sudo apt-get install python-sqlalchemy
        $ sudo apt-get install python psycopg2
        $ sudo apt-get install python-flask
        $ sudo apt-get install python-oauth2client
        $ sudo vi /etc/apache2/sites-available/catalog-app.conf
        $ sudo a2ensite catalog-app
        $ sudo service apache2 reload
        $ sudo vi /var/www/catalog/catalog.wsgi
        ```
    * Make sure your `.git` directory is not publicly accessible via a browser (/etc/apache2/apache2.conf).


## Third-Party Resources

The following resources were used in creating this project.
* [Debian-Administration](https://debian-administration.org/article/69/Some_upgrades_show_packages_being_kept_back)
* [Liquid Web | Knowledge Base](https://www.liquidweb.com/kb/install-git-ubuntu-16-04-lts)
* [Coding Blocks](https://medium.com/coding-blocks/creating-user-database-and-adding-access-on-postgresql-8bfcd2f4a91e)
* [BogoToBogo](https://www.bogotobogo.com/python/Flask/Python_Flask_HelloWorld_App_with_Apache_WSGI_Ubuntu14.php)
* [Digital Ocean](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps)
