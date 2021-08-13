# Project 2 WEB STACK IMPLEMENTATION (LEMP STACK)

# Linux Nginx Mysql Php

## I created an Ec2 instance on AWS and used connect to it using my ssh

##  Installing Nginx.
### I updated my ubuntu server using sudo apt-get update
### I installed Nginx : sudo apt install nginx 
### Checked the status of my nginx after installation using sudo systemctl status nginx.
### Then i navigate to my ec2 instance security group and opened port 80 which is http.
### then i used curl http://localhost:80 to check if its open and it did open and same on my web browser which display welcome to nginx page


## Mysql installation.
### Just as i did in project 1 with mysql installation and creation of a user that can access the mysql link here https://github.com/Tobang1/darey.io-pbl/blob/c7390a536bd62bed498cb1f4e1251138a66349ce/project1.md


## Php Installation
 
 ## I installed Php and all of it packages using sudo apt install php-fpm php-mysql and used php -v to check the version and check if its installed on the server.

 ## CONFIGURING NGINX TO USE PHP PROCESSOR 

 ### The first thing is making a web root directory in the var/www/ folder which i named projectLEMP using sudo mkdir /var/www/projectLEMP 
 ### I assigned an ownership permission to the folder  using sudo chown -R $USER:$USER /var/www/projectLEMP
 ### Then i created a new configuration file for my sites-available directory using sudo nano /etc/nginx/sites-available/projectLEMP editted it and pasted the bare-bone configuration code in it. which defiens the listen, file path. server name, etc.
 ### I activate the configuration by linking the config file on nginx sit-enabled directory using  sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/
 ### Then i did a unit test on my code to check if its error free using sudo nginx -t and its ok and successful.
### Then i disabled the default nginx host using sudo unlink /etc/nginx/sites-enabled/default and restart my nginx server with sudo systemctl reload nginx.
### I created an index.html fine to activate a new server block in the /var/www/projectLEMP folder.
### Then used this code  (sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html) .
### I navigate to my browser to check my ip on port :80 http://<Public-IP-Address>:80 then i saw the echo command i used the last time on terminal


## TESTING PHP WITH NGINX
### First i created and open a file called info.php in the /var/www/projectLemp/info.php and typed in the code <?php phpinfo(); in the info.php file

### Then i checked my domain server ip http://ip/info.php and it display all information about my php server

## RETRIVING DATA FROM MYSQL DATABASE WITH PHP.

## This part we are creating a test database with simple todo list and we are connecting our php webserver to retrieve the information from the database to the web browser.

### I connect to mysql database with sudo mysql
### created a new database with CREATE DATABASE testdb
### Then i created a new user as i did in project 1 and it used the user to connect to mysql using mysql -u username -p password and it went well.

## PROBLEMS ENCOUNTER
### I didnt encounter any problem at all. im getting deep knowlege with it.

![alt text](![alt text](https://github.com/Tobang1/darey.io-pbl/blob/77f25527f37ef75b89d48b61c024f045086ba96a/pbl_images/project_2_lemp.PNG))