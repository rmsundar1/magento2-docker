# Magento 2 Docker for Developer project

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
4. Build and run all docker components:
`docker-compose up --build`
5. After configuration is build and running open a new terminal tab and connect to php container:
`docker exec -it php bash`
6. Open "magento" folder and verify that the folder is empty:
`cd /var/www/magento; ls -la`
7. Choose your approach below and install the project

7.1 If you want to install a vanilla Magento instance you need to go to https://devdocs.magento.com/guides/v2.3/install-gde/composer.html 
and create a new project to the current directory.

Example:
`composer create-project --repository=https://repo.magento.com/ magento/project-enterprise-edition .`

7.2 If you want to install an existent project you need to clone project repository to the "magento" folder:
`git clone <--project_repository_url--> .`

When prompted, enter your Magento authentication keys.

8\. Once composer install is done run Magento install command:
```
php bin/magento setup:install \
        --db-host=db \
        --db-name=magento \
        --db-user=magento \
        --db-password=123123q \
        --base-url=http://magento.local \
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
    && chown -R www-data:www-data .
```

9\. Update your laptop hosts file: `127.0.0.1 magento.local`

10\. Open http://magento.local/

## Using Redis for session and cache
Open ./magento/app/etc/env.php and add/update the corresponding data:
```php
...
    'session' => [
        'save' => 'redis',
        'redis' => [
            'host' => 'redis',
            'port' => '6379',
            'password' => '',
            'timeout' => '2.5',
            'persistent_identifier' => '',
            'database' => '2',
            'compression_threshold' => '2048',
            'compression_library' => 'gzip',
            'log_level' => '1',
            'max_concurrency' => '6',
            'break_after_frontend' => '5',
            'break_after_adminhtml' => '30',
            'first_lifetime' => '600',
            'bot_first_lifetime' => '60',
            'bot_lifetime' => '7200',
            'disable_locking' => '0',
            'min_lifetime' => '60',
            'max_lifetime' => '2592000'
        ]
    ],
...
    'cache' => [
        'frontend' => [
            'default' => [
                'backend' => 'Cm_Cache_Backend_Redis',
                'backend_options' => [
                    'server' => 'redis',
                    'database' => '0',
                    'port' => '6379'
                ]
            ]
        ]
    ]
...

``` 

## Connect to containers via SSH
Docker configuration contains such components as:
- web (nginx service)
- php (php-fpm service)
- db (MySQL service)
- redis (redis service)
- mail (mailhog mail service)

For connecting to container via SSH you need use the command:
`docker exec -it <--container_name--> bash`, where <--container_name--> is container name

Example:
`docker exec -it web`

## Reloading services

### Reload ngnix
```
docker exec -it web
/etc/init.d/nginx reload
```

### Reload php
```
docker docker-compose restart php
```

## Connect to MySQL from your laptop
You're able to connect to MySQL using "0.0.0.0" as host, port should be default one - "3306", credentials from .env file

## Using MailHog for sending emails
You're able to find all the email you send from Magento instance on http://localhost:8025/

## Switch to PHP 7.x
1\. Shutdown your current docker instance

2\. Change PHP version in ./php/Docker file to needed version

Example: 

If you need to change PHP version from 7.2 to 7.1 you need to change `FROM php:7.2-fpm` to `FROM php:7.1-fpm`

3\. Build and run all docker components:
`docker-compose up --build`