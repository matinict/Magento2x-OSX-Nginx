# Magento2x-OSX-Nginx

How To  Install Magento 2x  Mac or OSX , Nginx, MySQL, PHP (MEMP stack)

  
My Development Environment:

     Magento 2.3.x
     nginx/1.14.x
     PHP 7.2.x
     MySQL 5.7
     macOS/OSX :10.13.6
  
 
 I am going to show everybody the best practice, How to install Magento 2.3x on OSX with Nginx.
 I will show everybody steps by step to install Magento 2.3x on OSX :10.13.6 with Nginx 1.10.x, PHP 7.2.x and MySQL 5.7.
 
 If you want to start developing PHP applications, or merely work on your PHP-based site off-line, on Mac OS X you can easily do so. In this how-to we’ll see how you can set up NginX, a high performance web server, with the PHP version shipped with Mac OS X itself to create a local web server. In case you’re wondering, you can of course use it in parallel with MAMP, XAMPP or even the multi-PHP version server I’ve described in an earlier post.

If you want to start developing PHP applications, or merely work on your PHP-based site off-line, on Mac OS X you can easily do so. In this how-to we’ll see how you can set up NginX, a high performance web server, with the PHP version shipped with Mac OS X itself to create a local web server. In case you’re wondering, you can of course use it in parallel with MAMP, XAMPP or even the multi-PHP version server


 

Okie, let's go.Let's do this practice, you need to follow steps by step:

# Step 0: Install HomeBrew
    If you don’t have HomeBrew already you need to install it per its instructions. HomeBrew is a package manager for Mac OS X which lets you install a lot of software without hunting around the Internet for a suitable package file.
    HomeBrew is a package manager for macOS, that allows to easily install various Unix applications.

To install, simply execute the command shown on the official website:
Open the “Terminal” application, found in /Applications/Utilities/
Enter the following command into a single line of the terminal:

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

If you do not already have them, macOS will prompt you to first install Xcode Command Line Tools.
    

# Step 1: Installing the server. 

    - cat /etc/*release (check your current OSX version)
    I have OSX on my localhost.
    - You can check the requirements system for Magento 2.3 in the 
    link https://devdocs.magento.com/guides/v2.3/install-gde/prereq/nginx.html    
    
   # 1.1- Install the softwares: [Advanced Nginx](https://github.com/matinict/Magento2x-OSX-Nginx/blob/master/nginx.md) 
    
    
      apt-get update
      apt-get -y install git wget curl nano unzip sudo vim net-tools openssh-server
      - Install the Nginx 1.10.x: 
      apt-get -y install nginx
      service nginx start
      nginx -v
      
   # 1.2- Install PHP 7.2.x and the required PHP extensions: 
   
      apt-get install software-properties-common
      add-apt-repository ppa:ondrej/php
      apt-get update
      apt-cache search php7.2
      apt-get install php7.2 libapache2-mod-php7.2 php7.2-common php7.2-gd php7.2-mysql php7.2-curl php7.2-intl php7.2-xsl php7.2-mbstring php7.2-zip php7.2-bcmath php7.2-soap php-xdebug php-imagick
      
      php -v
      - Install PHP 7.2 FPM:
      apt-get install php7.2-fpm
      nano /etc/php/7.2/fpm/php.ini
      press ctrl + w for searching
      press ctrl + shift+v for past &  Enter Search
       
      memory_limit = 2G
      max_execution_time = 3600
      max_input_time = 1800
      upload_max_filesize = 10M
      zlib.output_compression = On
      press ctrl + O for saving or Ctrl+x
      service php7.2-fpm start
      
           
   # 1.3- Install Composer:
    
      curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
      composer --version
      
      
   # 1.4- Install MySQL 5.7  
   
    
    
           
     Install MySQL using the apt command below:
          sudo apt install mysql-server mysql-client -y
          enter the password for the root user: giaphugroup
          service mysql start
          mysql_secure_installation
          SHOW VARIABLES LIKE "%version%"; 
          sudo mysql -u root -p
 
     After the MySQL installation is complete, start the MySQL service and enable it to launch everytime at system boot.
          systemctl start mysql
          systemctl enable mysql

          
   # 1.5- Install phpMyAdmin
   
    install PHPMyAdmin using the apt command below.
     sudo apt install phpmyadmin -y
     During the installation, it will ask you about the web server configuration for phpmyadmin.
     Choose none option and move the cursor to: 'OK'.
     For the phpmyadmin database configuration: choose 'Yes'
     And type new 'STRONG' phpmyadmin admin such as 'Hakaselabs001@#'.

     Enter a password: "PhpMyAdmin@123"
     Repeat the "PhpMyAdmin@123" password.

   # 1.6-Create a new virtual host for accessing to phpmyadmin
    
        
         sudo  vim /etc/nginx/sites-available/default
         Paste the following Nginx configuration for phpmyadmin inside the 'server {...}' bracket.

     location /phpmyadmin {
         root /usr/share/;
         index index.php;
         try_files $uri $uri/ =404;

     location ~ ^/phpmyadmin/(doc|sql|setup)/ {
         deny all;
         }

     location ~ /phpmyadmin/(.+\.php)$ {
         fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
         fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
         include fastcgi_params;
         include snippets/fastcgi-php.conf;
         }
     }
     Save and exit.
     
      Test the nginx configuration and restart the nginx service.
     nginx -t
     systemctl reload nginx
     And we've added the Nginx configuration for phpmyadmin.
     Login to the MySQL shell.

     mysql -u root -p
     Now create a new user using the MySQL queries below.

     create user phpmyadmin@'localhost' identified by 'PhpMyAdmin@123';
     grant all privileges on *.* to phpmyadmin@'localhost' identified by 'PhpMyAdmin@123';
     flush privileges;
     exit;

     Test Login PhpMyAdmin
     On the web browser, type the following phpmyadmin URL (replace the IP with your server IP).

     http://127.0.0.1/phpmyadmin/


# Step 2: Install and configure Magento 2.3.x

    - Create the Magento authentication keys: https://marketplace.magento.com/
    - Create the new database named magento2_3_0: http://localhost:9000
    - cd /var/www/html
    -composer create-project --repository=https://repo.magento.com/ magento/project-community-edition <install-directory-name>
    [ composer create-project --repository-url=https://repo.magento.com/ magento/project-community-edition=2.3.x magento2.3.x]
    - cd magento2.3.2
    find var generated vendor pub/static pub/media app/etc -type f -exec chmod g+w {} +
    find var generated vendor pub/static pub/media app/etc -type d -exec chmod g+ws {} +
    chown -R :www-data .
    chmod u+x bin/magento
    php bin/magento setup:di:compile
    - Create a new virtual host for accessing to the Magento2.3.x site
    nano /etc/nginx/sites-available/magento2.3.x
    upstream fastcgi_backend {
        server unix:/run/php/php7.2-fpm.sock;
    }

    server {
        listen 86;
        server_name localhost;
        set $MAGE_ROOT /var/www/html/magento2.3.2;
        include /var/www/html/magento2.3.2/nginx.conf.sample;
    }
    ln -s /etc/nginx/sites-available/magento2.3.2 /etc/nginx/sites-enabled
    - Restart Nginx:
    nginx -t
    service nginx restart
    netstat -plnt
    86 is of the magento 2.3.2 site.

    - Install Magento 2.3.2: http://localhost:86
    - cd /var/www/html/magento2.3.2
    php bin/magento setup:static-content:deploy -f
    
 
 
 


# Step 3: Nginx server blocks (Virtual Hosts)
          --Multiple Subdomain of Nginx Server
          
          
          - cd /var/www/html
          composer create-project --repository=https://repo.magento.com/ magento/project-community-edition magento.lan
           cd magento.lan
          find var generated vendor pub/static pub/media app/etc -type f -exec chmod g+w {} +
          find var generated vendor pub/static pub/media app/etc -type d -exec chmod g+ws {} +
          chown -R :www-data .
          chmod u+x bin/magento
          php bin/magento setup:di:compile
          
          
         /etc/nginx/sites-available/default. Let's take a look at it:
         *** Remove below code from all site except default otherwise show error**
         $ cp /etc/nginx/sites-available/default /etc/nginx/sites-available/default.bak
         $ sudo nano /etc/nginx/sites-available/default
         
         upstream fastcgi_backend {
               server unix:/run/php/php7.2-fpm.sock;
          }

          # Default server configuration
        
          server {
               listen 80 default_server;
               listen [::]:80 default_server;

               # SSL configuration
               #
               # listen 443 ssl default_server;
               # listen [::]:443 ssl default_server;
               #
               # Note: You should disable gzip for SSL traffic.
               # See: https://bugs.debian.org/773332
               #
               # Read up on ssl_ciphers to ensure a secure configuration.
               # See: https://bugs.debian.org/765782
               #
               # Self signed certs generated by the ssl-cert package
               # Don't use them in a production server!
               #
               # include snippets/snakeoil.conf;

               root /var/www/html;

               # Add index.php to the list if you are using PHP
               index index.php index.html index.htm index.nginx-debian.html;

               server_name _;

               location / {
                    # First attempt to serve request as file, then
                    # as directory, then fall back to displaying a 404.
                    try_files $uri $uri/ =404;
               }

               # pass PHP scripts to FastCGI server
               #
               #location ~ \.php$ {
               #	include snippets/fastcgi-php.conf;
               #
               #	# With php-fpm (or other unix sockets):
               #	fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
               #	# With php-cgi (or other tcp sockets):
               #	fastcgi_pass 127.0.0.1:9000;
               #}

               # deny access to .htaccess files, if Apache's document root
               # concurs with nginx's one
               #
               #location ~ /\.ht {
               #	deny all;
               #}
               location /phpmyadmin {
                root /usr/share/;
               index index.php;
                try_files $uri $uri/ =404;

               location ~ ^/phpmyadmin/(doc|sql|setup)/ {
               deny all;
                    }

               location ~ /phpmyadmin/(.+\.php)$ {
               fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
               fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
               include fastcgi_params;
               include snippets/fastcgi-php.conf;
                    }	
               }


          }
          
          $sudo nano /etc/nginx/sites-available/magento.lan 
          server {
              listen 80;
              server_name www.magento.lan;
              set $MAGE_ROOT /var/www/html/magento.lan;
              include /var/www/html/magento.lan/nginx.conf.sample;
               }
               
           $ sudo nginx -t  
           $ sudo nano /etc/hosts
           
           127.0.0.1	localhost
           Host File Content Like That:
           
               127.0.1.1	matin-U18 
               127.0.0.1	www.magento.lan 
           
          $ sudo ln -s /etc/nginx/sites-available/magento.lan /etc/nginx/sites-enabled
          $ sudo systemctl restart nginx
          
          http://www.magento.lan/setup
          
          [In your browser Complete Install]

          
# Step 4: Sample Data Install using Composer:  

   
  
    cd /var/www/html/magento.lan
    php bin/magento sampledata:deploy
     Authentication required (repo.magento.com): if ask cmd
     Username: xxxxx
     Pasward: xxx

    php bin/magento setup:upgrade
          
   [https://marketplace.magento.com/customer/accessKeys/ login then copy Public & private Key]
   
   Ex.
   
     Public Key: 2c534578b8507d00e7f927af2591756d Copy
     Private Key: 8819e8bf8336c7ada213ee2df18f5c15 Copy
          


# Instruction *
 
 ** If Any Command not work or errror something use # sudo { sudo apt-get update }
 ** Sometime network & System issue some apt may not work properly check errorlog


# Ref## 
 [dionysopoulos](https://www.dionysopoulos.me/set-up-nginx-and-php-for-development-on-mac-os-x/)
 [sylvaindurand](https://www.sylvaindurand.org/setting-up-a-nginx-web-server-on-macos/)
 [dionysopoulos](https://www.dionysopoulos.me/custom-apache-and-php-server-on-macos-the-definitive-2019-edition/)
 [dionysopoulos](https://www.dionysopoulos.me/set-up-nginx-and-php-for-development-on-mac-os-x/)
 [atlassian](https://magento2.atlassian.net/wiki/spaces/m1wiki/pages/14024849/Configuring+nginx+for+Magento+1.x)
 
 










   Thank you for watching guide.If you have any questions about this practice, 
   please feel free to leave a comment or hangout:matinict@gmail.com.
   Please do not hesitate to contact me don't worry about charge I try to help free on my little knowledge,
   if you need me to join your Magento project especially extension developer.
   My rate is $20/hour in Magento 1 and $25/hour in Magento 2x.

