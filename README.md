# Boilerplate Magento 2 Docker

## Project Installation
* Clone git repo or create new composer project in `./src`
* `cp dev/composer.env.sample dev/composer.env` and input valid Magento
  credentials and GitHub key

## Configure `dev/docker-compose.yml`
* Replace `magento.docker` with desired domain
    - Add a corresponding host entry on the host machine for `127.0.0.1 yourdomain.docker`
* Replace `{{blakcfireClientId}}`, `{{blakcfireClientToken}}`,
* `{{blakcfireServerId}}`, `{{blakcfireServerToken}}` with valid Blackfire
  credentials
* `XDEBUG_ENABLE=false` to disable xDebug
* `MAGENTO_RUN_MODE=production` to force production mode
* PHP versions can be interchanged by updating the build directory for the `app` service

## Services Available to the Host by default
* localhost:1080 Mailcatcher
* localhost:9200 Elasticsearch
* localhost:15672 RabbitMQ Management
* localhost:9306 MariaDB
* localhost:6081 Varnish

## Building the Dockerfiles
* `cd dev`
* `docker-compose build`
  - Note: the default project prefix for containers is the directory name. When
    using this for multiple projects you should specify the project name via
    the `-p` flag or change the name of the `dev` directory.

## Installing Magento
* `cd dev`
* `docker-compose exec app magento-installer`

## Installing Sample Data From Performance Toolkit
* `cd dev`
* `docker-compose exec app magento-sample-data`

## Running `bin/magento` Commands
* `cd dev`
* `docker-compose exec app magento {{command}}`
or you can enter the container using `docker-compose exec app bash`
