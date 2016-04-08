centos7_install_hive

# CentOS 7 安装 Hive #

[下载hive](http://mirror.bit.edu.cn/apache/hive/)  

http://my.oschina.net/u/204498/blog/522772

## 需要JDK Hadoop MySQL ##

```  
$ java -version
java version "1.8.0_77"
Java(TM) SE Runtime Environment (build 1.8.0_77-b03)
Java HotSpot(TM) 64-Bit Server VM (build 25.77-b03, mixed mode)

```  

创建MySQL账号(默认使用Derby报错, 改用MySQL)  

```  
mysql> grant all privileges on *.* to hive@'hadoop1' identified by 'GitHub2016!';
Query OK, 0 rows affected, 1 warning (0.04 sec)

$ mysql -hhadoop1 -uhive -p

mysql> create database test1;
Query OK, 1 row affected (0.00 sec)

mysql> show create database test1;
+----------+----------------------------------------------------------------+
| Database | Create Database                                                |
+----------+----------------------------------------------------------------+
| test1    | CREATE DATABASE `test1` /*!40100 DEFAULT CHARACTER SET utf8 */ |
+----------+----------------------------------------------------------------+
1 row in set (0.00 sec)

mysql> drop database test1;
Query OK, 0 rows affected (0.00 sec)


# /usr/local/hive-2.0.0/lib
```  
将 mysql-connector-java-5.1.38.jar 上传到 ` /usr/local/hive-2.0.0/lib `.  



## 安装Hive ##

下载Hive到/soft目录. 配置Hadoop安装目录和HDFS路径.  


```  
$ cd /soft/
$ sudo tar -zxvf apache-hive-2.0.0-bin.tar.gz
$ sudo mv apache-hive-2.0.0-bin hive-2.0.0
$ sudo cp -R /soft/hive-2.0.0/ /usr/local/


$ sudo cp /usr/local/hive-2.0.0/conf/hive-default.xml.template /usr/local/hive-2.0.0/conf/hive-default.xml
$ sudo cp /usr/local/hive-2.0.0/conf/hive-default.xml.template /usr/local/hive-2.0.0/conf/hive-site.xml
$ sudo cp /usr/local/hive-2.0.0/conf/hive-env.sh.template /usr/local/hive-2.0.0/conf/hive-env.sh

$ sudo vim /usr/local/hive-2.0.0/conf/hive-site.xml

  <property>
    <name>hive.metastore.warehouse.dir</name>
    <value>hdfs://hadoop1:9000/hive/warehouse</value>
    <description>location of default database for the warehouse</description>
  </property>
  

$ sudo vim /usr/local/hive-2.0.0/conf/hive-env.sh
# HADOOP_HOME=${bin}/../../hadoop
HADOOP_HOME=/usr/local/hadoop-2.7.2


$ sudo /usr/local/hadoop-2.7.2/bin/hdfs dfs -mkdir /hive/warehouse
$ sudo /usr/local/hadoop-2.7.2/bin/hdfs dfs -chmod g+w /hive/warehouse
$ sudo /usr/local/hadoop-2.7.2/bin/hdfs dfs -chmod g+w /tmp
```  

Hive 使用Derby报错, 修改为MySQL.  

```  

Exception in thread "main" java.lang.RuntimeException: Unable to instantiate org.apache.hadoop.hive.ql.metadata.SessionHiveMetaStoreClient
Caused by: javax.jdo.JDOFatalDataStoreException: 
Unable to open a test connection to the given database. 
JDBC url = jdbc:derby:;databaseName=metastore_db;create=true, username = APP. 
Terminating connection pool (set lazyInit to true if you expect to start your database after your app). 
Original Exception: ------
java.sql.SQLException: Failed to start database 'metastore_db' with class loader sun.misc.Launcher$AppClassLoader@1ee0005, see the next exception for details.
Caused by: java.sql.SQLException: Another instance of Derby may have already booted the database /home/bigdata/metastore_db.
Caused by: ERROR XSDB6: Another instance of Derby may have already booted the database /home/bigdata/metastore_db.
Caused by: java.sql.SQLException: Failed to start database 'metastore_db' with class loader sun.misc.Launcher$AppClassLoader@1ee0005, see the next exception for details.
Caused by: java.sql.SQLException: Another instance of Derby may have already booted the database /home/bigdata/metastore_db.
Caused by: ERROR XSDB6: Another instance of Derby may have already booted the database /home/bigdata/metastore_db.


$ sudo vim /usr/local/hive-2.0.0/conf/hive-site.xml
  <property>
    <name>javax.jdo.option.ConnectionPassword</name>
    <!-- <value>mine</value> -->
    <value>GitHub2016!</value>
    <description>password to use against metastore database</description>
  </property>

  
  <property>
    <name>javax.jdo.option.ConnectionURL</name>
    <!-- <value>jdbc:derby:;databaseName=metastore_db;create=true</value> -->
    <value>jdbc:mysql://hadoop1:3306/hivemetadb?createDatabaseIfNotExist=true&amp;characterEncoding=utf-8&amp;userSSL=false</value>
    <description>JDBC connect string for a JDBC metastore</description>
  </property>



  
  <property>
    <name>javax.jdo.option.ConnectionDriverName</name>
    <!-- <value>org.apache.derby.jdbc.EmbeddedDriver</value> -->
    <value>com.mysql.jdbc.Driver</value>
    <description>Driver class name for a JDBC metastore</description>
  </property>

  
  <property>
    <name>javax.jdo.option.ConnectionUserName</name>
    <!-- <value>APP</value> -->
    <value>hive</value>
    <description>Username to use against metastore database</description>
  </property>


  <property>
    <name>datanucleus.schema.autoCreateAll</name>
    <!-- <value>false</value> -->
    <value>true</value>
    <description>creates necessary schema on a startup if one doesn't exist. set this to false, after creating it once</description>
  </property>
  
  
```  


Hive解析文件路径报错  

```  
Exception in thread "main" java.lang.IllegalArgumentException: 
java.net.URISyntaxException: Relative path in absolute URI: ${system:java.io.tmpdir%7D/$%7Bsystem:user.name%7D

$ echo ${system:java.io.tmp.dir}
$ echo ${system:user.name}

$ sudo cat /usr/local/hive-2.0.0/conf/hive-site.xml | grep "system:java.io"
    <value>${system:java.io.tmpdir}/${system:user.name}</value>
    <value>${system:java.io.tmpdir}/${hive.session.id}_resources</value>
    <value>${system:java.io.tmpdir}/${system:user.name}</value>
    <value>${system:java.io.tmpdir}/${system:user.name}/operation_logs</value>

$ sudo mkdir -p /var/hive/tmp

  <property>
    <name>hive.exec.local.scratchdir</name>
    <!-- <value>${system:java.io.tmpdir}/${system:user.name}</value> -->
    <value>/var/hive/tmp</value>
    <description>Local scratch space for Hive jobs</description>
  </property>  
  
  <property>
    <name>hive.downloaded.resources.dir</name>
    <!-- <value>${system:java.io.tmpdir}/${hive.session.id}_resources</value> -->
    <value>/var/hive/tmp/${hive.session.id}_resources</value>
    <description>Temporary local directory for added resources in the remote file system.</description>
  </property>


  <property>
    <name>hive.querylog.location</name>
    <!-- <value>${system:java.io.tmpdir}/${system:user.name}</value> -->
    <value>/var/hive/tmp</value>
    <description>Location of Hive run time structured log file</description>
  </property>
  

  <property>
    <name>hive.server2.logging.operation.log.location</name>
    <!-- <value>${system:java.io.tmpdir}/${system:user.name}/operation_logs</value> -->
    <value>/var/hive/tmp/operation_logs</value>
    <description>Top level directory where operation logs are stored if logging functionality is enabled</description>
  </property>
  
  
```  

## 使用hive命令行 ##

[Hive CLI](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli)  
  
准备导入hive数据文件. 导入hive.  

```  


$ sudo vim /soft/hive_data1.txt
1       Name1   Info1
2/tName2/tInfo2
3/tName3/tInfo3


$ sudo /usr/local/hive-2.0.0/bin/hive
which: no hbase in (/sbin:/bin:/usr/sbin:/usr/bin)
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/usr/local/hive-2.0.0/lib/hive-jdbc-2.0.0-standalone.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/usr/local/hive-2.0.0/lib/log4j-slf4j-impl-2.4.1.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/usr/local/hbase-1.1.3/lib/slf4j-log4j12-1.7.5.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/usr/local/hadoop-2.7.2/share/hadoop/common/lib/slf4j-log4j12-1.7.10.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
SLF4J: Actual binding is of type [org.apache.logging.slf4j.Log4jLoggerFactory]

Hive-on-MR is deprecated in Hive 2 and may not be available in the future versions. 
Consider using a different execution engine (i.e. spark, tez) or using Hive 1.X releases.


hive> create table hive_test1(id int, name string, info string) row format delimited fields terminated by '/t';
OK
Time taken: 1.741 seconds


hive> describe hive_test1;
OK
id                      int
name                    string
info                    string
Time taken: 0.233 seconds, Fetched: 3 row(s)


hive> load data local inpath '/soft/hive_data1.txt' into table hive_test1;
Loading data to table default.hive_test1
OK
Time taken: 0.498 seconds


hive> select * from hive_test1;
OK
NULL    NULL    NULL
2       tName2  tInfo2
3       tName3  tInfo3
Time taken: 0.188 seconds, Fetched: 3 row(s)

# 数据可以重复导入
hive> load data local inpath '/soft/hive_data1.txt' into table hive_test1;
hive> select * from hive_test1;


# overwrite会覆盖原有数据
hive> load data local inpath '/soft/hive_data1.txt' overwrite into table hive_test1;
hive> select * from hive_test1;


hive> show databases;
OK
default
Time taken: 0.01 seconds, Fetched: 1 row(s)


```  

## 启动metastore ##

```  

$ sudo /usr/local/hive-2.0.0/bin/hive --service metastore
which: no hbase in (/sbin:/bin:/usr/sbin:/usr/bin)
Starting Hive Metastore Server
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/usr/local/hive-2.0.0/lib/hive-jdbc-2.0.0-standalone.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/usr/local/hive-2.0.0/lib/log4j-slf4j-impl-2.4.1.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/usr/local/hbase-1.1.3/lib/slf4j-log4j12-1.7.5.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/usr/local/hadoop-2.7.2/share/hadoop/common/lib/slf4j-log4j12-1.7.10.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
SLF4J: Actual binding is of type [org.apache.logging.slf4j.Log4jLoggerFactory]


$ sudo jps
5952 RunJar
6486 RunJar

$ sudo ps -ef | grep metastore

```  

  

## 启动hiverserver2 ##

```  
$ sudo /usr/local/hive-2.0.0/bin/hiveserver2
which: no hbase in (/sbin:/bin:/usr/sbin:/usr/bin)
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/usr/local/hive-2.0.0/lib/hive-jdbc-2.0.0-standalone.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/usr/local/hive-2.0.0/lib/log4j-slf4j-impl-2.4.1.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/usr/local/hbase-1.1.3/lib/slf4j-log4j12-1.7.5.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/usr/local/hadoop-2.7.2/share/hadoop/common/lib/slf4j-log4j12-1.7.10.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.


# 也可以使用hive命令启动
$ sudo /usr/local/hive-2.0.0/bin/hive --service hiveserver2


$ sudo jps

$ sudo ps -ef | grep hiveserver2

```  



## 使用beeline命令行 ## 

### not allowed to impersonate anonymous异常, 修改Hadoop core-site.xml  ###

参考 [user-oozie-is-not-allowed-to-impersonate](http://stackoverflow.com/questions/16582126/getting-e0902-exception-occured-user-oozie-is-not-allowed-to-impersonate-ooz)  


```  
$ sudo /usr/local/hive-2.0.0/bin/beeline
which: no hbase in (/sbin:/bin:/usr/sbin:/usr/bin)
Beeline version 2.0.0 by Apache Hive


beeline> !connect jdbc:hive2://localhost:10000/default
Connecting to jdbc:hive2://localhost:10000/default
Enter username for jdbc:hive2://localhost:10000/default:
Enter password for jdbc:hive2://localhost:10000/default:
Error: Failed to open new session: java.lang.RuntimeException: 
org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.security.authorize.AuthorizationException): 
User: root is not allowed to impersonate anonymous (state=,code=0)


$ sudo vim /usr/local/hadoop-2.7.2/etc/hadoop/core-site.xml
  <property>
    <name>hadoop.proxyuser.root.hosts</name>
    <value>*</value>
  </property>
  <property>
    <name>hadoop.proxyuser.root.groups</name>
    <value>*</value>
  </property>


$ sudo /usr/local/hadoop-2.7.2/sbin/stop-dfs.sh

$ sudo /usr/local/hadoop-2.7.2/sbin/start-dfs.sh

$ sudo jps

```  

### /tmp/hive Permission denied异常, hdfs-site.xml设置dfs.permissions为false  ###


```  

$ sudo /usr/local/hive-2.0.0/bin/beeline
Error: Failed to open new session: 
java.lang.RuntimeException: org.apache.hadoop.security.AccessControlException: 
Permission denied: user=anonymous, access=EXECUTE, inode="/tmp/hive":root:supergroup:drwx-w----

$ sudo /usr/local/hadoop-2.7.2/bin/hdfs dfs -chmod g+w /tmp/hive

$ sudo /usr/local/hive-2.0.0/bin/beeline

$ sudo vim /usr/local/hadoop-2.7.2/etc/hadoop/hdfs-site.xml
  <property>
    <name>dfs.permissions</name>
    <value>false</value>
    <description>
    If true, enable permission checking in HDFS.
    If false, permission checking is turned off.
   </description>
  </property>


$ sudo /usr/local/hadoop-2.7.2/sbin/stop-dfs.sh
$ sudo /usr/local/hadoop-2.7.2/sbin/start-dfs.sh
$ sudo jps

  
```  

### 正常启动beeline ###

```  
$ sudo /usr/local/hive-2.0.0/bin/beeline

beeline> !connect jdbc:hive2://hadoop1:10000
Connecting to jdbc:hive2://hadoop1:10000
Enter username for jdbc:hive2://hadoop1:10000:
Enter password for jdbc:hive2://hadoop1:10000:
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/usr/local/hive-2.0.0/lib/hive-jdbc-2.0.0-standalone.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/usr/local/hive-2.0.0/lib/log4j-slf4j-impl-2.4.1.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/usr/local/hbase-1.1.3/lib/slf4j-log4j12-1.7.5.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/usr/local/hadoop-2.7.2/share/hadoop/common/lib/slf4j-log4j12-1.7.10.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
SLF4J: Actual binding is of type [org.apache.logging.slf4j.Log4jLoggerFactory]
Connected to: Apache Hive (version 2.0.0)
Driver: Hive JDBC (version 2.0.0)
16/04/08 11:48:20 [main]: WARN jdbc.HiveConnection: Request to set autoCommit to false; Hive does not support autoCommit=false.
Transaction isolation: TRANSACTION_REPEATABLE_READ


0: jdbc:hive2://hadoop1:10000> show tables;
INFO  : OK
+-------------+--+
|  tab_name   |
+-------------+--+
| hive_test1  |
+-------------+--+
1 row selected (1.212 seconds)


0: jdbc:hive2://hadoop1:10000> create table bee_test(id int, name string, info string, address string);
INFO  : OK
No rows affected (0.987 seconds)


0: jdbc:hive2://hadoop1:10000> show tables;
INFO  : OK
+-------------+--+
|  tab_name   |
+-------------+--+
| bee_test    |
| hive_test1  |
+-------------+--+
2 rows selected (0.089 seconds)

0: jdbc:hive2://hadoop1:10000> describe bee_test;
INFO  : OK
+-----------+------------+----------+--+
| col_name  | data_type  | comment  |
+-----------+------------+----------+--+
| id        | int        |          |
| name      | string     |          |
| info      | string     |          |
| address   | string     |          |
+-----------+------------+----------+--+
4 rows selected (0.176 seconds)


0: jdbc:hive2://hadoop1:10000> select * from hive_test1;
INFO  : OK
+----------------+------------------+------------------+--+
| hive_test1.id  | hive_test1.name  | hive_test1.info  |
+----------------+------------------+------------------+--+
| NULL           | NULL             | NULL             |
| 2              | tName2           | tInfo2           |
| 3              | tName3           | tInfo3           |
+----------------+------------------+------------------+--+
3 rows selected (1.667 seconds)


```  













 
   



  
  
