# pipress

apt-get update
apt-get upgrade
apt-get install -y mariadb-client apache2 php php-mysql mariadb-server 

rm /var/www/html/*
cd /var/www/html
wget http://wordpress.org/latest.tar.gz
tar xzf latest.tar.gz
mv wordpress/* . 
rm -rf wordpress latest.tar.gz
chown -R $USER:www-data /var/www/html
chmod -R 775 /var/www/html

mysql_secure_installation

mariadb -uroot -p

create database wordpress;
CREATE USER 'wordpressuser'@'localhost' IDENTIFIED BY 'test';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wordpressuser'@'localhost' IDENTIFIED BY 'test';
FLUSH PRIVILEGES;
exit

----

# Add to wp-config.php
define('FS_METHOD', 'direct');

-----

cd && wget https://dl.eff.org/certbot-auto
chmod a+x certbot-auto
cd && ./certbot-auto certonly
cd && ./certbot-auto renew --dry-run
crontab -e
0 0 * * * cd && ./certbot-auto renew --quiet --no-self-upgrade
0 12 * * * cd && ./certbot-auto renew --quiet --no-self-upgrade

----

a2enmod ssl

# Add to /etc/apache2/sites-available/000-default.conf
<VirtualHost *:443>
        SSLEngine on
        SSLCertificateFile /etc/letsencrypt/live/test.co.uk/fullchain.pem
        SSLCertificateKeyFile /etc/letsencrypt/live/test.co.uk/privkey.pem
        SSLCertificateChainFile /etc/letsencrypt/live/test.co.uk/fullchain.pem
        ServerAdmin test@gmail.com
        DocumentRoot /var/www/html
</VirtualHost>


# Add to /var/www/html/.htaccess
<IfModule mod_rewrite.c>
RewriteEngine On
RewriteCond %{HTTPS} !=on
RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</IfModule>

service apache2 restart

----

# Maybe need this in .htaccess??????
<IfModule mod_rewrite.c>
RewriteEngine On
RewriteBase /
RewriteRule ^index\.php$ - [L]
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule . /index.php [L]
</IfModule>

