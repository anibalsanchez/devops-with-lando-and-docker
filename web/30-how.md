# Let's practice! <!-- .slide: class="extly-slide-style plain console" -->


# Revisiting the First Recipe <!-- .slide: class="extly-slide-style plain console" -->

    ##
    # My First Recipe
    # .lando.yml
    #
    name: mysite
    recipe: lamp


## Recipe configuration <!-- .slide: class="extly-slide-style plain console" -->

### Load a custom php.ini

    services:
      appserver:
        config:
          conf: config/php.ini


## Proxy routes <!-- .slide: class="extly-slide-style plain console" -->

    proxy:
      html:
        - mysite.lndo.site
        - devops-with-lando-and-docker.extly.com


## Services <!-- .slide: class="extly-slide-style plain console" -->

    pma:
      ## To handle the administration of MySQL
      type: phpmyadmin

    mailhog:
      ## Web and API based SMTP testing
      type: mailhog


## Tooling <!-- .slide: class="extly-slide-style plain console" -->

Flexible tooling for Joomla.

    tooling:
      j-install:
        service: appserver
        description: "Command to install Joomla."
        cmd: echo Installing Joomla ...

      j-dev:
        service: appserver
        description: "Apply my default configuration for JDevelopment"
        cmd: echo Configuring my defaults in configuration.php for JDevelopment…

      j-restore:
        service: appserver
        description: "Restore an Akeeba .jpa backup"
        cmd: echo Restoring Akeeba .jpa backup …

    events:
      post-j-install:
        - appserver: curl -L https://downloads.joomla.org/cms/joomla3/3-8-7/Joomla_3-8-7-Stable-Full_Package.tar.gz | tar zxv -C $LANDO_WEBROOT

      post-j-dev:
          - appserver: test -e $LANDO_MOUNT/www/configuration.php && sh $LANDO_MOUNT/config/j-dev.sh

      post-j-restore:
        - appserver: cd $LANDO_WEBROOT; wget -O kickstart.zip https://www.akeebabackup.com/download/akeeba-kickstart/5-4-2/kickstart-core-5-4-2-zip.zip
        - appserver: cd $LANDO_WEBROOT; unzip kickstart.zip kickstart.php
        - appserver: cd $LANDO_WEBROOT; php kickstart.php *.jpa
        - appserver: cd $LANDO_WEBROOT; rm kickstart.*


## Tooling <!-- .slide: class="extly-slide-style plain console" -->

Flexible tooling for WordPress CLI.

    tooling:
      wp-install:
        service: appserver
        description: "Command to install WordPress."
        cmd: echo Installing WordPress ...

    events:
      post-wp-install:
      ## Download WordPress
      - appserver: cd $LANDO_WEBROOT; wp core download
      ## Configure it
      - appserver: cd $LANDO_WEBROOT; wp core config --dbname=wp_tpldb --dbuser=wp_tpldb --dbpass=wp_tpldb --dbhost=database --dbprefix=wp_
      ## Install it
      - appserver: cd $LANDO_WEBROOT; wp core install --url="http://localhost:8080" --title="Blog Title" --admin_user="admin" --admin_password="admin" --admin_email="anibal.sanchez@extly.com"
      ## Install Gutenberg plugin
      - appserver: cd $LANDO_WEBROOT; wp plugin install gutenberg --activate


## First Recipe - Internals <!-- .slide: class="extly-slide-style plain console" -->

<!-- .element: class="small" --> **Hidden docker-compose.yml**: ~/.lando/compose/mysite/mysite-1.yml

    version: '3.2'
    services:
      appserver:
        image: 'devwithlando/php:7.1-apache'
        environment:
          TERM: xterm
          COMPOSER_ALLOW_SUPERUSER: 1
          PATH: >-
            /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/var/www/.composer/vendor/bin
          LANDO_WEBROOT: /app
          LANDO_SERVICE_NAME: appserver
          LANDO_SERVICE_TYPE: php
          LANDO_MOUNT: /app
          DB_HOST: database
          DB_USER: lamp
          DB_PASSWORD: lamp
          DB_NAME: lamp
          DB_PORT: 3306
          COLUMNS: 256
          LANDO: 'ON'
          LANDO_CONFIG_DIR: /home/anibal/.lando
          LANDO_APP_NAME: mysite
          LANDO_APP_ROOT: /home/anibal/TEMP/U
          LANDO_APP_ROOT_BIND: /home/anibal/TEMP/U
          LANDO_HOST_OS: linux
          LANDO_HOST_UID: '1000'
          LANDO_HOST_GID: '1000'
          LANDO_HOST_IP: 192.168.23.100
          LANDO_WEBROOT_USER: www-data
          LANDO_WEBROOT_GROUP: www-data
          LANDO_WEBROOT_UID: '33'
          LANDO_WEBROOT_GID: '33'
        ports:
          - '80'
          - '443'
        volumes:
          - /var/www/.composer
          - /usr/local/bin
          - >-
            /home/anibal/.lando/services/config/php/php.ini:/usr/local/etc/php/conf.d/xxx-lando-default.ini
          - '/home/anibal/.lando/engine/scripts/add-cert.sh:/scripts/add-cert.sh'
          - >-
            /home/anibal/.lando/services/config/php/httpd-ssl.conf:/etc/apache2/sites-enabled/000-default.conf
          - >-
            /home/anibal/.lando/engine/scripts/lando-entrypoint.sh:/lando-entrypoint.sh
          - '$LANDO_APP_ROOT_BIND:/app'
          - '$LANDO_ENGINE_HOME:/user'
          - '$LANDO_ENGINE_CONF:/lando'
          - '/home/anibal/.lando/engine/scripts/user-perms.sh:/helpers/user-perms.sh'
          - '/home/anibal/.lando/engine/scripts/load-keys.sh:/scripts/load-keys.sh'
          - >-
            /home/anibal/.lando/services/helpers/mysql-import.sh:/helpers/mysql-import.sh
          - >-
            /home/anibal/.lando/services/helpers/mysql-export.sh:/helpers/mysql-export.sh
        command: docker-php-entrypoint sh -c 'a2enmod rewrite && apache2-foreground'
        entrypoint: /lando-entrypoint.sh
        networks:
          default: {}
        labels:
          io.lando.container: 'TRUE'
          io.lando.id: d1db68d981e28ba92f9a5e9b7a920bc800c188a5
      database:
        image: 'mysql:latest'
        environment:
          MYSQL_USER: lamp
          MYSQL_PASSWORD: lamp
          MYSQL_ALLOW_EMPTY_PASSWORD: 'yes'
          MYSQL_DATABASE: lamp
          TERM: xterm
          LANDO_SERVICE_NAME: database
          LANDO_SERVICE_TYPE: mysql
          LANDO_MOUNT: /app
          COLUMNS: 256
          LANDO: 'ON'
          LANDO_CONFIG_DIR: /home/anibal/.lando
          LANDO_APP_NAME: mysite
          LANDO_APP_ROOT: /home/anibal/TEMP/U
          LANDO_APP_ROOT_BIND: /home/anibal/TEMP/U
          LANDO_HOST_OS: linux
          LANDO_HOST_UID: '1000'
          LANDO_HOST_GID: '1000'
          LANDO_HOST_IP: 192.168.23.100
          LANDO_WEBROOT_USER: www-data
          LANDO_WEBROOT_GROUP: www-data
          LANDO_WEBROOT_UID: '33'
          LANDO_WEBROOT_GID: '33'
        volumes:
          - 'data_database:/var/lib/mysql'
          - >-
            /home/anibal/.lando/engine/scripts/lando-entrypoint.sh:/lando-entrypoint.sh
          - '$LANDO_APP_ROOT_BIND:/app'
          - '$LANDO_ENGINE_HOME:/user'
          - '$LANDO_ENGINE_CONF:/lando'
          - '/home/anibal/.lando/engine/scripts/user-perms.sh:/helpers/user-perms.sh'
          - '/home/anibal/.lando/engine/scripts/load-keys.sh:/scripts/load-keys.sh'
          - >-
            /home/anibal/.lando/services/helpers/mysql-import.sh:/helpers/mysql-import.sh
          - >-
            /home/anibal/.lando/services/helpers/mysql-export.sh:/helpers/mysql-export.sh
        healthcheck:
          test: mysql -uroot --silent --execute "SHOW DATABASES;"
          interval: 2s
          timeout: 10s
          retries: 25
        command: docker-entrypoint.sh mysqld
        ports:
          - '3306'
        entrypoint: /lando-entrypoint.sh
        networks:
          default: {}
        labels:
          io.lando.container: 'TRUE'
          io.lando.id: d1db68d981e28ba92f9a5e9b7a920bc800c188a5
    volumes:
      data: {}
      appserver: {}
      data_database: {}
    networks: {}


## Overrides <!-- .slide: class="extly-slide-style plain console" -->

### Useful snippets - localhost:8080

    overrides:
      services:
        ports:
          # A nice local site http://localhost:8080
          - '8080:80


## Overrides <!-- .slide: class="extly-slide-style plain console" -->

### Useful snippets - Testing MySQL 8

    database:
      type: mysql
      overrides:
        services:
          image: 'mysql:8'


## My recipes <!-- .slide: class="extly-slide-style plain console" -->

<!-- .element: class="small" --> Recipes for Lando (Docker containers). Tested with Joomla, WordPress, PrestaShop, Laravel, Symphony, etc.

- Joomla Template
- Lamp Template
- WordPress Template

<!-- .element: class="small" --> <https://github.com/anibalsanchez/lando-lamp-boilerplate>
