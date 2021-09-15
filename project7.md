# DEVOPS TOOLING WEBSITE
## Connecting of three different servers together. 
## NFS Server1 with Storage REDHAT
## Database server UBUNTU
## WEB SERVER REDHAT


## 1. PREPARATION OF NFS SERVER AND STORAGE.
### I spin up new instance Redhat and added 3 new disk and mount it on /mnt/apps, /mnt/logs, /mnt/opt, directory but use xfs format
### Then i install NFS server (sudo yum -y update
### sudo yum install nfs-utils -y
### sudo systemctl start nfs-server.service
### sudo systemctl enable nfs-server.service
### sudo systemctl status nfs-server.service)
### Then i checked my subnet cidr on my instances under networking IPV4

### I set permission to all my mounted directory and restart my nfs server
### sudo chown -R nobody: /mnt/apps
### sudo chown -R nobody: /mnt/logs
### sudo chown -R nobody: /mnt/opt

### sudo chmod -R 777 /mnt/apps
### sudo chmod -R 777 /mnt/logs
### sudo chmod -R 777 /mnt/opt

### sudo systemctl restart nfs-server.service
### Then i configured access to my subnet client under the /etc/exports file. and added the below code in the file
### /mnt/apps <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
### /mnt/logs <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
### /mnt/opt <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)

### then i saved it and  export it (sudo exportfs -arv)
### I then checked which port is been used by the NFS server using (rcpinfo -p | grep nfs) and i open it under the security inbound rules.
### i opened port TCP111, UDP 111, UDP 2049.

## Configuration of the Database Server.
### I opened a new instance for my db server using ubuntu. and created a new database called tooling and a new user named webaccesss and grant all privileges to the user on the database tooling from the webserver 
### I did bound my address so i can connect to the database remotely from my web server

## Preparing The Web Server.
### First i spined up another instances for my Webserver using redhat
### Then i installed NFS client (sudo yum install nfs-utils nfs4-acl-tools -y)
### then created a new directory (sudo mkdir -p /var/www)
### And i did mount my nfs server disk /mnt/apps on /var/www and /mnt/logs on /var/logs on my webserver (sudo mount -t nfs -o rw,nosuid <NFS-Server-Private-IP-Address>:/mnt/apps /var/www)
### then i verify my mount  using (df -h and mounted it permanently editing the /etc/fstab) added the code in the file (<NFS-Server-Private-IP-Address>:/mnt/apps /var/www nfs defaults 0 0)
### Next i installed apache (sudo yum install httpd -y)
### then i repeated same thing for another new server on preparing the server
## Git Hub
### I forked the Git tooling repository from https://github.com/darey-io/tooling.git then copy the http pull link
### I then make a new directory /git and installed git using (yum install git)
### then i initialize the git local directory using (git init) 
### then i pulled the repository into my local directory (git pull https://github.com/darey-io/tooling.git)
### then copied the repository into my /var/ww/html directory.
### restart my httpd and open port 80 on security.
### I was getting error 403 when i did curl -v to tail the file then i used (sudo setenforce 0 )
### then changed it permanently editing the /etc/sysconfig/selinux file and set the selinux = disabled
### Then i updated the website configuration to connect to the database in the function.php file and i applied tooling-db.sql script to the database (mysql -u webaccess tooling < tooling-db.sql -p )
### I created new MYSQL admin user with username myuser and password password (INSERT INTO ‘users’ (‘id’, ‘username’, ‘password’, ’email’, ‘user_type’, ‘status’) VALUES (1, ‘myuser’, ‘5f4dcc3b5aa765d61d8327deb882cf99’, ‘user@mail.com’, ‘admin’, ‘1’);

### Teh opened my browser http://publicIP/index.php and i login with myuser as user


## Problems encountered
## I didnt open the port 3306 and port 80 on my db and web server



![alt text](![alt text](https://github.com/Tobang1/darey.io-pbl/blob/bbe7178c179f6ffb1ed673c9ee171a88ebf4cda5/pbl_images/project7_database.png))
![alt text](![alt text](https://github.com/Tobang1/darey.io-pbl/blob/bbe7178c179f6ffb1ed673c9ee171a88ebf4cda5/pbl_images/project7_mount.png))
![alt text](![alt text](https://github.com/Tobang1/darey.io-pbl/blob/bbe7178c179f6ffb1ed673c9ee171a88ebf4cda5/pbl_images/project7_index.png))
