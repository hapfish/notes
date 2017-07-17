
# CentOS 7 install mysql 5.7 #


添加sudo权限, 并安装vim确认
```  
$ su root
# visudo
## Allow root to run any commands anywhere
root    ALL=(ALL)       ALL
lidongxu ALL=(ALL)     ALL

# exit

$ sudo yum repolist
$ sudo yum -y install vim


```  






## 使用yum安装mysql ##

MySQL yum repo  
[http://dev.mysql.com/downloads/repo/yum/](http://dev.mysql.com/downloads/repo/yum/)

可以手动添加mysql yum repo  
[http://dev.mysql.com/doc/mysql-yum-repo-quick-guide/en/](http://dev.mysql.com/doc/mysql-yum-repo-quick-guide/en/)  

```  
$ sudo vim /etc/yum.repos.d/mysql-community.repo
[mysql57-community]
name=MySQL 5.7 Community Server
baseurl=http://repo.mysql.com/yum/mysql-5.7-community/el/7/$basearch/
enabled=1
gpgcheck=1
# gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
gpgkey=http://repo.mysql.com/RPM-GPG-KEY-mysql


$ sudo yum search mysql

$ sudo yum repolist
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.btte.net
 * extras: mirrors.btte.net
 * updates: mirrors.opencas.cn
repo id                                    repo name                                    status
base/7/x86_64                              CentOS-7 - Base                              9,007
extras/7/x86_64                            CentOS-7 - Extras                              226
mysql57-community/x86_64                   MySQL 5.7 Community Server                      56
updates/7/x86_64                           CentOS-7 - Updates                             952
repolist: 10,241


```  





## 安装MySQL Community Server ##

```  
$ sudo yum install -y mysql-community-server

$ sudo service mysqld start

$ sudo service mysqld status
Redirecting to /bin/systemctl status  mysqld.service
● mysqld.service - MySQL Server
   Loaded: loaded (/usr/lib/systemd/system/mysqld.service; enabled; vendor preset: disabled)
   Active: active (running) since Tue 2016-03-22 13:18:19 CST; 16s ago
  Process: 19083 ExecStart=/usr/sbin/mysqld --daemonize --pid-file=/var/run/mysqld/mysqld.pid $MYSQLD_OPTS (code=exited, status=0/SUCCESS)
  Process: 19010 ExecStartPre=/usr/bin/mysqld_pre_systemd (code=exited, status=0/SUCCESS)
 Main PID: 19086 (mysqld)
   CGroup: /system.slice/mysqld.service
           └─19086 /usr/sbin/mysqld --daemonize --pid-file=/var/run/mysqld/mysqld.pid

Mar 22 13:18:14 mysqltest1 systemd[1]: Starting MySQL Server...
Mar 22 13:18:19 mysqltest1 systemd[1]: Started MySQL Server.

$ sudo chkconfig mysqld on

$ sudo grep 'temporary password' /var/log/mysqld.log
2016-03-22T05:18:15.520264Z 1 [Note] A temporary password is generated for root@localhost: uqApwQkNd0;u


```  






## 登录MySQL修改密码 ##
修改密码成功后, 使用新密码登录.  

```  
$ mysql -uroot -p


mysql> alter user 'root'@'localhost' identified by 'GitHub2016!';
Query OK, 0 rows affected (0.00 sec)

mysql> select host, user from mysql.user;

$ mysql -uroot -p

mysql> select host, user, authentication_string, password_expired, password_last_changed, password_lifetime, account_locked from mysql.user;

```  





## 修改my.cnf配置文件 ##
修改MySQL默认字符集为utf8, 存储引擎为InnoDB. 创建数据库和数据表验证.  

```  
$ sudo vim /etc/my.cnf
[mysqld]

character_set_server=utf8
default-storage-engine=InnoDB


$ sudo service mysqld restart
$ sudo service mysqld status

$ mysql -uroot -p

mysql> create database db_test;
Query OK, 1 row affected (0.00 sec)

mysql> show create database db_test;
+----------+------------------------------------------------------------------+
| Database | Create Database                                                  |
+----------+------------------------------------------------------------------+
| db_test  | CREATE DATABASE `db_test` /*!40100 DEFAULT CHARACTER SET utf8 */ |
+----------+------------------------------------------------------------------+
1 row in set (0.00 sec)



mysql> use db_test;
Database changed

mysql> create table t1 ( id int primary key not null, name varchar(60));
Query OK, 0 rows affected (0.04 sec)

mysql> show create table t1;
+-------+-------------------------------------------------------------------------------------------------------------------------------------------+
| Table | Create Table                                                                                                                              |
+-------+-------------------------------------------------------------------------------------------------------------------------------------------+
| t1    | CREATE TABLE `t1` (
  `id` int(11) NOT NULL,
  `name` varchar(60) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 |
+-------+-------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)

mysql>


# 创建用户  
mysql> grant all privileges on *.* to github_demo@'%' identified by 'GitHubDemo2017!';

# mysql -ugithub_demo -p


mysql> create database demo_database;
Query OK, 1 row affected (0.00 sec)

mysql> show create database demo_database;
+---------------+------------------------------------------------------------------------+
| Database      | Create Database                                                        |
+---------------+------------------------------------------------------------------------+
| demo_database | CREATE DATABASE `demo_database` /*!40100 DEFAULT CHARACTER SET utf8 */ |
+---------------+------------------------------------------------------------------------+
1 row in set (0.00 sec)

mysql> use demo_database;


Database changed
mysql> create table demo_person(id bigint primary key not null auto_increment,
    -> name varchar(64),
    -> unique index unique_name(name));
Query OK, 0 rows affected (0.01 sec)



mysql> insert into demo_person(id, name) values (1001, 'Tom@qq.com');
Query OK, 1 row affected (0.00 sec)

mysql> insert into demo_person(id, name) values (1002, 'demo1002@qq.com');
Query OK, 1 row affected (0.00 sec)

mysql> insert into demo_person(id, name) values (1003, '测试1003');
Query OK, 1 row affected (0.00 sec)


```  



