# Magento 1.9 Docker Container

This Docker container is forked from [andregugliotti/docker-magento19](https://github.com/andregugliotti/docker-magento19), I made some changes to suit my usage habits. which like:
* multiple sites
* php.ini on local computer for easier customized
* not include the source code (clone faster)
* failure up the container `phpmyadmin`

It build with support to `Nginx`, `PHP 5.6` (built with PHP FPM) and `MySQL 5.7`. It is also shipped with `PHPMyAdmin` and `xDebug`. Additionally, you can enable support for ionCube Loader, uncommenting the right lines at _docker-magento19/php/Dockerfile_.

## How to use this repository

Just clone the files into your project folder. It contains a Magento 1.9.3.4 CE installation, with sample data.

There are three folders:

- docker - that is the folder for Docker files
- src - that is the root folder, where Magento source files should in here as a folder (here this container can set multiple sites)

### Starting the container

Just open your terminal and navigate to foler `docker` of this project. Type:

`docker-compose up -d`

Docker will build the containers and start them. You must also map the address _127.0.0.1 local.m1docker.com_ on your hosts file. If you don't know how to do that, just google it. It's easy!

### About phpmyadmin 

The container phpmyadmin should work, but if you have multiple containers to use phpmyadmin, you might have issue.

For me, I have many docker environment and can't make phpmyadmin installed successfully. **(if you would like to help me, please feel free to create pull requests, many thanks :))** so I just go to https://www.phpmyadmin.net/downloads/ to download and put it into **/src**. It also works. 

Of cource, if you install phpmyadmin by put it into `src` folder, remember to comment / remove the phpmyadmin container in the docker-compose.yml file.

### Create database

Here you should create a database named **magento1**

If you want to install it by CLI, we should into the mysql container first:
```
docker-compose exec mysql bash

```
Then in the mysql container:
```
mysql -uroot -psecret_password
>
create database magento1;
```


### First access to project site

**Before accessing the Magento site**, you must decide if you want to use the sample data or use a clean install.

Or you can copy the file **local.xml** on the root directory into the magento source folder **app/etc/local.xml**

If you prefer to use the sample data, This is a repository to provide it:

[magento1.9.3.4-CE-with-sample-data](https://github.com/linxiaobo/magento1.9.3.4-CE-with-sample-data)

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

## Cloning [magento1.9.3.4-CE-with-sample-data](https://github.com/linxiaobo/magento1.9.3.4-CE-with-sample-data) inside src folder from [linxiaobo/docker-nginx-magento19](https://github.com/linxiaobo/docker-nginx-magento19)

In my case was used the folder src/magento1.9.3.4-CE-with-sample-data/local.m1docker.com, so it´s possible configure the file local.m1docker.com.conf with new directory "/var/www/html/magento1.9.3.4-CE-with-sample-data/local.m1docker.com".

``` bash

cd src/ 

git clone git@github.com:linxiaobo/magento1.9.3.4-CE-with-sample-data.git

```

### Configuring another port for Magento frontend and backend

I needed develop an application asp.net debugging and testing in my Local IIS, then I got to do move docker-compose.yaml port nginx for 20000:20000 instead of 80:80. 

So the url in configuration core_config_data table too moved to use new port 20000.

``` powershell
# Accessing mysql docker-compose 
docker-compose exec mysql bash

```

``` bash 
# Acessing mysql instance
mysql -uroot -psecret_password magento1

``` 

``` sql 
/* moving from http://m1.docker.local/ to http://m1.docker.local:20000/ */
update core_config_data set value='http://m1.docker.local:20000/' where config_id in (1529, 1530);

``` 

### Accessing Magento backend

When using sample data, just navigate to m1.docker.local/admin and access the panel with these credentials:

username: `admin`
password: `a123456`

BTW, if you want to change the password of admin, run this command in mysql container:
I prefer admin/admin123:

```
UPDATE admin_user SET `password` = md5('admin123'), is_active = 1 WHERE `username` = 'admin';
```

### Accessing the container terminal

If you need to access the container terminal, use the following commands:

- to see which are the running containers: `docker ps`
- to connect to a container as root: `docker exec -it {container_name} bash`
for example, into nginx container: `docker exec -it nginx bash`  (container name should be the name in docker-compose.yml)
- to connect to a container as local user (1000): `docker exec -it --user local {container_name} bash`

This last option is useful when you need to connect to the container and run an non root user (like when you need to run a console script).

## Contribution

This is an open source module and can be used as a base for other modules. If you have suggestions for improvements, just submit your pull request.

## License

This project is licensed under GNU General Public License, version 3.# docker-nginx-magento19
