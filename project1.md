# LAMP STACK IMPLEMENTATION.

## Connectiong To AWS EC2 instance.
### I signed up and created a new ubuntu instance for my project 1 Lamp stack which generated a .pem file which i downloaded on AWS.
### then i connected to the sever using ssh with the .pem file using gitbash terminal ssh -i project.pem ubuntu@0.0.0.0.0 (the 0 is a reference to  my ip address).
### I updated & upgraded the server using sudo apt-get update and sudo apt-get upgrade.


## INSTALLING THE components of LAMP Apache, Mysql and Php.
## APACHE  (engine)

### i installed apache using sudo apt install apache2
### checked the status using sudo systemctl status apache 2 which showed green and says active.
### then i went to the instance security group to edit my inbound rules and open port 80 which is http and using anywhere for the source ip 0.0.0.0.0.
### I did checked my localhost adress on browser and it worked. showed apache2 page.

## MYSQL (Database)
### I installed mysql using sudo apt install mysql-server.
### then used mysql secure_installation to authenticate mysql and create a root user with password.
### then i opend mysql using sudo mysql and got connected.
### i later created a user and a password so i can connect remotely and the connection ip address to anywhere for start using % sign and i connectd with mysql -u Tobang -p -h 19.168.5.6 which i got connected.
### the password  authentication was mysql_native_password.

## PHP (backend script side )

### I installed php and all its components using sudo apt install php libapache2-mod-php php-mysql
### I checked the status using php -v which showed php 7.4.3 .

## Creating The virtual host 
### I maded a directory to hold my index.php file in the server directory  using sudo mkdir /var/www/projectlamp
### gave the directory the right permission as root using  sudo chown -R $USER:$USER /var/www/projectlamp
### I created a new configuration file in the site-available directory named projectlamp.conf using sudo vi /etc/apache2/sites-available/projectlamp.conf 
### I used vim as my editor and later used nano also.
### Then i made the projectlamp.conf file the default web root directory using 000-default.conf  default-ssl.conf  projectlamp.conf and sudo a2ensite projectlamp to enable the virtual host and sudo a2dissite 000-default to dissable the default preinstalled apache VH.
### cheacked my code if it has any error using sudo apache2ctl configtest
### Then reload my apache using sudo systemctl reload apache2
### so i send the change the dir.conf file using sudo vim /etc/apache2/mods-enabled/dir.conf i changed the order of arrangement and put the index.php first so the webpage can load the index.php first 
### Then reload my apache using sudo systemctl reload apache2


### To check if the webroot and web page is working i created an index.php file in the /var/www/projectlamp folder and added a php code <?php phpinfo(); .
### checked my ip address on the web browser with port 80 http://ip:80 and it worked fine showing the php version and all information about my server.

## Problems encounter.

### The only problem i encountered was when i wanted to check my ip on the browser my browser was set to default ### https so i couldnt connect i thouth i was wrong did the project all over again up to 4 times. it took me more than 6 hrs to figure this out because it worked on the terminal but not on the browser.
### well i did figure it out.




