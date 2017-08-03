
# centos7_install_kafka.md  


# 文档  

http://kafka.apache.org/  
http://kafka.apache.org/quickstart   
https://cwiki.apache.org/confluence/display/KAFKA/Clients  


# 准备安装包  

```  
$ scp kafka_2.12-0.11.0.0.tgz root@192.168.1.106:/software
$ scp zookeeper-3.4.9.tar.gz root@192.168.1.106:/software
$ scp jdk-8u144-linux-x64.rpm root@192.168.1.106:/software

```  

# 安装  
```  
-- java
$ sudo rpm -ivh /software/jdk-8u144-linux-x64.rpm
$ java -version
java version "1.8.0_144"
Java(TM) SE Runtime Environment (build 1.8.0_144-b01)
Java HotSpot(TM) 64-Bit Server VM (build 25.144-b01, mixed mode)

-- zookeeper  
$ sudo tar -zxvf zookeeper-3.4.9.tar.gz
$ sudo cp -R /software/zookeeper-3.4.9 /usr/local/
$ sudo cp /usr/local/zookeeper-3.4.9/conf/zoo_sample.cfg /usr/local/zookeeper-3.4.9/conf/zoo.cfg
$ sudo /usr/local/zookeeper-3.4.9/bin/zkServer.sh start
ZooKeeper JMX enabled by default
Using config: /usr/local/zookeeper-3.4.9/bin/../conf/zoo.cfg

$ /usr/local/zookeeper-3.4.9/bin/zkCli.sh -server 127.0.0.1:2181


-- kafka  
$ sudo tar -zxf kafka_2.12-0.11.0.0.tgz
$ sudo cp -R /software/kafka_2.12-0.11.0.0 /usr/local/
$ /usr/local/kafka_2.12-0.11.0.0/bin/kafka-server-start.sh /usr/local/kafka_2.12-0.11.0.0/config/server.properties


```  

# 创建Topic  

```  
-- create topic  
$ /usr/local/kafka_2.12-0.11.0.0/bin/kafka-topics.sh --create --zookeeper 127.0.0.1:2181 --replication-factor 1 --partitions 1 --topic topicTest
Created topic "topicTest".

-- list topic  
$ /usr/local/kafka_2.12-0.11.0.0/bin/kafka-topics.sh --list --zookeeper 127.0.0.1:2181
topicTest

-- producer  
$ /usr/local/kafka_2.12-0.11.0.0/bin/kafka-console-producer.sh --broker-list 127.0.0.1:9092 --topic topicTest
>This is a message. {id:10001}
>(id:10002, message: new message}
>{id:10003, message: This is a good message!}
>

-- consumer
$ /usr/local/kafka_2.12-0.11.0.0/bin/kafka-console-consumer.sh --bootstrap-server 127.0.0.1:9092 --topic topicTest --from-beginning
This is a message. {id:10001}
(id:10002, message: new message}
{id:10003, message: This is a good message!}


```  

# 创建多个broker(multi-broker cluster)  
```  
$ sudo cp /usr/local/kafka_2.12-0.11.0.0/config/server.properties /usr/local/kafka_2.12-0.11.0.0/config/server_101.properties

$ sudo cp /usr/local/kafka_2.12-0.11.0.0/config/server.properties /usr/local/kafka_2.12-0.11.0.0/config/server_102.properties

$ sudo vim server_101.properties
broker.id=101
listeners=PLAINTEXT://:10101
# log.dirs=/tmp/kafka-logs
log.dirs=/tmp/kafka-logs-101


$ sudo vim server_102.properties
broker.id=102
listeners=PLAINTEXT://:10102
# log.dirs=/tmp/kafka-logs
log.dirs=/tmp/kafka-logs-102



$ /usr/local/kafka_2.12-0.11.0.0/bin/kafka-server-start.sh /usr/local/kafka_2.12-0.11.0.0/config/server_101.properties &

$ /usr/local/kafka_2.12-0.11.0.0/bin/kafka-server-start.sh /usr/local/kafka_2.12-0.11.0.0/config/server_102.properties &

-- 创建topic
$ /usr/local/kafka_2.12-0.11.0.0/bin/kafka-topics.sh --create --zookeeper 127.0.0.1:2181 --replication-factor 3 --partitions 1 --topic topicReplication2017
Created topic "topicReplication2017".

-- 查看topic  
$ /usr/local/kafka_2.12-0.11.0.0/bin/kafka-topics.sh --describe --zookeeper 127.0.0.1:2181 --topic topicReplication2017
Topic:topicReplication2017      PartitionCount:1        ReplicationFactor:3     Configs:
        Topic: topicReplication2017     Partition: 0    Leader: 102     Replicas: 102,0,101     Isr: 102,0,101

$ /usr/local/kafka_2.12-0.11.0.0/bin/kafka-topics.sh --describe --zookeeper 127.0.0.1:2181 --topic topicTest
Topic:topicTest PartitionCount:1        ReplicationFactor:1     Configs:
        Topic: topicTest        Partition: 0    Leader: 0       Replicas: 0     Isr: 0


$ /usr/local/kafka_2.12-0.11.0.0/bin/kafka-console-producer.sh           -broker-list 127.0.0.1:9092 --topic topicReplication2017
>{"id": 20001, "message": "This a test message1"}
>{"id": 20002, "message": "Good"}
>

$ /usr/local/kafka_2.12-0.11.0.0/bin/kafka-console-consumer.sh --bootstrap-server 127.0.0.1:9092 --topic topicReplication2017 --from-beginning
{"id": 20001, "message": "This a test message1"}
{"id": 20002, "message": "Good"}

$ ps aux | grep server_101
		

```  



