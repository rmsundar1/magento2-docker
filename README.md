# Magento 2.4.x Docker for Developer project

Using this docker project you can initiate a new project or import the existent one.

## Preparation
1. Open https://www.docker.com/
1. Register new account or login with an existent one
1. Download and install docker app


## Install Magento
1. Import "magento2-docker" repository
2. Open "magento2-docker" folder in terminal app
3. Create "magento" folder: 
`mkdir magento`
4. If you want to import existing DB dump
   * 4.1 Create "db" folder:  `mkdir db`
   * 4.2 Copy the `.sql` or `.sql.gz` file
5. Build and run all docker components:
`docker-compose up --build`
6. After configuration is build and running open a new terminal tab and connect to php container:
`docker exec -it %PHP_CONTAINER_ID% bash`
You can easily get any container name via:
`docker ps`
7. Open "magento" folder and verify that the folder is empty:
`cd /var/www/magento; ls -la`
8. Choose your approach below and install the project

   * 8.1 If you want to install a vanilla Magento instance you need to go to https://devdocs.magento.com/guides/v2.4/install-gde/composer.html 
and create a new project to the current directory.

     Example:
`composer create-project --repository=https://repo.magento.com/ magento/project-enterprise-edition .`

    * 8.2 If you want to install an existent project you need to clone project repository to the "magento" folder:
`git clone <--project_repository_url--> .`

When prompted, enter your Magento authentication keys.

9. Once composer install is done run Magento install command:
```shell
php bin/magento setup:install \
        --db-host=db \
        --db-name=magento \
        --db-user=magento \
        --db-password=123123q \
        --base-url=https://magento.local \
        --backend-frontname=admin \
        --admin-user=admin \
        --admin-password=123123q \
        --admin-email=admin@test.com \
        --admin-firstname=Magento \
        --admin-lastname=User \
        --language=en_US \
        --currency=USD \
        --timezone=America/Chicago \
        --skip-db-validation \
        --elasticsearch-host=elasticsearch \
        --elasticsearch-port=9200 \
        --amqp-host=rabbitmq \
        --amqp-port=5672 \
        --amqp-user=guest \
        --amqp-password=guest \
        --amqp-virtualhost="/" \
    && chown -R www-data:www-data .
```

10. Update your laptop hosts file: `127.0.0.1 magento.local`

11. Open http://magento.local/

## Using Redis for session and cache
Connect to php container vis ssh and run the following commands:
```shell
php bin/magento setup:config:set --session-save=redis --session-save-redis-host="redis" --session-save-redis-port=6379 --session-save-redis-db=1
php bin/magento setup:config:set --cache-backend=redis --cache-backend-redis-server="redis" --cache-backend-redis-port=6379 --cache-backend-redis-db=2
```

For connecting to container via SSH you have to use the command:
`docker exec -it %CONTAINER_ID% bash`

## Reloading services

### Reload ngnix
```
docker exec -it %WEB_CONTAINER_ID% /etc/init.d/nginx reload
```

### Reload php
```
docker container restart %PHP_CONTAINER_ID%
```

## Connect to MySQL from your laptop
You're able to connect to MySQL using "0.0.0.0" as host, port should be default one - "3306", credentials from .env file

## Using MailHog for sending emails
You're able to find all the email you send from Magento instance on http://localhost:8025/

## Switch to PHP 7.x
1. Shutdown your current docker instance

2. Change PHP version in ./php/Docker file to needed version

Example: 

If you need to change PHP version from 7.4 to 7.3 you need to change `FROM php:7.4-fpm-buster` to `FROM php:7.3-fpm-buster`

3. Build and run all docker components:
`docker-compose up --build`

## xDebug Configuration
By default Xdebug is enabled in the container, Configure Xdebug in PHPSTORM IDE follow below steps
1. Goto `Files` > `Settings` > `PHP` > `Servers`
2. Add new server by clicking `+` icon
    * name: magento.local
    * host: magento.local
    * port: 443
3. Check `Use path mappings`
    * Absolute path on the server → `/var/www/magento/`
4. Start listening to the PHP Debug connecitons

### To debug CLI comments
1. Add `XDEBUG_CONFIG=idekey=phpstorm` before the comment.
e.g `XDEBUG_CONFIG=idekey=phpstorm bin/magento cache:flush`

## Disable Xdebug
1. connect to php container:
`docker exec -it %PHP_CONTAINER_ID% bash`
2. edit `/usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini` using `nano` or `vi` and comment the first line i.e.,
`;zend_extension=xdebug`
3. restart the PHP container
```
docker container restart %PHP_CONTAINER_ID%
```

## Configure Blackfire
1. connect to php container:
`docker exec -it %PHP_CONTAINER_ID% bash`
2. run with your server ID and Token `blackfire-agent --register --server-id={YOUR_SERVER_ID} --server-token={YOUR_SERVER_TOKEN}`
3. run `/etc/init.d/blackfire-agent start` to start the agent
4. Start the profiling from browser with blackfire extension.

### Credits
- [Igor](https://github.com/isydorenko)
- [Nithin](https://github.com/nithincninan/)
