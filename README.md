# Xdebugを使用する場合
- launch.json
```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Listen for XDebug",
            "type": "php",
            "request": "launch",
            "port": 9003,
            "pathMappings": {
                "/var/www/html/": "/home/nebur/projects/docker_wordpress/src/",
            }
        },
    ]
}
```
- docker-compose.yml
```yml
services:
   wordpress:
     container_name: ${PROJECT_NAME}
     depends_on:
       - db
     build:
       context: .
       dockerfile: Dockerfile
     ports:
       - "8081:80"
     environment:
       WORDPRESS_DB_HOST: db:3306
       WORDPRESS_DB_USER: ${DB_USER}
       WORDPRESS_DB_PASSWORD: ${DB_PASS}
       WORDPRESS_DB_NAME: ${DB_NAME}
     volumes:
      - ${PROJECT_PATH}:/var/www/html
      - ../php/php.ini:/usr/local/etc/php/conf.d/php.ini
      - ../php/xdebug.ini:/usr/local/etc/php/conf.d/xdebug.ini

   db:
     container_name: ${PROJECT_NAME}_mysql
     image: mysql:${MYSQL_VERSION}
     volumes:
       - ./db/my.cnf:/etc/mysql/conf.d/my.cnf
       - db_data:/var/lib/mysql
     environment:
       MYSQL_ROOT_PASSWORD: ${DB_PASS}
       MYSQL_DATABASE: ${DB_NAME}
       MYSQL_USER: ${DB_USER}
       MYSQL_PASSWORD: ${DB_PASS}
       TZ: ${TZ}
     ports:
       - ${DB_PORT}:3306

volumes:
  db_data: 
```

- Dockerfile(ymlと同階層)
```Docker
FROM wordpress:6.2.2

# Install necessary packages
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
       zlib1g-dev \
       libzip-dev \
       unzip \
    && docker-php-ext-install zip \
    && docker-php-ext-enable zip

# Install xdebug
RUN pecl install xdebug \
    && docker-php-ext-enable xdebug

```
