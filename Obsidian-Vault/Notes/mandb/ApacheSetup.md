1. Install Apache
2. WebDAV
   2.1 Enabling the webDAV
   2.2 Configuring Apache
3. Access From Anywhere
   3.1 Port Forwarding
       80, 443
   3.2 Dynamic DNS
       DuckDNS
4. HTTPS
5. Password




1. Install Apache
sudo apt update  && sudp apt upgrade -y
sudo apt install apache2 -y
-Test : http: ip	<- ip addr

2. WebDAV
2.1 Enabling the webDAV
sudo a2enmod dav
sudo a2enmod dav_fs
sudo systemctl restart apache2.service

2.2 Configuring Apache
Creating Domain Directory
sudo mkdir /var/www/webdav
Changing OwnerShip and File Permission
sudo chown www-data:www-data /var/www/webdav
sudo chmod -R 755 /var/www/webdav

sudo nano /etc/apache2/sites-available/000-default.conf


Alias /webdav /var/www/webdav
<Directory /var/www/webdav>
	DAV On
</Directory>

sudo systemctl restart apache2.service

3. Access From Anywhere

4. HTTPS
sudo apt install certbot python3-certbot-apache -y
sudp nano /etc/apache2/sites-available/000-default.conf

	ServerAdmin webmaster@localhost
	ServerName your_domain
	ServerAlias www.your_domain

sudo certbot --apache -d your_domain -d www.your_domain


5. Password
sudo mkdir -p /usr/local/apache
sudo chown www-data:www-data /usr/local/apache
sudo touch /usr/local/apache/webdav.users
sudo chown www-data:www-data /usr/local/apache/webdav.users
sudo htdigest /usr/local/apache/webdav.users webdav netvn
Username: netvn 
Password: 123
sudo nano /etc/apache2/sites-available/000-default-le-ssl.conf

<Directory /var/ww/webdav>
  DAV On
  AuthType Digest
  AuthName "webdav"
  AuthUserFile /usr/local/apache/webdav.users
  Require valid-user
</Directory>

sudo a2enmod auth_digest
sudo systemctl restart apache2.service
