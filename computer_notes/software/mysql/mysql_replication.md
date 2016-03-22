
mysql_replication
# MySQL复制 #

MySQL replication官方文档  
[http://dev.mysql.com/doc/refman/5.7/en/replication.html](http://dev.mysql.com/doc/refman/5.7/en/replication.html)  

本文参考 高性能MySQL 第10章 复制  
MySQL版本 5.7  


## 复制概念 ##
MySQL内建的复制功能是构建基于MySQL的大规模, 高性能应用的基础. 这类应用使用所谓的"水平扩展"的架构. 通常为服务器配置一个或多个备库的方式来进行数据同步. 复制功能不仅有利于构建高性能的应用, 同时也是高可用, 可扩展性, 灾难恢复, 备份及数据仓库等工作的基础.  

复制解决的基本问题是让一台服务器的数据与其它服务器保持同步. 一台主库的数据可以同步到多台备库上. 备库本身也可以配置成另外一台服务器的主库. 主库和备库之间可以有多种不同的组合方式.  

MySQL支持两种复制方式, 基于行的复制和基于语句的复制. 基于语句的复制(也称为逻辑复制), 早在MySQL 3.23版本中就存在, 而基于行的复制方式在5.1版本中才被加进来. 这两种方式都是通过在主库上记录二进制日志, 在备库上重放日志的方式来实现的异步的数据复制. 这意味着, 在同一时间点备库上的数据可能与主库存在不一致. 并且无法保证主备之间的延迟, 一些大的语句可能导致备库产生几秒, 几分钟甚至几个小时的延迟.  

MySQL复制大部分是向后兼容的, 新版本的服务器可以作为老版本服务器的备库. 但反过来, 将老版本作为新版本服务器的备库通常是不行的. 因为它可能无法解析新版本所采用的特性或语法, 另外所使用的二进制文件的格式也可能不相同. 阅读每次版本更新的ChangeLog可以找到不同版本间做了什么修改.  

复制通常不会增加主库的开销, 主要是启用二进制日志带来的开销. 但出于备份或及时从崩溃中恢复的目的, 这点开销也是必要的.  

在一主库多备库的架构中, 写操作会被执行多次, 这时候整个系统的性能决定于最慢的部分.  

复制解决的问题  
> (1) 数据分布.  MySQL复制通常不会对带宽造成很大的压力. 但在5.1版本引入的基于行的复制会比传统的基于语句的复制模式的带宽压力更大. 可以随意地停止或开始复制. 并在不同的地理位置来分布数据备份. 例如不同的数据中心. 即使在不稳定的网络环境下, 远程复制也可以工作. 如果为了保持很低的延迟, 最好有一个稳定的, 低延迟连接.  
> (2) 负载均衡.  通过MySQL复制可以把读操作分布到多个服务器上, 实现对读密集型应用的优化, 并且实现很方便. 通过简单的代码修改就能实现基本的负载均衡. 小规模的应用, 可以简单地对机器名做硬编码或使用DNS轮询. 也可以使用更复杂的方法, 例如网络负载均衡这一类的标准负载均衡解决方案, 能够很好地将负载分配到不同的MySQL服务器上. Linux虚拟服务器(Linux Virtual Server, LVS)也能够很好地工作.  
> (3) 备份.  对于备份来说, 复制是一项很有意义的技术补充, 但复制既不是备份, 也不能取代备份.  
> (4) 高可用性和故障切换.  复制能够帮助应用程序避免MySQL单点失败, 一个包括复制的设计良好的故障切换系统能够显著地缩短宕机时间.  
> (5) MySQL升级测试.  使用一个更高版本的MySQL作为备库, 保证在升级全部实例前, 查询能够在备库按照预期执行.  



### 复制步骤 ###
复制有三个步骤:  
> (1) 在主库上把数据更改记录到二进制日志(Binary Log)中. (这些记录被称为二进制日志事件).  
> (2) 备库将主库上的日志复制到自己的中继日志(Relay Log)中.  
> (3) 备库读取中继日志中的事件, 将其重放到备库数据之上.  

第一步, 在主库上记录二进制日志. 在每次准备提交事务完成数据更新前, 主库将数据更新的事件记录到二进制日志中. MySQL会按事务提交的顺序而非每条语句的执行顺序来记录二进制日志. 在记录二进制日志后, 主库会告诉存储引擎可以提交事务了.  

第二步, 备库将主库的二进制日志复制到其本地的中继日志. 首先, 备库会启动一个工作线程, 称为I/O线程, I/O线程跟主库建立一个普通的客户端连接. 然后在主库上启动一个特殊的二进制转储(binlog dump)线程(该线程没有对应的SQL命令). 这个二进制转储线程会读取主库上二进制日志中的事件. 它不会对事件进行轮询. 如果该线程追赶上了主库, 它将进入睡眠状态, 直到主库发送信号量通知其有新的事件产生时才会被唤醒. 备库I/O线程会将接收到的事件记录到中继日志中.  

MySQL 4.0之前的复制与之后的版本相比改变很大, 例如MySQL最初的复制功能没有使用中继日志, 所以复制只用到了两个线程, 而不是现在的三个线程.  

备库的SQL线程执行最后一步, 该线程从中继日志中读取事件并在备库执行. 从而实现备库数据更新. 当SQL线程追赶上I/O线程时, 中继日志通常已经在系统缓存中, 所以中继日志的开销很低. SQL线程执行的事件也可以通过配置选项来决定是否写入其自己的二进制日志中.  



这种复制架构实现了获取事件和重放事件的解耦, 允许这两个过程异步进行. 也就是说, I/O线程能够独立于SQL线程之外工作. 但这种架构也限制了复制的过程. 其中最重要的一点是在主库上并发运行的查询在备库上只能串行化执行, 因为只有一个SQL线程来重放中继日志中的事件. 这是很多工作负载的性能瓶颈所在. 虽然有一些针对该问题的解决方案, 但大多数用户仍然受制于单线程.  







## 配置复制 ##

### 创建复制账号 ###
备库I/O线程以该用户名连接到主库并读取其二进制日志.  
```  
mysql> grant replication slave, replication client on *.* to replication@'192.168.1.%' identified by 'TestRep2016!';

mysql> select host,user from mysql.user;

```  
在主库和备库都创建该帐号, 把这个账号限制在本地网络, 因为这是一个特权账号(尽管该帐号无法执行select或修改数据, 但仍然能从二进制日志中获得一些数据).  

### 配置主库和备库 ###

#### 配置主库 ####
需要打开二进制日志并指定一个独一无二的服务器ID(Server ID), 在主库的my.cnf文件中:  
```  
$ sudo mkdir /var/lib/mysql-log

$ sudo vim /etc/my.cnf
[mysqld]
log_bin=/var/lib/mysql/mysql-bin
server_id=10
sync_binlog=1



$ sudo service mysqld restart
$ sudo service mysqld status

```  
默认服务器ID通常为1(这和版本有关, 一些MySQL版本根本不允许使用这个值).  

在主库上二进制日志最重要的选项是 sync_binlog:  
` sync_binlog=1 `  
开启该选项, MySQL每次在提交事务前会将二进制日志同步到磁盘上. 保证该服务器崩溃时不会丢失事件. 只适用于二进制日志, 不适用于中继日志.  



使用show master status命令, 检查输出是否一致.  
```  
mysql> show master status;
+------------------+----------+--------------+------------------+-------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
+------------------+----------+--------------+------------------+-------------------+
| mysql-bin.000001 |      154 |              |                  |                   |
+------------------+----------+--------------+------------------+-------------------+
1 row in set (0.00 sec)

mysql>

```  

#### 配置备库 ####
备库也需要在my.cnf中增加配置
```  
$ sudo vim /etc/my.cnf
[mysqld]
log_bin=/var/lib/mysql/mysql-bin
server_id=21
relay_log=/var/lib/mysql/mysql-relay-bin
log_slave_updates=1
read_only=1
skip_slave_start



$ sudo service mysqld restart
$ sudo service mysqld status

```  

** 启动复制 **

使用change master to语句, 完全替代了my.cnf中相应的设置, 并允许以后指向别的主库无须重启备库.  

```  
mysql> change master to master_host='192.168.1.101', master_user='replication', master_password='TestRep2016!', master_log_file='mysql-bin.000001', master_log_pos=0;
Query OK, 0 rows affected, 2 warnings (0.03 sec)

mysql> show slave status;

mysql> start slave;
Query OK, 0 rows affected (0.00 sec)

mysql> show slave status;


```  
master_log_pos参数设置为0, 表示从日志的开头读起. 通过show slave status语法检查复制是否正确.  


主库可以看到由备库I/O线程向主库发起的连接. 主库show processlist信息  
```  
mysql> show processlist;
+----+-------------+---------------------+---------+-------------+------+---------------------------------------------------------------+------------------+
| Id | User        | Host                | db      | Command     | Time | State                                                         | Info             |
+----+-------------+---------------------+---------+-------------+------+---------------------------------------------------------------+------------------+
|  3 | replication | 192.168.1.104:48170 | NULL    | Binlog Dump |  141 | Master has sent all binlog to slave; waiting for more updates | NULL             |
|  4 | root        | localhost           | db_test | Query       |    0 | starting                                                      | show processlist |
+----+-------------+---------------------+---------+-------------+------+---------------------------------------------------------------+------------------+
2 rows in set (0.00 sec)


```  


备库可以看到两个线程, 一个是I/O线程, 一个是SQL线程. 备库show processlist信息  
```  
mysql> show processlist;
+----+-------------+-----------+------+---------+------+--------------------------------------------------------+------------------+
| Id | User        | Host      | db   | Command | Time | State                                                  | Info             |
+----+-------------+-----------+------+---------+------+--------------------------------------------------------+------------------+
|  2 | root        | localhost | NULL | Query   |    0 | starting                                               | show processlist |
|  3 | system user |           | NULL | Connect |   41 | Waiting for master to send event                       | NULL             |
|  4 | system user |           | NULL | Connect |   41 | Slave has read all relay log; waiting for more updates | NULL             |
+----+-------------+-----------+------+---------+------+--------------------------------------------------------+------------------+
3 rows in set (0.00 sec)


```  

#### 验证复制效果 ####
在主库上创建数据库
```  
mysql> create database replication_test;
Query OK, 1 row affected (0.00 sec)

mysql> show create database replication_test;
+------------------+---------------------------------------------------------------------------+
| Database         | Create Database                                                           |
+------------------+---------------------------------------------------------------------------+
| replication_test | CREATE DATABASE `replication_test` /*!40100 DEFAULT CHARACTER SET utf8 */ |
+------------------+---------------------------------------------------------------------------+
1 row in set (0.00 sec)

mysql> use replication_test;
Database changed

mysql> create table test1 ( id int primary key not null, name varchar(60));
Query OK, 0 rows affected (0.02 sec)

mysql> insert into test1 (id, name) values (101, 'Name101');
Query OK, 1 row affected (0.00 sec)

mysql> insert into test1 (id, name) values (102, 'Name102');
Query OK, 1 row affected (0.01 sec)

mysql> insert into test1 (id, name) values (103, 'Name103');
Query OK, 1 row affected (0.00 sec)

mysql>

```

在备库上验证效果
```  
mysql> show databases;

mysql> use replication_test;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed

mysql> show tables;
+----------------------------+
| Tables_in_replication_test |
+----------------------------+
| test1                      |
+----------------------------+
1 row in set (0.00 sec)

mysql> select * from test1;
+-----+---------+
| id  | name    |
+-----+---------+
| 101 | Name101 |
| 102 | Name102 |
| 103 | Name103 |
+-----+---------+
3 rows in set (0.00 sec)

mysql>


```  



#### 推荐配置 ####
InnoDB强烈推荐设置如下:  
```  
innodb_flush_logs_at_trx_commit  # Flush every log write
innodb_support_xa=1  # MySQL 5.0 newer only
innodb_safe_binlog # MySQL 4.1 only, roughly equivalent to innodb_support_xa

```  


log_bin指定一个参数  
```  
log_bin=/var/lib/mysql/mysql-bin  # Good, specifies a path and base name  
# log_bin  # Bad, base name will be server's host name  

```  

备库上, 推荐开启如下配置  
```  
relay_log=/var/bin/mysql/relay-bin  
skip_slave_start
read_only

```  
relay_log  可以避免中继日志文件基于机器名来命名.  
skip_slave_start  阻止备库在崩溃后自动启动复制.  
read_only  可以阻止大部分用户更改临时表.  

master.info和中继日志文件都不是崩溃安全的, 如果使用MySQL 5.5并且不介意额外的fsync()导致的性能开销, 最好使用  
```  
sync_master_info=1
sync_relay_log=1
sync_relay_log_info=1

```  

配置relay_log_space_limit变量, 如果中继日志的大小之和超过这个值, I/O线程会停止, 等待SQL线程释放磁盘空间. 可能导致这些日志在主库崩溃时丢失.  


## 复制拓扑 ## 
可以在任意个主库和备库之间建立复制, 只有一个限制, 每一个备库只能有一个主库.  

基本原则:  
> (1) 一个MySQL备库实例只能有一个主库.  
> (2) 每个备库必须有一个唯一的服务器ID.  
> (3) 一个主库可以有多个备库 (或者相应的, 一个备库可以有多个兄弟备库).  
> (4) 如果打开了 log_slave_updates 选项, 一个备库可以把其主库上的数据变化传播到其它主库.  


### 一主库多备库 ###
一主多备的结构和基本配置差不多简单. 因为备库之间根本没有交互, 它们仅仅是连接到同一个主库上.  
在有少量写和大量读时, 这种配置是非常有用的.  

### 主动-主动模式下的主-主复制 ###  
主-主复制(也叫双主复制或双向复制), 包含两台服务器, 每一台配制成对方的主库和备库. 它们是一对主库.  
这种配置的最大问题是如何解决冲突, 两个可写的互主服务器导致的问题非常多. 这通常发生在两台服务器同时修改一行记录, 或同时在两台服务器上向一个包含auto_increment列的表里插入数据.  
MySQL不支持多主库复制.  
MySQL 5.0 增加了一些特性, 使得这种配置稍微安全了点. 就是设置 auto_increment_increment 和 auto_increment_offset. 通过这两个选项可以让MySQL自动为insert语句选择不互相冲突的值.  


### 主动-被动模式下的主-主复制 ###
是构建容错性和高可用性的非常强大的方式, 主要区别在于其中的一台服务器是只读的被动服务器.  
这种方式使得反复切换主动和被动服务器非常方便, 因为服务器是对称的. 可以在被动服务器上执行alter操作, 交换角色, 最后在先前的主动服务器上启动复制.  

 

















