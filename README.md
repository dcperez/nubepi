# nubepi

<p align="center"><i>Personal cloud server using Nextcloud services and a Raspberry Pi 3 B+.</i></p>

## Installation
*In Progress*

### Installing Apache and PHP

1. Update
```
sudo apt-get update
sudo apt-get upgrade
```

2. Install Apache 2

`sudo apt-get install apache2`

3. Install PHP

`sudo apt-get install php7.3 php7.3-gd sqlite php7.3-sqlite3 php7.3-curl php7.3-zip php7.3-xml php7.3-mbstring`

4. Restart Apache sever

`sudo service apache2 restart`

### Installing Nextcloud
1. cd into html directory

`cd /var/www/html`

2. Download and extract latest version of Nextcloud

`curl https://download.nextcloud.com/server/releases/nextcloud-16.0.3.tar.bz2 | sudo tar -jxv`

3. Create data directory

`sudo mkdir -p /var/www/html/nextcloud/data`

4. Give user and group control to Nextcloud folder

`sudo chown -R www-data:www-data /var/www/html/nextcloud/`

5. Give permissions

`sudo chmod 750 /var/www/html/nextcloud/data`

6. Test by going to Nextcloud

`IP Address/nextcloud`

Tip:  Use your Raspberry Pi's ID address. It can be accessed using `ifconfig`.

7. Create your Username and Password on Nextcloud

### Moving Nextcloud's data folder

1. Create the directory

`sudo mkdir -p /var/nextcloud`

2. Move current data directory into new directory

`sudo mv -v /var/www/html/nextcloud/data /var/nextcloud/data`

3. Point data directory config to new directory

`cd /var/www/html/nextcloud/config`

4. Create backup of config file

`sudo cp -p config.php config.php.bk`

5. Open config.php file using text editor. With nano or vim, you can use the following commands:

`sudo nano config.php` or  `sudo vim config.php`

6. Change the following line

`'datadirectory' => '/var/www/html/nextcloud/data'`

to 

`'datadirectory' => '/var/nextcloud/data',`

7. Exit text editor

### Increasing Nextcloud's max upload size
By default, the upload limit is 2MB. The following will increase the limit to 1 GB.

1. Open the following configuration file with your text editor

`sudo nano /etc/php/7.3/apache2/php.ini` or `sudo vim /etc/php/7.3/apache2/php.ini`

2. Replace the following two lines

```
post_max_size = 8M
upload_max_filesize = 2M
```

to 

```
post_max_size = 1024M
upload_max_filesize = 1024M
```

3. Exit text editor

4. Restart Apache server

`sudo service apache2 restart`

### Modifying .htaccess override
This configuration file affects how Apache will respond to various requests.

1. Open the following file using your text editor

`sudo nano /etc/apache2/apache2.conf` or `sudo vim /etc/apache2/apache2.conf`

2. Change the following block

```
<Directory /var/www/>        
        Options Indexes FollowSymLinks
        AllowOverride None
        Require all granted
</Directory>
```

to 

```
<Directory /var/www/>        
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
</Directory>
```

3. Exit text editor

4. Restart Apache server

`sudo service apache2 restart`

### Setting up SSL for Nextcloud
Set up to run sever through HTTPS

1. Create directory to store ssl 

`sudo mkdir -p /etc/apache2/ssl`

2. Generate certificate

`sudo openssl req -x509 -nodes -days 365 -newkey rsa:4096 -keyout /etc/apache2/ssl/apache.key -out /etc/apache2/ssl/apache.crt`

3. Fill out the following options

```
Country Name (2 letter code) [AU]:
State or Province Name (full name) [Some-State]:
Locality Name (eg, city) []:
Organization Name (eg, company) [Internet Widgits Pty Ltd]:
Organizational Unit Name (eg, section) []:
Common Name (e.g. server FQDN or YOUR name) []:
Email Address []:
```

4. Enable SSL module for Apache

`sudo a2enmod ssl`

5. Open *default-ssl* file to utilize new certificate within your text editor

`sudo nano /etc/apache2/sites-available/default-ssl.conf` or `sudo vim /etc/apache2/sites-available/default-ssl.conf`

6. Modify the following lines:

```
SSLCertificateFile /etc/ssl/certs/ssl-cert-snakeoil.pem
SSLCertificateKeyFile /etc/ssl/private/ssl-cert-snakeoil.key
```

to

```
SSLCertificateFile /etc/apache2/ssl/apache.crt
SSLCertificateKeyFile /etc/apache2/ssl/apache.key
```

7. Exit text editor

8. Enable default-ssl configuration

`sudo a2ensite default-ssl.conf`

9. Restart Apache server

`sudo service apache2 restart`

10. Test Raspberry Pi's IP address with **https://** 

`https://*IP Address*/nextcloud`

**If you want to direct all traffic to HTTPS**

11. Open default Apache configuration file with your text editor

`sudo nano /etc/apache2/sites-available/000-default.conf` or `sudo vim /etc/apache2/sites-available/000-default.conf`

12. Replace all the text with the following code block below:

```
<VirtualHost *:80>
   ServerAdmin example@example

   RewriteEngine On
   RewriteCond %{HTTPS} off
   RewriteRule ^(.*)$ https://%{HTTP_HOST}$1 [R=301,L]
</VirtualHost>
```

13. Exit text editor

14. Redirect module and restart Apache

```
sudo a2enmod rewrite
sudo service apache2 restart
```

### Port Forwarding

1. Open Nextcloud confirguration file using your text editor

`sudo nano /var/www/html/nextcloud/config/config.php` or `sudo vim /var/www/html/nextcloud/config/config.php`

2. Add Raspberry Pi's IP address to the following array

```
'trusted_domains' =>
array (
    0 => '192.168.1.105',
),
```

3. Exit text edior

4. Set up port forwarding on router with TCP (Port 80 and Port 443)


## Enjoy!

## To-Do
- [ ] Set-up DNS for external network access
- [ ] SD Mac Mount for periodic back-ups
