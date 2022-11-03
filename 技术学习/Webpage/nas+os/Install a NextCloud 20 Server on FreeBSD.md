**Description**

-   **Table of contents**
-   [Prepare the Environment](https://project.altservice.com/issues/959#Prepare-the-Environment)
-   [Install Nginx](https://project.altservice.com/issues/959#Install-Nginx)
-   [Install PostgreSQL](https://project.altservice.com/issues/959#Install-PostgreSQL)
    -   [Create PostgreSQL Databases and Users](https://project.altservice.com/issues/959#Create-PostgreSQL-Databases-and-Users)
-   [Install Nextcloud](https://project.altservice.com/issues/959#Install-Nextcloud)
-   [Redis](https://project.altservice.com/issues/959#Redis)
-   [Resources](https://project.altservice.com/issues/959#Resources)

This is a guide on setting up NextCloud 20 with Nginx on FreeBSD 12.

# Prepare the Environment[¶](https://project.altservice.com/issues/959#Prepare-the-Environment)

-   Before installation of the components, make sure everything is up to date using the following command:  
    
    ```
    pkg update -f && pkg upgrade
    
    ```
    

-   Create the nextcloud user:  
    
    ```
    pw user add -n nextcloud -m -s /sbin/nologin -c "NextCloud" 
    
    ```
    

___

# Install Nginx[¶](https://project.altservice.com/issues/959#Install-Nginx)

-   Install Nginx  
    
    ```
    pkg install nginx
    
    ```
    

-   Start and enable nginx at boot:  
    
    ```
    sysrc nginx_enable=YES
    service nginx start
    
    ```
    

-   Create a configuration directory to make managing individual server blocks easier  
    
    ```
    mkdir /usr/local/etc/nginx/conf.d
    
    ```
    

-   Edit the main nginx config file:  
    
    ```
    vi /usr/local/etc/nginx/nginx.conf
    
    ```
    
    -   And strip down the config file and add the include statement at the end to make it easier to handle various server blocks:  
        
        ```
        worker_processes  1;
        error_log  /var/log/nginx-error.log;
        
        events {
            worker_connections  1024;
        }
        
        http {
            include       mime.types;
            default_type  application/octet-stream;
            sendfile        on;
            keepalive_timeout  65;
        
            # Load config files from the /etc/nginx/conf.d directory
            include /usr/local/etc/nginx/conf.d/*.conf;
        }
        
        ```
        

___

# Install PostgreSQL[¶](https://project.altservice.com/issues/959#Install-PostgreSQL)

-   Start by installing the postgresql packages:  
    
    ```
    pkg install postgresql12-{server,client} php74-{pdo_pgsql,pgsql}
    
    ```
    

-   Enable, initialize and start PostgreSQL  
    
    ```
    postgresql_enable=YES
    service postgresql initdb
    service postgresql start
    
    ```
    

-   Edit the pg\_hba.conf file:  
    
    ```
    vi /usr/local/pgsql/data/pg_hba.conf
    
    ```
    
    -   And add the following to the end of the file to enable password authentication:  
        
        ```
        host    all        all        samehost        md5
        
        ```
        

## Create PostgreSQL Databases and Users[¶](https://project.altservice.com/issues/959#Create-PostgreSQL-Databases-and-Users)

-   Log in to postgresql user account  
    
    ```
    su - pgsql
    
    ```
    

-   Connect to postgresql database  
    
    ```
    psql -d template1
    
    ```
    
    -   Create a user and database for NextCloud:  
        
        ```
        CREATE USER nextclouduser WITH PASSWORD 'SuperSecretPassword' CREATEDB;
        
        CREATE DATABASE nextclouddb OWNER nextclouduser;
        
        ```
        

-   Quit postgresql and exit the user:  
    
    ```
    \q
    exit
    
    ```
    

___

# Install Nextcloud[¶](https://project.altservice.com/issues/959#Install-Nextcloud)

-   Install nextcloud:  
    
    ```
    pkg install nextcloud
    
    ```
    

-   Create an **nextcloud.example.com server block** config file:  
    
    ```
    vi /usr/local/etc/nginx/conf.d/nextcloud.example.com.conf
    
    ```
    
    -   Add the following:  
        
        ```
        upstream nextcloud-handler {
          server unix:/var/run/nextcloud.sock;
        }
        
        server {
          listen 80;
          server_name nextcloud.example.com;
        
          # Path to the root of your installation
          root /usr/local/www/nextcloud/;
        
          # set max upload size
          client_max_body_size 10G;
          fastcgi_buffers 64 4K;
        
          # Disable gzip to avoid the removal of the ETag header
          gzip off;
        
          rewrite ^/caldav(.*)$ /remote.php/caldav$1 redirect;
          rewrite ^/carddav(.*)$ /remote.php/carddav$1 redirect;
          rewrite ^/webdav(.*)$ /remote.php/webdav$1 redirect;
        
          index index.php;
          error_page 403 /core/templates/403.php;
          error_page 404 /core/templates/404.php;
        
          location = /robots.txt {
            allow all;
            log_not_found off;
            access_log off;
          }
        
          location ~ ^/(?:\.htaccess|data|config|db_structure\.xml|README){
            deny all;
          }
        
          location / {
            # The following 2 rules are only needed with webfinger
            rewrite ^/.well-known/host-meta /public.php?service=host-meta last;
            rewrite ^/.well-known/host-meta.json /public.php?service=host-meta-json last;
        
            rewrite ^/.well-known/carddav /remote.php/carddav/ redirect;
            rewrite ^/.well-known/caldav /remote.php/caldav/ redirect;
        
            rewrite ^(/core/doc/[^\/]+/)$ $1/index.html;
        
            try_files $uri $uri/ =404;
          }
        
          location ~ \.php(?:$|/) {
            fastcgi_split_path_info ^(.+\.php)(/.+)$;
            include fastcgi_params;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            fastcgi_param PATH_INFO $fastcgi_path_info;
            fastcgi_pass nextcloud-handler;
            fastcgi_intercept_errors on;
          }
        
          # Adding the cache control header for js and css files
          # Make sure it is BELOW the location ~ \.php(?:$|/) { block
          location ~* \.(?:css|js)$ {
            add_header Cache-Control "public, max-age=7200";
            # Add headers to serve security related headers
            add_header Strict-Transport-Security "max-age=15768000; includeSubDomains; preload;";
            add_header X-Content-Type-Options nosniff;
            add_header X-Frame-Options "SAMEORIGIN";
            add_header X-XSS-Protection "1; mode=block";
            add_header X-Robots-Tag none;
            # Optional: Don't log access to assets
            access_log off;
          }
        
          # Optional: Don't log access to other assets
          location ~* \.(?:jpg|jpeg|gif|bmp|ico|png|swf)$ {
            access_log off;
          }
        }
        
        ```
        

-   Create the temporary session folder and restrict its permissions:  
    
    ```
    mkdir -p /usr/local/www/nextcloud/tmp
    chmod o-rwx /usr/local/www/nextcloud/tmp
    
    ```
    

-   Create the nextcloud php-fpm pool config file:  
    
    ```
    vi /usr/local/etc/php-fpm.d/nextcloud.example.com.conf
    
    ```
    
    -   And add the following:  
        
        ```
        [nextcloud.example.com]
        user = nextcloud
        group = www
        listen = /var/run/nextcloud.sock
        listen.owner = nextcloud
        listen.group = www
        pm = dynamic
        pm.max_children = 5
        pm.start_servers = 2
        pm.min_spare_servers = 1
        pm.max_spare_servers = 3
        php_admin_value[session.save_path] = "/usr/local/www/nextcloud/tmp" 
        
        ```
        

-   Change the ownership of the nextcloud directory:  
    
    ```
    chown -R nextcloud:www /usr/local/www/nextcloud
    
    ```
    

-   Restart nginx and start php-fpm:  
    
    ```
    service nginx restart
    service php-fpm start
    
    ```
    

___

# Redis[¶](https://project.altservice.com/issues/959#Redis)

-   Install Redis and PHP extension:  
    
    ```
    pkg install redis php74-pecl-redis
    
    ```
    

-   Edit the redis config:  
    
    ```
    vi /usr/local/etc/redis.conf
    
    ```
    
    -   And modify the following parameters in the config:  
        
        ```
        port 0
        unixsocket /var/run/redis/redis.sock
        unixsocketperm 770
        
        ```
        

-   Add nextcloud user to redis group  
    
    ```
    pw groupmod redis -m nextcloud
    
    ```
    

-   Start and enable Redis at boot:  
    
    ```
    sysrc redis_enable=YES
    service redis.start
    
    ```
    

-   Edit the NextCloud config:  
    
    ```
    vi /usr/local/www/nextcloud/config/config.php
    
    ```
    
    -   And add the following **before** the ending `);`:  
        
        ```
          'memcache.locking' => '\OC\Memcache\Redis',
          'memcache.local' => '\OC\Memcache\Redis',
          'redis' => array(
             'host' => '/var/run/redis/redis.sock',
             'port' => 0,
          ),
        ```