### Yaml Entry
```
wallabag:
    image: ikaruswill/wallabag
    container_name: wallabag
    environment:
      - MYSQL_ROOT_PASSWORD=wallaroot
      - SYMFONY__ENV__DATABASE_DRIVER=pdo_mysql
      - SYMFONY__ENV__DATABASE_HOST=walla_db
      - SYMFONY__ENV__DATABASE_PORT=3306
      - SYMFONY__ENV__DATABASE_NAME=wallabag
      - SYMFONY__ENV__DATABASE_USER=wallabag
      - SYMFONY__ENV__DATABASE_PASSWORD=wallapass
      - SYMFONY__ENV__DATABASE_CHARSET=utf8mb4
      - SYMFONY__ENV__MAILER_HOST=127.0.0.1
      - SYMFONY__ENV__MAILER_USER=~
      - SYMFONY__ENV__MAILER_PASSWORD=~
      - SYMFONY__ENV__FROM_EMAIL=wallabag@example.com
      - SYMFONY__ENV__DOMAIN_NAME=https://wb.DOMAIN.net #fake url
      - SYMFONY__ENV__SERVER_NAME="Your wallabag instance"
      - SYMFONY__ENV__FOSUSER_CONFIRMATION=FALSE
    ports:
      - "8091:80"
    volumes:
      - ./wallabag/wallabag/images:/var/www/wallabag/web/assets/images
  walla_db:
    image: mariadb
    environment:
      - MYSQL_ROOT_PASSWORD=wallaroot
    volumes:
      - ./wallabag/wallabag/data:/var/lib/mysql
  redis:
    image: redis:alpine
```

### Proxy Config
the file is named wb.subdomain.conf and was copied from wallabag.subdomain.conf.sample
in the swag container, there is a "wb" entry for the subdomains
```
## Version 2021/02/16
# make sure that your dns has a cname set for wallabag and that your wallabag container is not using a base url.
# also, make sure your env var in your docker run or compose match the full domain, incl. https://
# i.e. - SYMFONY__ENV__DOMAIN_NAME=https://wallabag.yourdomain.com
server {
    listen 443 ssl;
    listen [::]:443 ssl;

    server_name wb.*;

    include /config/nginx/ssl.conf;

    client_max_body_size 0;

    location / {

        include /config/nginx/proxy.conf;
        resolver 127.0.0.11 valid=30s;
        set $upstream_app wb;
        set $upstream_port 80;
        set $upstream_proto http;
        proxy_pass $upstream_proto://$upstream_app:$upstream_port;

    }
}
```
