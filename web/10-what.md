# What is Lando? <!-- .slide: class="extly-slide-style plain" -->

![Lando is for developers](images/10-what/lando-logo.png)


## In this workshop <!-- .slide: class="extly-slide-style plain" data-background-repeat="no-repeat" data-background-image="images/10-what/lando-seal.png" data-background-size="15%" data-background-position="95% 5%" -->

- How to use Lando
- Configure recipes
- Mimic locally production environments
- Customize or extend tooling
- Deployment options


![Lando Calrissian](images/10-what/lando-calrissian.jpg)


## A developer should be able to <!-- .slide: class="extly-slide-style plain" data-background-repeat="no-repeat" data-background-image="images/10-what/lando-seal.png" data-background-size="15%" data-background-position="95% 5%" -->

- git clone
- **lando start**
- ... and get EVERYTHING they need to develop


## Lando is for devs  <!-- .slide: class="extly-slide-style plain" data-background-repeat="no-repeat" data-background-image="images/10-what/lando-seal.png" data-background-size="15%" data-background-position="95% 5%" -->

- Quickly &amp; painlessly spin up the services
- Local development environment
- DevOps tool built on Docker containers
- It's a free, open source, and cross-platform


_Lando is a tool to help you do your job and make your team faster and better._


_It is not a “silver bullet” to instantly solve your #99DevProblems._


## First Recipe <!-- .slide: class="extly-slide-style plain console" -->

    ##
    # My First Recipe
    # .lando.yml
    #
    name: mysite
    recipe: lamp


## lando start <!-- .slide: class="extly-slide-style plain console" -->

    anibal@local-server:~/$ lando start

    landoproxyhyperion5000gandalfedition_proxy_1 is up-to-date
    Creating network "mysite_default" with the default driver
    Creating volume "mysite_data" with default driver
    Creating volume "mysite_appserver" with default driver
    Creating volume "mysite_data_database" with default driver
    Creating mysite_database_1  ... done
    Creating mysite_appserver_1 ... done
    Waiting until database service is ready...

    BOOMSHAKALAKA!!!

    Your app has started up correctly.
    Here are some vitals:

    NAME            mysite
    LOCATION        /home/anibal
    SERVICES        appserver, database
    APPSERVER URLS  https://localhost:32813
                    http://localhost:32814
                    http://mysite.lndo.site


## This presentation <!-- .slide: class="extly-slide-style plain console" -->

    ##
    # The Recipe of this Presentation
    # .lando.yml
    #
    name: devops-with-lando-and-docker

    proxy:
      html:
        - devops-with-lando-and-docker.lndo.site
    services:
      html:
        type: apache:2.2
        webroot: web
        overrides:
          services:
            ports:
              - '8080:80'


## This presentation - lando start <!-- .slide: class="extly-slide-style plain console" -->

    anibal@local-server:~/devops-with-lando-and-docker$ lando start

    landoproxyhyperion5000gandalfedition_proxy_1 is up-to-date
    Starting devopswithlandoanddocker_html_1 ... done
    Waiting until html service is ready...

    BOOMSHAKALAKA!!!

    Your app has started up correctly.
    Here are some vitals:

    NAME       devops-with-lando-and-docker
    LOCATION   /home/anibal/devops-with-lando-and-docker
    SERVICES   html
    HTML URLS  http://localhost:8080
                http://localhost:32818
                http://devops-with-lando-and-docker.lndo.site
                https://devops-with-lando-and-docker.lndo.site
