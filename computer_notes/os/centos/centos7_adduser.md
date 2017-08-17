centos7_adduser

# CentOS7 添加用户 #

## 添加用户并设置密码 ##

```  
[root@hadoop1 ~]# useradd bigdata
[root@hadoop1 ~]# passwd bigdata
Changing password for user bigdata.
New password:
BAD PASSWORD: The password is shorter than 8 characters
Retype new password:
passwd: all authentication tokens updated successfully.

```  


## 配置sudo不使用密码 ##

在 `/etc/sudoers` 中新增一行用户配置, 使用 `NOPASSWD:`
```  
[root@hadoop1 ~]# visudo
## Allow root to run any commands anywhere
root    ALL=(ALL)       ALL
bigdata   ALL=NOPASSWD:ALL


```  


## 使用新用户验证配置 ##

使用sudo安装vim, 并新建目录和文件. 使用vim在文件内写入数据.  

```  
[bigdata@hadoop1 ~]$ sudo yum search vim

[bigdata@hadoop1 ~]$ sudo yum install -y vim


[bigdata@hadoop1 ~]$ sudo mkdir -p /var/testdata/data1
[bigdata@hadoop1 ~]$ sudo vim /var/testdata/data1/file1.txt


[bigdata@hadoop1 ~]$ ll -a /var/testdata/data1/
total 4
drwxr-xr-x. 2 root root 22 Apr  5 10:28 .
drwxr-xr-x. 3 root root 18 Apr  5 10:26 ..
-rw-r--r--. 1 root root 21 Apr  5 10:27 file1.txt


```  

## 用户生成RSA密钥对 ##

```  
[bigdata@hadoop1 ~]$ ssh-keygen -b 2048 -t "rsa"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/bigdata/.ssh/id_rsa):
Created directory '/home/bigdata/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/bigdata/.ssh/id_rsa.
Your public key has been saved in /home/bigdata/.ssh/id_rsa.pub.
The key fingerprint is:
74:d1:ce:66:44:b9:b3:ee:9e:42:ec:54:7d:23:bf:80 bigdata@hadoop1
The key's randomart image is:
+--[ RSA 2048]----+
|          .o..   |
|           .+    |
|        . .+ o   |
|       . .  O....|
|        S. +.oo..|
|          +E.. . |
|         + .  . .|
|          o .. . |
|           ++    |
+-----------------+
[bigdata@hadoop1 ~]$


[bigdata@hadoop1 ~]$ ll -a
total 16
drwx------. 3 bigdata bigdata  85 Apr  5 10:38 .
drwxr-xr-x. 3 root    root     20 Apr  5 10:17 ..
-rw-r--r--. 1 bigdata bigdata  18 Nov 20 13:02 .bash_logout
-rw-r--r--. 1 bigdata bigdata 193 Nov 20 13:02 .bash_profile
-rw-r--r--. 1 bigdata bigdata 231 Nov 20 13:02 .bashrc
drwx------. 2 bigdata bigdata  36 Apr  5 10:38 .ssh
-rw-------. 1 bigdata bigdata 700 Apr  5 10:27 .viminfo


[bigdata@hadoop1 ~]$ ll -a .ssh/
total 8
drwx------. 2 bigdata bigdata   36 Apr  5 10:38 .
drwx------. 3 bigdata bigdata   85 Apr  5 10:38 ..
-rw-------. 1 bigdata bigdata 1671 Apr  5 10:38 id_rsa
-rw-r--r--. 1 bigdata bigdata  397 Apr  5 10:38 id_rsa.pub


```  

