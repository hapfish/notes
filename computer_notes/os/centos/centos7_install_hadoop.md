centos7_install_hadoop


# CentOS 7 安装 hadoop #

[下载Oracle JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html)  
[Linux install Oracle JDK](http://docs.oracle.com/javase/8/docs/technotes/guides/install/linux_jdk.html#BJFGGEFG)  

[hadoop官方文档](http://hadoop.apache.org/)  

[hadoop 2.7.2文档](http://hadoop.apache.org/docs/r2.7.2/)  

[下载hadoop](http://mirror.bit.edu.cn/apache/hadoop/common/)  
[下载hadoop 2.7.7](http://mirror.bit.edu.cn/apache/hadoop/common/hadoop-2.7.2/)  

[Pseudo Distributed Operation](http://hadoop.apache.org/docs/r2.7.2/hadoop-project-dist/hadoop-common/SingleCluster.html#Pseudo-Distributed_Operation)


## 下载JDK和hadoop ##

下载JDK, hadoop并上传到 /soft目录

```  
$ sudo mkdir /soft
$ cd /soft/


[bigdata@hadoop1 soft]$ sudo rpm -ivh jdk-8u77-linux-x64.rpm
Preparing...                          ################################# [100%]
Updating / installing...
   1:jdk1.8.0_77-2000:1.8.0_77-fcs    ################################# [100%]
Unpacking JAR files...
        tools.jar...
        plugin.jar...
        javaws.jar...
        deploy.jar...
        rt.jar...
        jsse.jar...
        charsets.jar...
        localedata.jar...
        jfxrt.jar...


$ java -version
java version "1.8.0_77"
Java(TM) SE Runtime Environment (build 1.8.0_77-b03)
Java HotSpot(TM) 64-Bit Server VM (build 25.77-b03, mixed mode)
		
```  


## 安装hadoop ##


```  
$ sudo vim /etc/hosts
192.168.1.100    hadoop1

$ ping hadoop1 -c 5



$ sudo tar -zxvf hadoop-2.7.2.tar.gz
$ sudo cp -R /soft/hadoop-2.7.2 /usr/local/


$ sudo mkdir -p /var/hadoop/data/namenode
$ sudo mkdir -p /var/hadoop/data/datanode
$ sudo mkdir -p /var/hadoop/data/temp

```  

编辑 hadoop-env.sh  
```  
$ sudo vim hadoop-env.sh
JAVA_HOME=/usr/java/jdk1.8.0_77

$ /usr/local/hadoop-2.7.2/bin/hadoop version
Hadoop 2.7.2
Subversion https://git-wip-us.apache.org/repos/asf/hadoop.git -r b165c4fe8a74265c792ce23f546c64604acf0e41
Compiled by jenkins on 2016-01-26T00:08Z
Compiled with protoc 2.5.0
From source with checksum d0fda26633fa762bff87ec759ebe689c
This command was run using /usr/local/hadoop-2.7.2/share/hadoop/common/hadoop-common-2.7.2.jar

```  



编辑core-site.xml  

```  
$ sudo vim /usr/local/hadoop-2.7.2/etc/hadoop/core-site.xml

<configuration>
  <property>
    <name>fs.defaultFS</name>
    <value>hdfs://hadoop1:9000</value>
  </property>

  <property>
    <name>Hadoop.tmp.dir</name>
    <value>/var/hadoop/data/temp</value>
  </property>
</configuration>



```  

编辑hdfs-site.xml  
```  
$ sudo vim /usr/local/hadoop-2.7.2/etc/hadoop/hdfs-site.xml

<configuration>
  <property>
    <name>dfs.namenode.name.dir</name>
    <value>/var/hadoop/data/namenode</value>
  </property>

  <property>
    <name>dfs.data.dir</name>
    <value>/var/hadoop/data/datanode</value>
  </property>

  <property>
    <name>dfs.replication</name>
    <value>1</value>
  </property>


</configuration>



```  

编辑 mapred-site.xml  
```  
$ sudo cp /usr/local/hadoop-2.7.2/etc/hadoop/mapred-site.xml.template /usr/local/hadoop-2.7.2/etc/hadoop/mapred-site.xml

$ sudo vim /usr/local/hadoop-2.7.2/etc/hadoop/mapred-site.xml
<configuration>
  <property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
  </property>


</configuration>


```  

编辑 yarn-site.xml  
```  
$ sudo vim /usr/local/hadoop-2.7.2/etc/hadoop/yarn-site.xml
<configuration>

<!-- Site specific YARN configuration properties -->
  <property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
  </property>

</configuration>


```  





ssh免登录  

```  
$ ssh-keygen -b 2048 -t "rsa"

$ cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
$ chmod 0600 ~/.ssh/authorized_keys

$ ssh hadoop1

```  

格式化namenode  

```  
$ sudo /usr/local/hadoop-2.7.2/bin/hdfs namenode -format
16/04/05 16:09:58 INFO namenode.NameNode: STARTUP_MSG:
/************************************************************
STARTUP_MSG: Starting NameNode
STARTUP_MSG:   host = hadoop1/192.168.1.100
STARTUP_MSG:   args = [-format]
STARTUP_MSG:   version = 2.7.2
STARTUP_MSG:   classpath =
STARTUP_MSG:   build = https://git-wip-us.apache.org/repos/asf/hadoop.git -r b165c4fe8a74265c792ce23f546c64604acf0e41; compiled by 'jenkins' on 2016-01-26T00:08Z
STARTUP_MSG:   java = 1.8.0_77
/************************************************************
SHUTDOWN_MSG: Shutting down NameNode at hadoop1/192.168.1.100
************************************************************/


$ sudo /usr/local/hadoop-2.7.2/sbin/start-all.sh
This script is Deprecated. Instead use start-dfs.sh and start-yarn.sh
Starting namenodes on [hadoop1]
root@hadoop1's password:
hadoop1: starting namenode, logging to /usr/local/hadoop-2.7.2/logs/hadoop-root-namenode-hadoop1.out
The authenticity of host 'localhost (::1)' can't be established.
ECDSA key fingerprint is bc:55:59:a9:ec:a4:b0:1a:97:10:34:32:e4:41:b1:f8.
Are you sure you want to continue connecting (yes/no)? yes
localhost: Warning: Permanently added 'localhost' (ECDSA) to the list of known hosts.
root@localhost's password:
localhost: starting datanode, logging to /usr/local/hadoop-2.7.2/logs/hadoop-root-datanode-hadoop1.out
Starting secondary namenodes [0.0.0.0]
root@0.0.0.0's password:
0.0.0.0: secondarynamenode running as process 20073. Stop it first.
starting yarn daemons
starting resourcemanager, logging to /usr/local/hadoop-2.7.2/logs/yarn-root-resourcemanager-hadoop1.out
root@localhost's password:
localhost: starting nodemanager, logging to /usr/local/hadoop-2.7.2/logs/yarn-root-nodemanager-hadoop1.out


$ sudo jps
20452 DataNode
21111 Jps
20073 SecondaryNameNode
20329 NameNode
20714 ResourceManager
20991 NodeManager


$ sudo ls /usr/local/hadoop-2.7.2/sbin/ | grep start
start-all.cmd
start-all.sh
start-balancer.sh
start-dfs.cmd
start-dfs.sh
start-secure-dns.sh
start-yarn.cmd
start-yarn.sh

$ sudo ls /usr/local/hadoop-2.7.2/sbin/ | grep stop
stop-all.cmd
stop-all.sh
stop-balancer.sh
stop-dfs.cmd
stop-dfs.sh
stop-secure-dns.sh
stop-yarn.cmd
stop-yarn.sh


$ sudo /usr/local/hadoop-2.7.2/sbin/stop-all.sh
This script is Deprecated. Instead use stop-dfs.sh and stop-yarn.sh
Stopping namenodes on [hadoop1]
root@hadoop1's password:
hadoop1: stopping namenode
root@localhost's password:
localhost: stopping datanode
Stopping secondary namenodes [0.0.0.0]
root@0.0.0.0's password:
0.0.0.0: stopping secondarynamenode
stopping yarn daemons
stopping resourcemanager
root@localhost's password:
localhost: stopping nodemanager
no proxyserver to stop


$ sudo /usr/local/hadoop-2.7.2/sbin/start-dfs.sh
Starting namenodes on [hadoop1]
root@hadoop1's password:
hadoop1: starting namenode, logging to /usr/local/hadoop-2.7.2/logs/hadoop-root-namenode-hadoop1.out
root@localhost's password:
localhost: starting datanode, logging to /usr/local/hadoop-2.7.2/logs/hadoop-root-datanode-hadoop1.out
Starting secondary namenodes [0.0.0.0]
root@0.0.0.0's password:
0.0.0.0: starting secondarynamenode, logging to /usr/local/hadoop-2.7.2/logs/hadoop-root-secondarynamenode-hadoop1.out

$ sudo /usr/local/hadoop-2.7.2/sbin/start-yarn.sh
starting yarn daemons
starting resourcemanager, logging to /usr/local/hadoop-2.7.2/logs/yarn-root-resourcemanager-hadoop1.out
root@localhost's password:
localhost: starting nodemanager, logging to /usr/local/hadoop-2.7.2/logs/yarn-root-nodemanager-hadoop1.out




```  


**NameNode**  http://192.168.1.100:50070/  
**ResourceManager**  http://192.168.1.100:8088  
**MapReduce JobHistory Server**   http://192.168.1.100:19888 (未启动)  


向hdfs中放入文件  
```  
$ sudo /usr/local/hadoop-2.7.2/bin/hdfs dfs -mkdir /test2016

$ sudo vim /var/hadoop/data/test.txt
This is a test file!

$ sudo /usr/local/hadoop-2.7.2/bin/hdfs dfs -put /var/hadoop/data/test.txt /test2016/

$ sudo /usr/local/hadoop-2.7.2/bin/hdfs dfs -cat /test2016/test.txt
This is a test file!

```  




