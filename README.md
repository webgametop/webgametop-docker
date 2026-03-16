# webgametop-docker

## run "cli" container

```bash
  sudo make php-cli
```

### commands for "cli" container

```bash
  mysql -u USERNAME -pPASSWORD -h HOSTNAME_OR_IP DATABASE_NAME
```

## docker-compose.yml

```yml
services:

  nginx:
    container_name: webgametop_nginx
    build:
      context: ./webgametop-docker/nginx
      dockerfile: Dockerfile
    volumes:
      - ./webgametop:/var/www/www-data/webgametop
      - ./logs/nginx:/var/log/nginx
    depends_on:
      - php-fpm
    ports:
      - "9000:9000" # webgametop
    networks:
      - webgametop

  php-fpm:
    container_name: webgametop_php-fpm
    build:
      context: ./webgametop-docker
      dockerfile: php/fpm/Dockerfile
    volumes:
      - ./webgametop:/var/www/www-data/webgametop
      - ./logs/php:/var/log/php
    environment:
      XDEBUG_CONFIG: "client_host=host.docker.internal client_port=9003 log=/var/log/php/xdebug.log"
      PHP_IDE_CONFIG: "serverName=Docker"
    extra_hosts:
      - "host.docker.internal:host-gateway"
    networks:
      - webgametop

  php-cli:
    container_name: webgametop_php-cli
    build:
      context: ./webgametop-docker
      dockerfile: php/cli/Dockerfile
    volumes:
      - ./webgametop:/var/www/www-data/webgametop
      - ./logs/php:/var/log/php
    depends_on:
      - mysql
    networks:
      - webgametop

  mysql:
    container_name: webgametop_mysql
    build:
      context: ./webgametop-docker
      dockerfile: database/mysql/Dockerfile
    volumes:
      - webgametop_mysql:/var/lib/mysql
    environment:
      - MYSQL_DATABASE=webgametop
      - MYSQL_ROOT_USER=root
      - MYSQL_ROOT_PASSWORD=secret
      - MYSQL_ROOT_HOST=%
    ports:
      - "3306:3306"
    networks:
      - webgametop

  node:
    container_name: webgametop_node
    build:
      context: ./webgametop-docker
      dockerfile: node/Dockerfile
    volumes:
      - ./webgametop:/var/www/www-data/webgametop
    networks:
      - webgametop

volumes:
  webgametop_mysql:

networks:
  webgametop:
```

## Makefile

```Makefile
init: build
up: docker-up
stop: docker-stop
down: docker-down
restart: down up
build: docker-down-clear docker-build docker-up

php-cli:
  docker compose run --rm php-cli bash

php-fpm:
  docker compose run --rm php-fpm bash

docker-up:
  docker compose up -d --build

docker-stop:
  docker compose stop

docker-down:
  docker compose down --remove-orphans

docker-down-clear:
  docker compose down -v --remove-orphans

docker-build:
  docker compose build --pull
```
