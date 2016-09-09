
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

ZooKeeper类提供了一个delete()方法, 该方法有两个参数: 节点路径和版本号. 如果所提供的版本号与znode的版本号一致, ZooKeeper会删除这个znode. 这是一种乐观的加锁机制. 使客户端能够检测出对znode的修改冲突. 通过将版本号设置为-1. 可以绕过这个版本检测机制, 不管znode的版本号是什么而直接将其删除.  
ZooKeeper不支持递归的删除操作, 因此在删除父节点之前必须先删除子节点.  

ZooKeeper维护着一个树形层次结构, 树中的节点被称为znode. znode可以用于存储数据, 并且有一个与之关联的ACL.  ZooKeeper被设计用来实现协调服务(这类服务通常使用小数据文件), 而不是用于大容量数据存储, 因此一个znode能够存储的数据被限制在1MB以内.  

ZooKeeper的数据访问具有原子性. 客户端在读取一个znode的数据时, 要么读到所有的数据, 要么读操作失败, 不会只读到部分数据.  

ZooKeeper中的路径必须是绝对路径, 也就是说每条路径必须从一个斜杠字符开始. 此外, 所有的路径表示必须是规范的, 即每条路径只有唯一的一种表示方式, 不支持路径解析. 如Unix中, 一个具有路径` /a/b ` 的文件也可以通过路径 ` /a/./b ` 来表示, 原因在于 "." 在Unix表示当前目录. 在ZooKeeper中, "."不具有这种特殊含义.  
ZooKeeper使用 /zookeeper子树来保存管理信息, 例如关于配额的信息.  

znode有两种类型: 短暂的和持久的.  短暂的znode不可以有子节点, 即使是短暂的子节点.  虽然每个短暂的znode会被绑定到一个客户端, 但它们对所有客户端还是可见的(当然, 还要符合ACL的定义).  


顺序(sequential) znode是指名称中包含ZooKeeper指定顺序号的znode. 如果在创建znode时设置了顺序标识, 那么该znode名称之后便会附加一个值, 这个值是由一个单调递增的计数器(由父节点维护)所添加的.  

在一个分布式系统中, 顺序号可以被用于为所有的事件进行全局排序, 这样客户端就可以通过顺序号来推断事件的顺序.  


znode以某种方式变化时, "观察"(watch)机制可以让客户端得到通知. 观察只能被触发一次, 对于连接事件的回调除外, 这种观察不需要重新注册. 为了能够多次收到通知, 客户端需要重新注册所需的观察.  


ZooKeeper中有9中基本操作  

```  
create  
delete  
exists  
getACL,  setACL  
getChildren  
getData,  setData  
sync  



```  

虽然ZooKeeper可以被看作是一个文件系统, 但出于简单性的需要, 有一些文件系统的基本操作被它摒弃了. 由于ZooKeeper中的文件较小并且总是被整体读写, 因此没有必要提供打开, 关闭或查找操作.  

ZooKeeper中有一个被称为multi的操作. 用于将多个操作集合合成一个操作单元, 并确保这些操作同时被执行成功, 或者同时失败, 不会发生其中部分基本操作被成功执行而其它基本操作失败的情况. 集合更新可以被用于在ZooKeeper中构建需要保持全局一致性的数据结构, 例如构建一个无向图.  


对于ZooKeeper客户端来说, 主要有两种语言绑定(binding)可以使用: Java和C. 当然也可以使用Perl, Python和 REST的contrib绑定. 对于每一种绑定语言来说, 在执行操作时都可以选择同步执行或异步执行.  

因为所有异步操作的结果都是通过回调来传送的, 因此在Java API中异步方法的返回类型都是void. 调用者传递一个回调的实现, 当ZooKeeper响应时, 该方法被调用. 在这种情况下, 回调采用StatCallback接口.  







 








 































