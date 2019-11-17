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
*In Progress*

### Port Forwarding
*In Progress*

## To-Do
- [ ] Finish installation guide
- [ ] Set-up DNS for external network access
- [ ] SD Mac Mount for periodic back-ups
