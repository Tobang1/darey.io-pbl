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