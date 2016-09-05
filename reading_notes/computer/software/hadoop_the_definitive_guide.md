
# hadoop_the_definitive_guide.md  Hadoop权威指南  



# 第14章 ZooKeeper  

ZooKeeper是Hadoop的分布式协调服务.  

写分布式应用的难点在于出现"部分失败".  

## ZooKeeper特点  

- ZooKeeper是简单的. ZooKeeper的核心是一个精简的文件系统, 它提供一些简单的操作和一些额外的抽象操作. 例如排序和通知.  
- ZooKeeper是富有表现力的. ZooKeeper的基本操作是一组丰富的"构件"(building block), 可以实现多种协调数据结构和协议.  
- ZooKeeper具有高可用性. ZooKeeper运行在一组机器之上, 并且在设计上具有高可用性, 因此应用程序可以完全依赖与它.  
- ZooKeeper采用松耦合交互方式. 在ZooKeeper支持的交互过程中, 参与者不需要彼此了解.  
- ZooKeeper是一个资源库. ZooKeeper提供了一个通用协调模式实现方法的开源共享库, 使程序员免于编写这类通用的协议(这通常是很难写正确的).  

## 安装和运行ZooKeeper  

```  

% tar -xzf zookeeper-x.y.z.tar.gz

% export ZOOKEEPER_INSTALL=/home/zookeeper-x.y.zookeeper-x
% export PATH=$PATH:$ZOOKEEPER_INSTALL/bin

zoo.cfg
tickTime=2000
dataDir=/Users/highill/zookeeper-x
clientPort=2181


% zkServer.sh start

% echo ruok | nc localhost 2181




```  


理解ZooKeeper的一种方式就是将其看作一个具有高可用性特征的文件系统. 这个文件系统中没有文件和目录, 而是统一使用"节点"(node)的概念, 称为znode. znode即可以作为保存数据的容器(如同文件), 也可以作为保存其他znode的容器(目录).  

有两种类型的znode: 短暂的(ephemeral)和持久的(persistent). 创建znode客户端断开连接时, 无论客户端是明确断开还是因为任何原因而终止, 短暂znode都会被ZooKeeper服务删除. 与之相反, 当客户端断开时, 持久znode不会被删除.  






























