# Magento 1.9 Docker Container

This Docker container is build with support to `Nginx`, `PHP 5.6` (built with PHP FPM) and `MySQL 5.7`. It is also shipped with `PHPMyAdmin` and `xDebug`. Additionally, you can enable support for ionCube Loader, uncommenting the right lines at _docker-magento19/php/Dockerfile_.

## How to use this repository

Just clone the files into your project folder. It contains a Magento 1.9.3.4 CE installation, with sample data.

There are three folders:

- docker-magento19 - that is the folder for Docker files
- src - that is the root folder, where Magento source files should in here as a folder (here this container can set multiple sites)

### Starting the container

Just open your terminal and navigate to docker-magento19 of this project. Type:

`docker-compose up -d`

Docker will build the containers and start them. You must also map the address _127.0.0.1 local.m1docker.com_ on your hosts file. If you don't know how to do that, just google it. It's easy!

### First access to project site

**Before accessing the Magento site**, you must decide if you want to use the sample data or use a clean install.

Or you can copy the file **local.xml** on the root directory into the magento source folder **app/etc/local.xml**

If you prefer to use the sample data, I have a repository to provide it:

https://github.com/linxiaobo/magento1.9.3.4-CE-with-sample-data

Put the **sample-db** folder to the folder **/mysql**

Why? because in the **docker-compose.yml** we use Volumes to share the sql file:

```
volumes:
    - ./mysql/sample-db:/usr/local/src
```

you must import it via mysql command. 
```
docker-compose exec mysql bash

```
Then in the mysql container:

Run the import command:
```
mysql -uroot -psecret_password magento1 < /usr/local/src/magento1.sql
```

Why **/usr/local/src/magento1.sql**? because in the **docker-compose.yml** we put it into container and the path is **/usr/local/src**
```
volumes:
    - ./mysql/sample-db:/usr/local/src
```


### Accessing Magento backend

When using sample data, just navigate to m1.docker.local/admin and access the panel with these credentials:

username: `admin`
password: `a123456`

BTW, if you want to change the password of admin, run this command in mysql container:
I prefer admin/admin123:

```
UPDATE admin_user SET `password` = md5('admin123'), is_active = 1 WHERE `username` = 'admin'
```

### Accessing the container terminal

If you need to access the container terminal, use the following commands:

- to see which are the running containers: `docker ps`
- to connect to a container as root: `docker exec -it {container_name} bash`
- to connect to a container as local user (1000): `docker exec -it --user local {container_name} bash`

This last option is useful when you need to connect to the container and run an non root user (like when you need to run a console script).

## Contribution

This is an open source module and can be used as a base for other modules. If you have suggestions for improvements, just submit your pull request.

## License

This project is licensed under GNU General Public License, version 3.# docker-nginx-magento19
