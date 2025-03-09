1. Start an AWS Server.

2. Putty/SSH into the Server

3. Set Up Server

   3.0. Apply Updates
	apt update && apt upgrade
	sudo reboot

   3.1. Create Project Directory in / {root}
	- project-name
	  - src/
	  - site/
	    - logs/
	    - public/
	      - media/
	      - static/

   3.2. Install PIP and Setup VirtualEnv
	apt install python3-pip
	pip3 install virtualenv
	virtualenv venv -p python3
	source venv/bin/activate
	pip install django
	pip install --upgrade pip
	pip install --upgrade setuptools
	apt-get install build-essential				 

4. Create a Django Project
   cd src
   django-admin startproject some-project-name . 
   Add server's IP address to 'settings.py' 'Allowed Host' Constant
   Run the django development server
      python manage.py runserver 0.0.0.0:8000
   Open a WebBrowser, goto <your-IP-Address>:8000

5. Install MySql
   sudo apt install mysql-server
   mysql_secure_installation
   mysql
     CREATE USER "username"@'localhost' IDENTIFIED BY 'password';
     CREATE DATABASE db;
     GRANT ALL PRIVILEGES ON db.* TO username@'localhost';
     FLUSH PRIVILEGES;
     SHOW DATABASES;
     USE db;
     SHOW tables;

6. Connect MySql and Django

   6.1. Install MySql Client
	sudo apt install python3-dev
	sudo apt install libmysqlclient-dev
	sudo apt-get install pkg-config
	pip install mysqlclient

   6.2. Add the following to settings.py
        DATABASES = {
	   'default':{
		'ENGINE':'django.db.backends.mysql',
		'NAME' : 'some-project-name',
		'USER' : 'username',
		'PASSWORD' : 'password',
		'HOST' : 'localhost',
		'PORT' : '3306',
		}
	}

   6.3. Check django, create superuser, make migration, runserver
	python manage.py check
	python manage.py migrate
	python manage.py createsuperuser
	python manage.py runserver 0.0.0.0:8000

7. INSTALL & CONFIGURE DJANGO
   7.1. INSTALL APACHE2
	sudo apt instal apache2 libapache2-mod-wsgi-py3
   7.2. Configure
	cd /etc/apache2/sites-available/
	nano 000-default.conf
		<VirtualHost *:80>
			ErrorLog /project-name/site/logs/error.log
			CustomLog /project-name/site/logs/access.log combined
			
			alias /static /project-name/site/public/static
			<Directory /project-name/site/public/static>
				Require all granted
			</Directory>
			
			<Directory /project-name/src/some-project-name>
				<Files wsgi.py>
					Require all granted
				</Files>
			</Directory>
			
			WSGIDaemonProcess some-project-name python-home=/project-name/venv python-path=project-name/src/
			WSGIProcessGroup some-porject-name
			WSGIScriptAlias / /project-name/src/some-project-name/wsgi.py
		</VirtualHost>
	
	apachectl configtest
	service apache2 restart

8. CONFIGURE STATIC
   8.1. Add the following to settings.py:
	STATIC_ROOT = 'project-name/site/public/static'
	MEDIA_ROOT = 'project-name/site/public/media'
   python manage.py collectstatic
 
