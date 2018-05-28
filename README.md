######Linux project- Catalog project######

**Objetive:** 

In this project the Linux virtual machine was set up and to load the the itemm catalog website (Preview Udacity project 3)
[GitHub] (https://github.com/tnino/Catalog-project-1)
to be configurated with the server. 

**The IP address and SSH port** 

 Public IP: 18.191.52.125 
 User name:ubuntu
 Port:2222
 
You can visit http://18.191.52.125 for the website deployed.

**These were the initial instructions provided, please follow them to get started:**

1. Launch your Virtual Machine with your Udacity account
2. Follow the instructions provided to SSH into your server
3. Create a new user named grader
4. Give the grader the permission to sudo
5. Update all currently installed packages
6. Change the SSH port from 22 to 2200
7. Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)
8. Configure the local timezone to UTC
9. Install and configure Apache to serve a Python mod_wsgi application
10. Install and configure PostgreSQL
11. Do not allow remote connections
12. Create a new user named catalog that has limited permissions to your catalog application database
13. Install git, clone and setup your Catalog App project (from your GitHub repository from earlier in the Nanodegree program) so that it functions correctly when visiting your server’s IP address in a browser. Remember to set this up appropriately so that your .git directory is not publicly accessible via a browser!

Follow al the instructions
Launch Virtual Machine
**Instructions for SSH access to the instance**

**Download Private Key below**
	
	Move the private key file into the folder ~/.ssh 
	(where ~ is your environment's home directory).

from where you saved the file, in this case the Downloads folder, type this command in your terminal:
	mv ~/Downloads/udacity_key.rsa ~/.ssh/

On your terminal type in :
	chmod 600 ~/.ssh/udacity_key.rsa
	ssh -i ~/.ssh/udacity_key.rsa root@18.191.52.125

Environment Information:
Public IP Address: 18.191.52.125

**Create a new user named Grader:**
	
	sudo adduser grader
	vim /etc/sudoers
	touch /etc/sudoers.d/grader
	vim /etc/sudoers.d/grader, 
type in grader 
	ALL=(ALL:ALL) ALL
		save and quit
	Set ssh login using keys
	generate keys on local machine usingssh-keygen  
	then save the private key in ~/.ssh on local machine

**Deploy public key on developement enviroment**

On you virtual machine:
	$ su - grader
	$ mkdir .ssh
	$ touch .ssh/authorized_keys
	$ vim .ssh/authorized_keys
Copy the public key generated on your local machine to this file and save
	$ chmod 700 .ssh
	$ chmod 644 .ssh/authorized_key
reload SSH using service
	ssh restart

Now you can use ssh to login with the new user you created
	ssh -i [privateKeyFilename] grader@172-26-5-48

**Update all currently installed packages**

	sudo apt-get update
	sudo apt-get upgrade
	
Change the SSH port from 22 to 2200
	Use sudo vim /etc/ssh/sshd_config and then change Port 22 to Port 2200 , save & quit.

Reload SSH using sudo service ssh restart
	Configure the Uncomplicated Firewall (UFW)
	Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)
	sudo ufw allow 2200/tcp
	sudo ufw allow 80/tcp
	sudo ufw allow 123/udp
	sudo ufw enable 
	Configure the local timezone to UTC
	Configure the time zone sudo dpkg-reconfigure tzdata
	It is already set to UTC.
	Install and configure Apache to serve a Python mod_wsgi application
	Install Apache sudo apt-get install apache2
	Install mod_wsgi sudo apt-get install python-setuptools libapache2-mod-wsgi
	Restart Apache sudo service apache2 restart
	Install and configure PostgreSQL
	Install PostgreSQL sudo apt-get install postgresql

**Check if no remote connections are allowed** 
	
	sudo vim /etc/postgresql/9.5/main/pg_hba.conf

**Login as user "postgres"** 
	
	sudo su - postgres

**Get into postgreSQL** 
	
	shell psql

**Create a new database named catalog and create a new user named catalog in postgreSQL shell**
	
	postgres=# CREATE DATABASE catalog;
	postgres=# CREATE USER catalog;
	Set a password for user catalog

postgres=# ALTER ROLE catalog WITH PASSWORD 'password'
Give user "catalog" permission to "catalog" application database

postgres=# GRANT ALL PRIVILEGES ON DATABASE catalog TO catalog;
	Quit postgreSQL postgres=# \q

Exit from user  "postgres"
	exit

**Installing Git and clone process:**
	
	sudo apt-get install git
	cd /var/www to move to the /var/www directory

**Create the application directory** 
	
	sudo mkdir FlaskApp

Move inside this directory using 
	cd FlaskApp
**Clone the Catalog App to the virtual machine** 
	
	git clone https://github.com/tnino/Catalog-project-1
	
Rename the project's name 
	sudo mv ./catalog ./FlaskApp
Move to the inner FlaskApp directory 
	cd FlaskApp
Rename catalog.py to __init__.py 
	sudo mv catalog.py __init__.py
Edit database_setup_catalog.py and change engine = create_engine('sqlite:///toyshop.db') to engine = create_engine('postgresql://catalog:password@localhost/catalog')====

**Install pip:**
	
	sudo apt-get install python-pip
Use pip to install dependencies: 
	sudo pip install -r requirements.txt
Install psycopg2:
	sudo apt-get -qqy install postgresql python-psycopg2
Create database schema:
	sudo python database_setup_catalog.py

**Enable a New Virtual Host**

**Create FlaskApp.conf to edit:** 

	sudo nano /etc/apache2/sites-available/FlaskApp.conf

You will need to copy and paste the following lines of code to the file to configure the virtual host:

    <VirtualHost *:80>
	ServerName 18.221.209.63
	ServerAdmin pphang804@gmail.com
	WSGIScriptAlias / /var/www/FlaskApp/flaskapp.wsgi
	<Directory /var/www/FlaskApp/FlaskApp/>
		Order allow,deny
		Allow from all
	</Directory>
	Alias /static /var/www/FlaskApp/FlaskApp/static
	<Directory /var/www/FlaskApp/FlaskApp/static/>
		Order allow,deny
		Allow from all
	</Directory>
	ErrorLog ${APACHE_LOG_DIR}/error.log
	LogLevel warn
	CustomLog ${APACHE_LOG_DIR}/access.log combined
  </VirtualHost>

Enable the virtual host with:
	sudo a2ensite FlaskApp

**Create the .wsgi File under:
	
	/var/www/FlaskApp:
	cd /var/www/FlaskApp
	sudo nano flaskapp.wsgi 

You will need to copy and paste the following lines of code to the file to the flaskapp.wsgi file:

	#!/usr/bin/python
	import sys
	import logging
	logging.basicConfig(stream=sys.stderr)
	sys.path.insert(0,"/var/www/FlaskApp/")

In theFlaskApp import app as application ass yourr secret key
	application.secret_key = 'Add your secret key'

**You need to restart Apache:**

	 sudo service apache2 restart

**Software/Packages that you will use and need to install during this project:**

	Github
	Postgresql
	Apache2
	Python mod_wsgi

**References:**

https://devops.profitbricks.com/tutorials/deploy-a-flask-application-on-ubuntu-1404/

https://www.ubuntu.com/server

https://blog.jsinh.in/how-to-install-and-use-postgresql-on-ubuntu-14-04/

https://services.github.com/on-demand/github-cli/clone-repo-cli

https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps

https://stackoverflow.com/questions/31809786/django-wsgi-setup-causing-permission-denied-issues-on-centos-7

https://community.ubuntu.com/c/support-help-requests

https://stackoverflow.com/questions/31809786/django-wsgi-setup-causing-permission-denied-issues-on-centos-7

https://discussions.udacity.com/t/issue-getting-my-application-to-run-on-linux-server/473507/80

