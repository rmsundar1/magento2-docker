# Changelog

## [2.0.5] - 2020-02-06
### Added
- Added SSL configuration to nginx config
- Replaced mariadb by mysql due to crash issues
- Added n98-magerun2
- Added https://github.com/hirak/prestissimo
- Added msmtp instead of outdated ssmtp 

## [2.0.4] - 2019-03-06
### Added
- .env file

## [2.0.3] - 2019-03-05
### Added
- Cache for volumes

## [2.0.2] - 2019-03-05
### Added
- Documentation for Redis configuration

## [2.0.1] - 2019-03-05
### Updated
- Documentation

## [2.0.0] - 2019-03-05
### Added
- MailHog container
- Redis container
- Container names
- Certificate generation for web container
- healthcheck for db container

### Updated
- Documentation
- Renamed hostname

## [1.0.0] - 2018-08-15
### Added
- CHANGELOG.md file to keep changes between versions.
- README.md file for repository description.
- docker-compose.yml file with directives to run required containers.
- php/Dockerfile with instructions to raise php-fpm with required extensions and scripts to install Magento2 instance.
- nginx/Dockerfile to raise latest nginx image and copy configuration file from host to container.
- nginx/conf/test.com.conf file with required nginx configuration to run Magento2 instance.
