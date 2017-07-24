

# firewall  
```  
# uname -a
Linux centos73minimal 3.10.0-514.el7.x86_64 #1 SMP Tue Nov 22 16:42:41 UTC 2016 x86_64 x86_64 x86_64 GNU/Linux

# service firewalld status
# systemctl status firewalld


# firewall-cmd --version
0.4.3.2

# firewall-cmd --state

# firewall-cmd --get-zones

# firewall-cmd --get-services

# firewall-cmd --get-active-zones
public
  interfaces: enp0s3

# firewall-cmd --zone=public --list-ports

# firewall-cmd --zone=public --add-port=22/tcp --permanent
# firewall-cmd --zone=public --add-port=80/tcp --permanent
# firewall-cmd --zone=public --add-port=9999/tcp --permanent

# firewall-cmd --reload
  
  
```  



# vim  

```  
# yum search vim
# yum -y install vim

```  


# nc  

```  
# yum search nmap-ncat
# yum -y install nmap-ncat

# nc -lk 9999

```  

