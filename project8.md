# LOAD BALANCER SOLUTION WITH APACHE
## Implementing two webserver into a load balancer server .
## TWO WEB SERVER REDHAT
## ONE MYSQLSERVER UBUNTU
## ONE NFS SERVER REDHAT
## ONE LOAD BALANCER SERVER UBUNTU
### Using the 4 servers  servers in project 7 

## CONFIGURE APACHE AS A LOAD BALANCER

### created a new Ubuntu ec2 instance and named it Project-8-apache-lb 
### Opened port 80 on the lb server
### Installed APache on the Load Balancer (
### sudo apt update
### sudo apt install apache2 -y
### sudo apt-get install libxml2-dev

### Enable following modules:
### sudo a2enmod rewrite
### sudo a2enmod proxy
### sudo a2enmod proxy_balancer
### sudo a2enmod proxy_http
### sudo a2enmod headers
### sudo a2enmod lbmethod_bytraffic

### Restart apache2 service
### sudo systemctl restart apache2)
### then i made sure apache is up and running (sudo systemctl status apache2 and allowed ufw 80 and 3306)

## Configuring Load Balancing 
### I edited the load balancing configuration file by Add this configuration into this section 

<VirtualHost *:80> 
 </VirtualHost>

<Proxy "balancer://mycluster">

               BalancerMember http://<WebServer1-Private-IP-Address>:80 loadfactor=5 timeout=1
## ,
               BalancerMember http://<WebServer2-Private-IP-Address>:80 loadfactor=5 timeout=1
               ProxySet lbmethod=bytraffic
               # ProxySet lbmethod=byrequests
        </Proxy>

        ProxyPreserveHost On
        ProxyPass / balancer://mycluster/
        ProxyPassReverse / balancer://mycluster/

### Then restart my apache ( sudo systemctl restart apache2)
### i did study more about by traffic, bybusyness, byrequests, heartbeat, Apache mod_proxy_balancer module and sticky seeions
### Then i verified that my configuration works opening the browser an using (http://<Load-Balancer-Public-IP-Address-or-Public-DNS-Name>/index.php)
### Then i check the log of each server using (sudo tail /var/log/httpd/access_log)

## Configuring each DNS names Resolution 
### Switching between IP can be difficult to manage if you have many severs on your load balancer
### i edit the /etc/hosts  and adding the two server ip to it (sudo vi /etx/hosts) 
### sudo vi /etc/hosts

### Add 2 records into this file with Local IP address and arbitrary name for both of your Web Servers

<WebServer1-Private-IP-Address> Web1
<WebServer2-Private-IP-Address> Web2
### Then i updated my LB config file with the ip address

#### BalancerMember http://Web1:80 loadfactor=5 timeout=1
#### BalancerMember http://Web2:80 loadfactor=5 timeout=1

### then i did curl http://web1 and curl http://web2 on my LB local server and it worked .


## Problem encountered

### I forgot to configure my webserver 2 well then i got error 403. so i did the permission of setenforce 0 and it worked 


![alt text](![alt text](https://github.com/Tobang1/darey.io-pbl/blob/c580f2b566608531da60cba12f15f334bd2d0254/pbl_images/project8_load%20balancer.png))