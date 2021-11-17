# Docker Installation/WordPress Container
By Myles David

This documentation walks through a simple Docker installation with a setup of a WordPress Container

## Installing Docker 

> <i> I am doing this installation on an Ubuntu LTS Virtual Machine. The process should work on any linux distribution but for the most similar install, I would suggest using the same exact distribtion found on www.ubuntu.com/download/desktop</i>

Once you are logged into your Ubuntu VM navigate to the terminal and begin to install the neccesary packages.

**Update apt**
~~~
# sudo apt update 
~~~
**Install neccesary packages** 

Install Certificates:
~~~
# sudo apt install ca-certificates curl gnupg lsb-release
~~~
Navigate to the istallation packages
~~~
# curl -fsSL https://download.docker.com/linux/ubuntu/gpg | >sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
~~~

~~~
# echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
~~~
Update apt again and install the container 
~~~
# sudo apt update
# sudo apt install docker-ce docker-ce-cli containered.io
~~~
Add admin user to the docker group at the end 
~~~
# sudo usermod -aG docker admin 
~~~
*admin is the name of your account*

**Install Docker Compose**
~~~
# sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
~~~
Give compose the appropriate permissions
~~~
# sudo chmod +x /usr/local/bin/docker-compose
~~~
Use `docker-compose--version` to test and make sure docker is installed and is the right version. 

# Wordpress Install 
WordPress is a very popular online blogging tool that we are going to house in our docker container 

**Create WordPress Directory**

You need to create a new directory called wordpress under /srv 
~~~
# sudo mkdir -p /srv/wordpress 
~~~
Once the directory is created, cd into the the directory and use `nano` to create a new file in the directory. 
 ~~~
# cd /srv/wordpress 
# sudo nano
~~~
This file will be the **YAML** file or the "config" file. In Docker all resourses used to run a container are defined in the YAML file. Populate the file with this text:
~~~
version: '3'
services:
  mysql:
    image: mysql:latest
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: my_password
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress_user
      MYSQL_PASSWORD: wordpress_password
    volumes:
      - mysql_data:/var/lib/mysql
  wordpress:
    image: wordpress:latest
    depends_on:
      - mysql
    ports:
      - 8080:80
    restart: always
    environment:
      WORDPRESS_DB_HOST: mysql:3306
      WORDPRESS_DB_USER: wordpress_user
      WORDPRESS_DB_PASSWORD: wordpress_password
    volumes:
      - ./wp-content:/var/www/html/wp-content
volumes:
  mysql_data:
~~~
> <i> after you create the file save it as </i> `docker-compose.yaml`

This script defines both **mysql** and **wordpress**. The MySQL enviroment allowes for wordpress to use the enviroment variables. The wordpress enviroment creates a space for a WordPress container to run in.

Because WordPressed is based in Apache, it uses port **80** by default so we need also map the default Apache port to port **8080** on the local machine.

**Run WordPress with DockerCompose** 

While still in the wordpress directory, build the local enviroment
~~~
# sudo docker-compose up -d
~~~
> This operation my take a while. The scrpit is will pulling in Docker images and will create the foler the `/srv/wordpress/wp-content` and fill it will files and folders.

**Use web interface to finish installation**
Using any web browser, enter `http://localhost.8080` in the search bar this will bring you to the WordPress instalation site and will allow you to finish the installation. 

Once Set up your admin interface will look like this 
![admin interface](https://github.com/mylesndavid/DockerInstallation/blob/main/AdminInterface.png)

Reference: https://linuxiac.com/wordpress-with-docker/
