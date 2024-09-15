## Install & Upgrade
```
sudo apt update && sudo apt upgrade -y
```
```
apt list --upgradable
```
```
sudo apt install python3-update-manager update-manager-core
```
## Install Firewall
```
sudo apt install ufw
```
## Enable Firewall
```
sudo ufw enable
```
## Allow SSH New Port
```
sudo ufw allow NEW_PORT/tcp
```
## Deny SSH Old Port
```
sudo ufw deny 22/tcp
```
## Allow Important Ports
```
sudo ufw allow 80
sudo ufw allow 443
```
## Firewall Status
```
sudo ufw status
```
## Change SSH Port
```
sudo nano /etc/ssh/sshd_config
```
```
sudo service ssh restart
sudo systemctl restart sshd
```
## Install Nginx
```
sudo apt install nginx
```

## Start Nginx
```
sudo systemctl start nginx
```

## Enable Nginx
```
sudo systemctl enable nginx
```

## Install MySql
```
sudo apt install mysql-server
```
## MySql Security
```
sudo mysql_secure_installation
```
## Install Software-properties-common
```
sudo apt install software-properties-common
```
## Add the repository for PHP
```
sudo add-apt-repository ppa:ondrej/php
```
## Install PHP FPM
```
sudo apt install php-fpm php-mysql
```
## Install PHP 8.3 Fpm
```
sudo apt install php8.3-fpm
```
## PHP 8.3 Fpm Status
```
sudo systemctl status php8.3-fpm
```
## Start PHP 8.3 Fpm
```
sudo systemctl start php8.3-fpm
```
## Enable PHP 8.3 Fpm
```
sudo systemctl enable php8.3-fpm
```
## Install PhpMyAdmin
```
sudo apt install phpmyadmin
```
## Symbolic Link
```
sudo ln -s /usr/share/phpmyadmin /var/www/html/phpmyadmin
```
## Nginx server block configuration
```
sudo nano /etc/nginx/sites-available/yourdomain.com
```
Edit and copy this text and paste it into the file above
```
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;

    root /var/www/html;
    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.3-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location /phpmyadmin {
        root /usr/share;
        index index.php;
        try_files $uri $uri/ =404;
        location ~ ^/phpmyadmin/(.*\.php)$ {
            fastcgi_pass unix:/var/run/php/php8.3-fpm.sock;
            fastcgi_index index.php;
            include fastcgi_params;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        }
    }
}
```
## Symbolic Link
```
sudo ln -s /etc/nginx/sites-available/example.com /etc/nginx/sites-enabled/
```
## Tests the Nginx configuration files for syntax errors
```
sudo nginx -t
```
## Restart Nginx
```
sudo systemctl restart nginx
```
## Install Certbot
```
sudo apt install certbot python3-certbot-nginx
```
## Get Certificate For Domain
```
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com
```
## Open MySql
```
sudo mysql -u root -p
```
## Create User
```
CREATE USER 'user'@'localhost' IDENTIFIED BY 'password';
CREATE USER 'phpmyadmin'@'localhost' IDENTIFIED BY 'password';
```
## Grant All Privileges
```
GRANT ALL PRIVILEGES ON *.* TO 'user'@'localhost' WITH GRANT OPTION;
GRANT ALL PRIVILEGES ON *.* TO 'phpmyadmin'@'localhost' WITH GRANT OPTION;

```
## Flush Privileges
```
FLUSH PRIVILEGES;
```
## Exit
```
exit
```
## Edit Config.inc.php
```
nano /etc/phpmyadmin/config.inc.php
```
```
<?php
if (!function_exists('check_file_access')) {
    function check_file_access(string $path): bool
    {
        if (is_readable($path)) {
            return true;
        }

        if (! file_exists($path)) {
            return false;
        }

        error_log(
            'phpmyadmin: Failed to load ' . $path
            . ' Check group www-data has read access and open_basedir restrictions.'
        );
        return false;
    }
}

// Load secret generated on postinst
if (check_file_access('/var/lib/phpmyadmin/blowfish_secret.inc.php')) {
    require('/var/lib/phpmyadmin/blowfish_secret.inc.php');
}

$i = 0;
$i++;

if (check_file_access('/etc/phpmyadmin/config-db.php')) {
    require('/etc/phpmyadmin/config-db.php');
}

if (!empty($dbname)) {
    $cfg['Servers'][$i]['auth_type'] = 'cookie';
    if (empty($dbserver)) $dbserver = 'localhost';
    $cfg['Servers'][$i]['host'] = $dbserver;

    if (!empty($dbport) || $dbserver != 'localhost') {
        $cfg['Servers'][$i]['connect_type'] = 'tcp';
        $cfg['Servers'][$i]['port'] = $dbport;
    }

    // User for advanced features
    $cfg['Servers'][$i]['controluser'] = 'phpmyadmin';
    $cfg['Servers'][$i]['controlpass'] = 'password';
    
    $cfg['Servers'][$i]['pmadb'] = $dbname;
    $cfg['Servers'][$i]['bookmarktable'] = 'pma__bookmark';
    $cfg['Servers'][$i]['relation'] = 'pma__relation';
    $cfg['Servers'][$i]['table_info'] = 'pma__table_info';
    $cfg['Servers'][$i]['table_coords'] = 'pma__table_coords';
    $cfg['Servers'][$i]['pdf_pages'] = 'pma__pdf_pages';
    $cfg['Servers'][$i]['column_info'] = 'pma__column_info';
    $cfg['Servers'][$i]['history'] = 'pma__history';
    $cfg['Servers'][$i]['table_uiprefs'] = 'pma__table_uiprefs';
    $cfg['Servers'][$i]['tracking'] = 'pma__tracking';
    $cfg['Servers'][$i]['userconfig'] = 'pma__userconfig';
    $cfg['Servers'][$i]['recent'] = 'pma__recent';
    $cfg['Servers'][$i]['favorite'] = 'pma__favorite';
    $cfg['Servers'][$i]['users'] = 'pma__users';
    $cfg['Servers'][$i]['usergroups'] = 'pma__usergroups';
    $cfg['Servers'][$i]['navigationhiding'] = 'pma__navigationhiding';
    $cfg['Servers'][$i]['savedsearches'] = 'pma__savedsearches';
    $cfg['Servers'][$i]['central_columns'] = 'pma__central_columns';
    $cfg['Servers'][$i]['designer_settings'] = 'pma__designer_settings';
    $cfg['Servers'][$i]['export_templates'] = 'pma__export_templates';

    $i++;
}

$cfg['UploadDir'] = '';
$cfg['SaveDir'] = '';

foreach (glob('/etc/phpmyadmin/conf.d/*.php') as $filename)
{
    include($filename);
}
```
```
sudo systemctl restart nginx
sudo systemctl restart php8.3-fpm
```
