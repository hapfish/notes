centos7_install_hbase

# CentOS 7 安装 HBase #


[HBase中文文档](http://abloz.com/hbase/book.html)  

[HBase伪分布模式](http://abloz.com/hbase/book.html#standalone_dist)  

[HBase下载](http://mirror.bit.edu.cn/apache/hbase/)  


## HBase需要先安装JDK ##

```  
$ java -version
java version "1.8.0_77"
Java(TM) SE Runtime Environment (build 1.8.0_77-b03)
Java HotSpot(TM) 64-Bit Server VM (build 25.77-b03, mixed mode)
```  

也需要准备好HDFS和ZooKeeper.  

## 安装HBase ##

下载HBase到/soft 目录.  

```  
$ cd /soft/

$ sudo tar -zxvf hbase-1.1.3-bin.tar.gz
$ ls /soft/hbase-1.1.3

$ sudo cp -R /soft/hbase-1.1.3/ /usr/local/
$ ls /usr/local/hbase-1.1.3/


```  

## 编辑hbase-site.xml ##

HDFS使用伪分布模式, HBase也使用伪分布模式. 使用已有的zookeeper.  


```  
$ sudo vim /usr/local/hbase-1.1.3/conf/hbase-site.xml
<configuration>
  <property>
    <name>hbase.rootdir</name>
    <value>hdfs://hadoop1:9000/hbase/data</value>
    <description>The directory shared by RegionServers.</description>
  </property>

  <property>
    <name>dfs.replication</name>
    <value>1</value>
    <description>The replication count for HLog and HFile, Should not be greater than HDFS datanode count.</description>
  </property>


  <property>
    <name>hbase.zookeeper.property.clientPort</name>
    <value>2181</value>
  </property>
  <property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
  </property>
  <property>
    <name>hbase.zookeeper.quorum</name>
    <value>hadoop1</value>
  </property>
</configuration>


$ sudo vim /usr/local/hbase-1.1.3/conf/hbase-env.sh
# export HBASE_MANAGES_ZK=true
export HBASE_MANAGES_ZK=false


$ sudo vim /usr/local/hbase-1.1.3/conf/regionservers
# localhost
hadoop1

```  


## 启动HBase ##



```  
$ sudo vim /usr/local/hbase-1.1.3/conf/hbase-env.sh
# export JAVA_HOME=/usr/java/jdk1.6.0/
export JAVA_HOME=/usr/java/jdk1.8.0_77


$ sudo /usr/local/hbase-1.1.3/bin/start-hbase.sh
starting master, logging to /usr/local/hbase-1.1.3/bin/../logs/hbase-root-master-hadoop1.out
Java HotSpot(TM) 64-Bit Server VM warning: ignoring option PermSize=128m; support was removed in 8.0
Java HotSpot(TM) 64-Bit Server VM warning: ignoring option MaxPermSize=128m; support was removed in 8.0


```  

启动错误, 主要是Java 8移除了参数.  
[Java 8 特性](http://www.importnew.com/11908.html#newFeatureOfJVM)  

`-XX:PermSize ` 被 ` -XX:MetaspaceSize ` 替代, ` -XX:MaxPermSize ` 被 ` -XX:MaxMetaspaceSize ` 替代.  

```  
$ sudo vim /usr/local/hbase-1.1.3/conf/hbase-env.sh
# export HBASE_MASTER_OPTS="$HBASE_MASTER_OPTS -XX:PermSize=128m -XX:MaxPermSize=128m"
export HBASE_MASTER_OPTS="$HBASE_MASTER_OPTS -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=128m"

# export HBASE_REGIONSERVER_OPTS="$HBASE_REGIONSERVER_OPTS -XX:PermSize=128m -XX:MaxPermSize=128m"
export HBASE_REGIONSERVER_OPTS="$HBASE_REGIONSERVER_OPTS -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=128m"


$ sudo /usr/local/hbase-1.1.3/bin/start-hbase.sh
starting master, logging to /usr/local/hbase-1.1.3/bin/../logs/hbase-root-master-hadoop1.out
root@hadoop1's password: root@localhost's password:
hadoop1: starting regionserver, logging to /usr/local/hbase-1.1.3/bin/../logs/hbase-root-regionserver-hadoop1.out
localhost: regionserver running as process 5522. Stop it first.
#: ssh: Could not resolve hostname #: Temporary failure in name resolution



$ sudo jps
5522 HRegionServer
5325 HMaster

$ sudo ps -ef | grep hbase

```  



## 在ZooKeeper中验证HBase ##
登录到HBase使用的ZoooKeeper, 出现 ` /hbase ` 节点.  

```  
$ sudo /usr/local/zookeeper-3.4.8/bin/zkCli.sh -server 192.168.1.100:2181

[zk: 192.168.1.100:2181(CONNECTED) 0] ls /
[zookeeper, test, hbase]

[zk: 192.168.1.100:2181(CONNECTED) 1] ls /hbase
[replication, meta-region-server, rs, splitWAL, backup-masters, table-lock, flush-table-proc, region-in-transition, online-snapshot, master, running, recovering-regions, draining, namespace, hbaseid, table]



```  


## 使用HBase Shell ##

```  
$ sudo /usr/local/hbase-1.1.3/bin/hbase shell
2016-04-07 08:28:04,956 WARN  [main] util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
HBase Shell; enter 'help<RETURN>' for list of supported commands.
Type "exit<RETURN>" to leave the HBase Shell
Version 1.1.3, r72bc50f5fafeb105b2139e42bbee3d61ca724989, Sat Jan 16 18:29:00 PST 2016

hbase(main):001:0> list
TABLE
0 row(s) in 0.3290 seconds

=> []

```  

debug模式启动HBase Shell.  
```  
$ sudo /usr/local/hbase-1.1.3/bin/hbase shell -d
Setting DEBUG log level..


```  

使用HBase Shell  
```  
hbase(main):001:0> create 'test_table', 'name', 'info'
0 row(s) in 1.7140 seconds

=> Hbase::Table - test_table


hbase(main):003:0> list
TABLE
test_table
1 row(s) in 0.0120 seconds

=> ["test_table"]


hbase(main):004:0> list 'test_table'
TABLE
test_table
1 row(s) in 0.0150 seconds

=> ["test_table"]


hbase(main):013:0> describe 'test_table'
Table test_table is ENABLED
test_table
COLUMN FAMILIES DESCRIPTION
{NAME => 'info', BLOOMFILTER => 'ROW', VERSIONS => '1', IN_MEMORY => 'false', KEEP_DELETED_CELLS => 'FALS
E', DATA_BLOCK_ENCODING => 'NONE', TTL => 'FOREVER', COMPRESSION => 'NONE', MIN_VERSIONS => '0', BLOCKCAC
HE => 'true', BLOCKSIZE => '65536', REPLICATION_SCOPE => '0'}
{NAME => 'name', BLOOMFILTER => 'ROW', VERSIONS => '1', IN_MEMORY => 'false', KEEP_DELETED_CELLS => 'FALS
E', DATA_BLOCK_ENCODING => 'NONE', TTL => 'FOREVER', COMPRESSION => 'NONE', MIN_VERSIONS => '0', BLOCKCAC
HE => 'true', BLOCKSIZE => '65536', REPLICATION_SCOPE => '0'}
2 row(s) in 0.0450 seconds


hbase(main):005:0>put 'test_table', 'key_2016_0001', 'name:n1', 'name 1'
0 row(s) in 0.2360 seconds

hbase(main):008:0> put 'test_table', 'key_2016_0001', 'info:info1', 'Hello Info 1 '
0 row(s) in 0.0290 seconds

hbase(main):012:0> put 'test_table', 'key_2016_0001', 'info:info1', 'Hello Info New 1 '
0 row(s) in 0.0140 seconds



hbase(main):014:0> put 'test_table', 'key_2016_0002', 'name:n2', 'name 2'
0 row(s) in 0.0140 seconds

hbase(main):015:0> put 'test_table', 'key_2016_0002', 'info:info2', 'Hello Info 2'
0 row(s) in 0.0130 seconds

hbase(main):016:0> put 'test_table', 'key_2016_0003', 'name:n3', 'name 3'
0 row(s) in 0.0230 seconds

hbase(main):017:0> put 'test_table', 'key_2016_0003', 'info:info3', 'Hello Info 3'
0 row(s) in 0.0150 seconds


hbase(main):018:0> scan 'test_table'
ROW                         COLUMN+CELL
 key_2016_0001              column=info:info1, timestamp=1459989439661, value=Hello Info New 1
 key_2016_0001              column=name:n1, timestamp=1459989350578, value=name 1
 key_2016_0002              column=info:info2, timestamp=1459989591766, value=Hello Info 2
 key_2016_0002              column=name:n2, timestamp=1459989566528, value=name 2
 key_2016_0003              column=info:info3, timestamp=1459989626682, value=Hello Info 3
 key_2016_0003              column=name:n3, timestamp=1459989615781, value=name 3
3 row(s) in 0.0430 seconds



hbase(main):019:0> scan 'test_table' , {COLUMNS => 'name'}
ROW                         COLUMN+CELL
 key_2016_0001              column=name:n1, timestamp=1459989350578, value=name 1
 key_2016_0002              column=name:n2, timestamp=1459989566528, value=name 2
 key_2016_0003              column=name:n3, timestamp=1459989615781, value=name 3
3 row(s) in 0.0280 seconds

hbase(main):022:0> scan 'test_table' , {COLUMNS => 'name:n2'}
ROW                         COLUMN+CELL
 key_2016_0002              column=name:n2, timestamp=1459989566528, value=name 2
1 row(s) in 0.0160 seconds


hbase(main):023:0> scan 'test_table' , {COLUMNS => ['name:n2', 'info']}
ROW                         COLUMN+CELL
 key_2016_0001              column=info:info1, timestamp=1459989439661, value=Hello Info New 1
 key_2016_0002              column=info:info2, timestamp=1459989591766, value=Hello Info 2
 key_2016_0002              column=name:n2, timestamp=1459989566528, value=name 2
 key_2016_0003              column=info:info3, timestamp=1459989626682, value=Hello Info 3
3 row(s) in 0.0700 seconds


hbase(main):027:0> get 'test_table', 'key_2016_0001', {COLUMN => 'name'}
COLUMN                      CELL
 name:n1                    timestamp=1459989350578, value=name 1
1 row(s) in 0.0350 seconds

hbase(main):028:0> get 'test_table', 'key_2016_0001', {COLUMN => ['name', 'info']}
COLUMN                      CELL
 info:info1                 timestamp=1459989439661, value=Hello Info New 1
 name:n1                    timestamp=1459989350578, value=name 1
2 row(s) in 0.0200 seconds


hbase(main):040:0> get 'test_table', 'key_2016_0001', 'name'
COLUMN                      CELL
 name:n1                    timestamp=1459989350578, value=name 1
1 row(s) in 0.0130 seconds

hbase(main):041:0> get 'test_table', 'key_2016_0001', 'name', 'info'
COLUMN                      CELL
 info:info1                 timestamp=1459989439661, value=Hello Info New 1
 name:n1                    timestamp=1459989350578, value=name 1
2 row(s) in 0.0120 seconds


hbase(main):042:0> count 'test_table'
3 row(s) in 0.0270 seconds

=> 3




hbase(main):048:0> disable 'test_table'
0 row(s) in 2.2650 seconds


hbase(main):052:0> alter 'test_table', NAME=>'address'
Updating all regions with the new schema...
1/1 regions updated.
Done.
0 row(s) in 1.9650 seconds


hbase(main):053:0> describe 'test_table'
Table test_table is DISABLED
test_table
COLUMN FAMILIES DESCRIPTION
{NAME => 'address', BLOOMFILTER => 'ROW', VERSIONS => '1', IN_MEMORY => 'false', KEEP_DELETED_CELLS => 'F
ALSE', DATA_BLOCK_ENCODING => 'NONE', TTL => 'FOREVER', COMPRESSION => 'NONE', MIN_VERSIONS => '0', BLOCK
CACHE => 'true', BLOCKSIZE => '65536', REPLICATION_SCOPE => '0'}
{NAME => 'info', BLOOMFILTER => 'ROW', VERSIONS => '1', IN_MEMORY => 'false', KEEP_DELETED_CELLS => 'FALS
E', DATA_BLOCK_ENCODING => 'NONE', TTL => 'FOREVER', COMPRESSION => 'NONE', MIN_VERSIONS => '0', BLOCKCAC
HE => 'true', BLOCKSIZE => '65536', REPLICATION_SCOPE => '0'}
{NAME => 'name', BLOOMFILTER => 'ROW', VERSIONS => '1', IN_MEMORY => 'false', KEEP_DELETED_CELLS => 'FALS
E', DATA_BLOCK_ENCODING => 'NONE', TTL => 'FOREVER', COMPRESSION => 'NONE', MIN_VERSIONS => '0', BLOCKCAC
HE => 'true', BLOCKSIZE => '65536', REPLICATION_SCOPE => '0'}
3 row(s) in 0.0130 seconds


hbase(main):054:0> alter 'test_table', NAME=>'address2', VERSIONS => 1
Updating all regions with the new schema...
1/1 regions updated.
Done.
0 row(s) in 1.9610 seconds

hbase(main):055:0> alter 'test_table', NAME => 'address', METHOD => 'delete'
Updating all regions with the new schema...
1/1 regions updated.
Done.
0 row(s) in 2.2220 seconds

hbase(main):059:0> enable 'test_table'
0 row(s) in 2.4130 seconds


hbase(main):066:0> scan 'test_table', {COLUMNS => ['info', 'name', 'address2']}
ROW                         COLUMN+CELL
 key_2016_0001              column=info:info1, timestamp=1459989439661, value=Hello Info New 1
 key_2016_0001              column=name:n1, timestamp=1459989350578, value=name 1
 key_2016_0002              column=info:info2, timestamp=1459989591766, value=Hello Info 2
 key_2016_0002              column=name:n2, timestamp=1459989566528, value=name 2
 key_2016_0003              column=info:info3, timestamp=1459989626682, value=Hello Info 3
 key_2016_0003              column=name:n3, timestamp=1459989615781, value=name 3
3 row(s) in 0.0240 seconds


hbase(main):083:0> delete 'test_table', 'key_2016_0003', 'info:info3'
0 row(s) in 0.0040 seconds


hbase(main):069:0> delete 'test_table', 'key_2016_0003', 'name:n3'
0 row(s) in 0.0050 seconds


```  







