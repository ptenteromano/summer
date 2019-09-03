# Deploy NFS server and client on Ubuntu

## Prerequisite
we need a runnig cluster (master and workers connected)
to check the ip of [server] or [clients], use the following code:
```
dig +short myip.opendns.com @resolver1.opendns.com
```
### Configure server(master)
1. On master: install and create an export dir /mnt/linuxidc
```
sudo apt-get update
sudo apt install nfs-kernel-server
sudo mkdir -p /mnt/linuxidc
sudo chown nobody:nogroup /mnt/linuxidc
sudo chmod 777 /mnt/linuxidc
```

2. On master: give clinet permission by edit exports file
```
sudo nano /etc/exports
```
Add the following lines to the exports file:
```
/mnt/linuxidc [client1IP](rw,sync,no_subtree_check)
/mnt/linuxidc [client2IP](rw,sync,no_subtree_check)
```

3. On master: apply the configuration
```
sudo exportfs -a
sudo systemctl restart nfs-kernel-server
```

4. On master: open Firewall for client(s):
```
sudo ufw allow from [clientIP or clientSubnetIP] to any port nfs
sudo ufw status
``` 

### Configure clients(workers)
5. On workers: install and create sharing dir /mnt/linuxidc_client
```
sudo apt-get update
sudo apt-get install nfs-common
sudo mkdir -p /mnt/linuxidc_client
```

6. On workers: 
```
sudo mount [serverIP]:/mnt/linuxidc /mnt/linuxidc_client
```

### Testing
7. Create a new file in master /mnt/linuxidc, see if the file is shared to workers /mnt/linuxidc_client







