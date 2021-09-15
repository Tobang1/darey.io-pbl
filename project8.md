# LOAD BALANCER SOLUTION WITH APACHE
## Implementing two webserver into a load balancer server .
## TWO WEB SERVER REDHAT
## ONE MYSQLSERVER UBUNTU
## ONE NFS SERVER REDHAT
## ONE LOAD BALANCER SERVER UBUNTU
### Using the 4 servers  servers in project 7 

## CONFIGURE APACHE AS A LOAD BALANCER

### cretaed a new Ubuntu ec2 instance and named it Project-8-apache-lb 
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

)