---
layout:     post
title:      The correct docker-compose setup for Wordpress and Nginx
date:       2021-06-03 10:14:00
categories: docker, nginx, wordpress
---

Most of the docker-compose example configurations available online for Wordpress and Nginx are not the correct approach in my opinion.
This is because they suggest to mount the whole `/var/www/html` folder as a volume.

For example binding a local `html` folder:

```yaml

services:
  nginx:
    ...
    volumes:
      - ./html:/var/www/html
  wordpress:
    ...
    volumes:
      - ./html:/var/www/html
```

The big problem with this approach is that it becomes very hard to upgrade Wordpress.
When wordpress' image version is changed, the files in the volume are not overwritten, meaning that no actual
upgrade was done. We'd need to delete the files in the volume (except for the `wp-content` folder) and then run
`docker-compose up` again.


The only folder we actually need to mount is `wp-content` to be able install and update themes and 
plugins.
It's a good idea to disable Wordpress automatic updates, which can be done with an environment variable.
We also need a way for nginx to be able to access worpress' files again. Named volumes come to the rescue:

```yaml
services:
  nginx:
    ...
    volumes:
      - www-data:/var/www/html
      - ./html:/var/www/html/wp-content
  wordpress:
    ...
    environment:
      - WORDPRESS_CONFIG_EXTRA=
          define('AUTOMATIC_UPDATER_DISABLED', true);
    volumes:
      - www-data:/var/www/html
      - ./html:/var/www/html/wp-content
volumes:
  www-data:
```
