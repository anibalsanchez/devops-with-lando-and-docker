# What is Lando?



## External 1.1

Content 1.1

Note: This will only appear in the speaker notes window.


## External 1.2

Content 1.2



## External 2

Content 2.1



## External 3.1

Content 3.1


## External 3.2

Content 3.2


## External 3.3

![External Image](https://s3.amazonaws.com/static.slid.es/logo/v2/slides-symbol-512x512.png)


    # These are v3 docker compose file overrides. They allow you to provide very
    # precise fine tuning to your app
    # See: https://docs.docker.com/compose/compose-file/



    overrides:
      services:
        ports: ['8080:80']
        # image: pirog/myapache:2
        # build: .