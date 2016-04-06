centos7_install_zookeeper

# CentOS 7安装 zookeeper #

[zookeeper下载地址](http://mirror.bit.edu.cn/apache/zookeeper/)  



## 需要先安装JDK ##
```  
$ java -version
java version "1.8.0_77"
Java(TM) SE Runtime Environment (build 1.8.0_77-b03)
Java HotSpot(TM) 64-Bit Server VM (build 25.77-b03, mixed mode)

```  


## 安装zookeeper ##
将zookeeper下载到/soft目录
```  
$ cd /soft/
$ sudo tar -zxvf zookeeper-3.4.8.tar.gz

$ sudo cp -R /soft/zookeeper-3.4.8 /usr/local/
$ ls /usr/local/zookeeper-3.4.8/

```  

## 修改zookeeper配置文件 ##
```  
$ sudo cp /usr/local/zookeeper-3.4.8/conf/zoo_sample.cfg /usr/local/zookeeper-3.4.8/conf/zoo.cfg
$ ls /usr/local/zookeeper-3.4.8/conf/
configuration.xsl  log4j.properties  zoo.cfg  zoo_sample.cfg

$ sudo mkdir /var/zookeeper

$ sudo vim /usr/local/zookeeper-3.4.8/conf/zoo.cfg
# dataDir=/tmp/zookeeper
dataDir=/var/zookeeper


$ sudo /usr/local/zookeeper-3.4.8/bin/zkServer.sh start
ZooKeeper JMX enabled by default
Using config: /usr/local/zookeeper-3.4.8/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED


$ sudo jps
11164 QuorumPeerMain

$ sudo ps -ef | grep zookeeper


```  

## 使用zkCli连接 ##

```  
$ /usr/local/zookeeper-3.4.8/bin/zkCli.sh -server 192.168.1.100:2181


[zk: 192.168.1.100:2181(CONNECTED) 0] help
ZooKeeper -server host:port cmd args
        stat path [watch]
        set path data [version]
        ls path [watch]
        delquota [-n|-b] path
        ls2 path [watch]
        setAcl path acl
        setquota -n|-b val path
        history
        redo cmdno
        printwatches on|off
        delete path [version]
        sync path
        listquota path
        rmr path
        get path [watch]
        create [-s] [-e] path data acl
        addauth scheme auth
        quit
        getAcl path
        close
        connect host:port


		
[zk: 192.168.1.100:2181(CONNECTED) 1] ls /
[zookeeper]

[zk: 192.168.1.100:2181(CONNECTED) 2] create /test testNode
Created /test

[zk: 192.168.1.100:2181(CONNECTED) 3] create /test/data1 data1
Created /test/data1

[zk: 192.168.1.100:2181(CONNECTED) 4] get /test
testNode
cZxid = 0x4
ctime = Wed Apr 06 16:56:36 CST 2016
mZxid = 0x4
mtime = Wed Apr 06 16:56:36 CST 2016
pZxid = 0x5
cversion = 1
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 8
numChildren = 1


[zk: 192.168.1.100:2181(CONNECTED) 5] set /test newTest

[zk: 192.168.1.100:2181(CONNECTED) 6] get /test
newTest

[zk: 192.168.1.100:2181(CONNECTED) 7] create /test/data1/new1 new1
Created /test/data1/new1

[zk: 192.168.1.100:2181(CONNECTED) 11] create /test/data1/new2 new2
Created /test/data1/new2

[zk: 192.168.1.100:2181(CONNECTED) 12] delete /test/data1
Node not empty: /test/data1

[zk: 192.168.1.100:2181(CONNECTED) 13] delete /test/data1/new2

[zk: 192.168.1.100:2181(CONNECTED) 14] rmr /test/data1

		
```  



