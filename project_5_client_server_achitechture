# CLIENT SERVER ARCHITECTURE 

## Connecting two server. Server A will have mysql client this will connect to the mysql database on sever B. 

## SERVER CLIENT SETUP
### I installed mysql client on the client server (sudo apt install mysql-client )
### 

##  MYSQL SERVER SETUP

### I setup my AWS server and named it mysql server.
### I installed mysql server on the server (sudo apt install mysql-server).

### Just as i did in project 1 with mysql installation and creation of a user that can access the mysql link here https://github.com/Tobang1/darey.io-pbl/blob/c7390a536bd62bed498cb1f4e1251138a66349ce/project1.md 
### but here i created a new user but set the ip to the client ip address (CREATE USER 'Tobang'@'my client ip' IDENTIFIED BY 'password'; ).

### Then opened port 3306 in the mysql server security inbound rules and open the source to the client ip address.
### I then bind the address to 0.0.0.0 in mysql configuration file (sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf )

## CONNECTION OF CLIENT TO SERVER
### i connected the client using mysql -u tobang -p -h 19.44.55.2 "thats the server ip address".
### Then got connected.

## PROBLEM ENCOUNTERED
### The connection didnt go through at first but i resolved it by activating my ufw status to enabled (sudo status ufw enable) and adding port 3306 to the ufw rules (sudo ufw add 3306)
## then i restart mysql server (sudo systemctl restart mysql-server or sudo service restart mysql-server)

![alt text](![alt text](https://github.com/Tobang1/darey.io-pbl/blob/6712973d872efd33053ace5864540ea3603a0f9e/pbl_images/Project5_Client_server_Achitechture.PNG))
