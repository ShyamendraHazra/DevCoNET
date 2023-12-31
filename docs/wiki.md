### [Go to Menu](menu.md) 🏃🏻🏃🏻‍♀️
### [Go Back to README](../README.md) 🏃🏻🏃🏻‍♀️

## File Structure and Functionalities 🏗️

### Repo File Structure 🌲

```
----- 🧍🏻 Normal State 🧍🏻‍♀️ -----
.
|-- Dockerfile
|-- README.md
|-- docker-compose.yml
|-- docs
|   `-- wiki.md
|-- main.sh
|-- mysql
|   |-- mysql.conf
|   |   `-- my.cnf
|   `-- sql
|       `-- init.sql
|-- scripts
|   |-- setup-mysql-demo-table.sh
|   |-- setup.sh
|   |-- start.sh
|   `-- stop.sh
`-- src
    |-- assets
    |   |-- css
    |   |   `-- style.css
    |   `-- js
    |       `-- scripts.js
    |-- inc
    |   `-- config.php
    `-- index.php

11 directories 📁, 15 files 📄



----- 🏃🏻 Running State 🏃🏻‍♀️ -----

.
|-- Dockerfile
|-- README.md
|-- docker-compose.yml
|-- docs
|   `-- wiki.md
|-- logs
|   |-- mysql
|   |   `-- mysql-error.log
|   |-- php-apache
|   |   |-- access.log
|   |   |-- error.log
|   |   `-- other_vhosts_access.log
|   `-- phpmyadmin
|       |-- access.log
|       |-- error.log
|       `-- other_vhosts_access.log
|-- main.sh
|-- mysql
|   |-- data
|   |   |-- #ib_16384_0.dblwr
|   |   |-- #ib_16384_1.dblwr
|   |   |-- #innodb_redo
|   |   |   |-- #ib_redo0
|   |   |   |-- #ib_redo10_tmp
|   |   |   |-- #ib_redo11_tmp
|   |   |   |-- #ib_redo12_tmp
|   |   |   |-- #ib_redo13_tmp
|   |   |   |-- #ib_redo14_tmp
|   |   |   |-- #ib_redo15_tmp
|   |   |   |-- #ib_redo16_tmp
|   |   |   |-- #ib_redo17_tmp
|   |   |   |-- #ib_redo18_tmp
|   |   |   |-- #ib_redo19_tmp
|   |   |   |-- #ib_redo1_tmp
|   |   |   |-- #ib_redo20_tmp
|   |   |   |-- #ib_redo21_tmp
|   |   |   |-- #ib_redo22_tmp
|   |   |   |-- #ib_redo23_tmp
|   |   |   |-- #ib_redo2_tmp
|   |   |   |-- #ib_redo3_tmp
|   |   |   |-- #ib_redo4_tmp
|   |   |   |-- #ib_redo5_tmp
|   |   |   |-- #ib_redo6_tmp
|   |   |   |-- #ib_redo7_tmp
|   |   |   |-- #ib_redo8_tmp
|   |   |   `-- #ib_redo9_tmp
|   |   |-- #innodb_temp
|   |   |   |-- temp_1.ibt
|   |   |   |-- temp_10.ibt
|   |   |   |-- temp_2.ibt
|   |   |   |-- temp_3.ibt
|   |   |   |-- temp_4.ibt
|   |   |   |-- temp_5.ibt
|   |   |   |-- temp_6.ibt
|   |   |   |-- temp_7.ibt
|   |   |   |-- temp_8.ibt
|   |   |   `-- temp_9.ibt
|   |   |-- auto.cnf
|   |   |-- ibdata1
|   |   |-- ibtmp1
|   |   |-- mysql
|   |   |-- mysql.ibd
|   |   |-- undo_001
|   |   `-- undo_002
|   |-- mysql.conf
|   |   `-- my.cnf
|   `-- sql
|       `-- init.sql
|-- mysql.env
|-- php.env
|-- phpmyadmin.env
|-- scripts
|   |-- setup-mysql-demo-table.sh
|   |-- setup.sh
|   |-- start.sh
|   `-- stop.sh
`-- src
    |-- assets
    |   |-- css
    |   |   `-- style.css
    |   `-- js
    |       `-- scripts.js
    |-- inc
    |   `-- config.php
    `-- index.php

19 directories 📁, 67 files 📄
```
#### **NOTE 🛠️ : The additional files and folder are generated by scripts or containers dynamically. After containers have stopped they are automatically removed by the script including DB data.**


#### The necessary env files, sql files are generated by bash scripting. (*in future powershell scripts will be added too*) ⚙️

* * *

### Working Prcedures ⚙️
 

#### dcoker-compose.yml 👇🏼

```yml
version: "3.8"
services:

  php-apache-server:
    build:
      context: .
      target: php_apache
      args:
        BUILD_DATE: ${BUILD_DATE:-$(date -u +"%Y-%m-%dT%H:%M:%SZ")}
    image: php-apache
    env_file:
      - php.env
    container_name: php-apache
    volumes:
      - ./src:/var/www/html
      - ./logs/php-apache:/var/log/apache2
    ports: 
      - 80:80
  
  mysql:
    build:
      context: .
      target: mysql
      args:
        BUILD_DATE: ${BUILD_DATE:-$(date -u +"%Y-%m-%dT%H:%M:%SZ")}
    image: mysql
    env_file:
      - mysql.env
    container_name: mysql
    volumes:
      - ./mysql/data:/var/lib/mysql
      - ./logs/mysql:/var/log/mysql
    restart: on-failure

  phpmyadmin:
    build:
      context: .
      target: phpmyadmin
      args:
        BUILD_DATE: ${BUILD_DATE:-$(date -u +"%Y-%m-%dT%H:%M:%SZ")}
    image: phpmyadmin
    container_name: phpmyadmin
    ports:
      - 8080:80
    env_file:
    - phpmyadmin.env
    volumes:
      - ./logs/phpmyadmin:/var/log/apache2
```

### Key 🔑 points to remember 👓
- 🫴🏼  **If you don't understand how above file work please do check prerquisites section. [see it here 🔗](prerequisites.md)**
- 🫴🏼  **apache is listening to default port 80 and that is forwarded to port 80 on the host**

- 🫴🏼  **mysql has no user other than root user. The root user have an empty password to ease of devlopment. Not Recommended for Production Environment. [see here 🔗](production.md)**

- 🫴🏼  **all of the images are build with a ENV variable named BUILD_DATE passed to the Dockerfile to make sure the current images build doesn't use cache data and is built with latest changes in the configuration files. TO know how ENV files are handled [see here 🔗](env-files.md)**

- 🫴🏼  **Not all the volumes that are used by the containers are created manually as missing ones are automatically created by Docker Compose at the time of creating the Containers. They are later removed by the script files.**

- 🫴🏼 🤔 ***Volumes are generally created to persist the data inside the containers after the containers are removed right? So why deleting the volumes?***
    1. 👉🏼 **for log perpose, It may be a hassle to check logs again and again using "docker logs" ***[for more details 🔗](https://docs.docker.com/engine/reference/commandline/logs/)*** command and it will be more conveninet if the logs are directly availabe dynamically in the host machine.**

    2. 👉🏼 **For Database data only sql files can do the job of persisting data so why keep the huge data.****
      
        - 🫸🏼 **also remember that in case of mysql image containers, all files with .sql , .sh etc extentions are automatically executed ***/docker-entrypoint-initdb.d/*** folder.**
        
          - 📕 **References**
              
              - 🔖 **[mysql 🔗](https://www.reddit.com/r/docker/comments/tfgdo9/can_i_run_a_mysql_setup_script_after_building/)**
              - 🔖 **[postgres 🔗](https://mkyong.com/docker/how-to-run-an-init-script-for-docker-postgres/)**
        
        - 🫸🏼 **Note: If mysql data volume is not empty above feature won't work**
    
    3. 👉🏼 **I hove that clarifies the need to make some of the container volumes folders dynamic and temporary**

### Dockerfile 👇🏼

```Dockerfile
FROM php:apache AS php_apache
ARG BUILD_DATE
RUN docker-php-ext-install mysqli
RUN echo "ServerName localhost" >> /etc/apache2/apache2.conf

FROM mysql:latest as mysql
ARG BUILD_DATE
COPY ./mysql/sql/ /docker-entrypoint-initdb.d/
COPY ./mysql/mysql.conf /etc/my.cnf.custom
RUN cat /etc/my.cnf.custom/my.cnf > /etc/my.cnf

FROM phpmyadmin AS phpmyadmin
ARG BUILD_DATE
RUN echo "ServerName localhost" >> /etc/apache2/apache2.conf

FROM php_apache AS final
```
### Key 🔑 points to remember 👓

- 🫴🏼 **The Dockerfile uses multi stage buiid.** ***[for more information 🔗](https://docs.docker.com/build/building/multi-stage/)***

- 🫴🏼 **my.cnf is modified version of official my.cnf provided in official mysql docker image. It is modified to log errors.**

- 🫴🏼 **"ServerName localhost" line is added in apache2 configuration of both php:apache and phpmyadmin containers to help them determine Servername.**

<br/>

* * *

- ### 👉🏼 The PHP Application files reside insise src ✨ folder

- ### 👉🏼 Logs 📃 of all containers can be accessed in the logs folder while the Containers are running

- ### 👉🏼 Put any sql file inside /docker-entrypoint-initdb.d/ folder of mysql image to have any database, tables and data loaded automatically in the containers.

