
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

实际上, 有两个C语言的共享库. 单线程库zookeeper_st只支持异步API, 并且主要在没有pthread库或pthread库不稳定的平台上使用. 大部分开发人员都是用多线程库zookeeper_mt, 它既支持同步API, 也支持异步API.  

如果习惯于事件驱动的编程模型, 则异步API更合适些.  


在exists, getChildren和getData这些操作上可以设置观察, 这些观察可以被写操作 create, delete, setData触发. ACL相关的操作不会参与触发任何观察.  

- 当所观察的znode被创建, 删除或其数据被更新时, 设置在exists操作上的观察将被触发.  
- 当所观察的znode被删除或其数据更新时, 设置在getData操作上的观察将被触发. 创建znode不会触发getData操作上的观察, 因为getData操作成功执行的前提是znode必须存在.  
- 当所观察的znode的一个子节点被创建或删除时, 或所观察的znode自己被删除时, 设置在getChildren操作上的观察将会被触发. 可以通过观察事件的类型来判断被删除的是znode还是其子节点: NodeDelete类型代表znode被删除; NodeChildrenChanged类型代表一个子节点被删除.  


观察及其触发操作所对应的时间类型  

<table>
    <tr>
	    <th rowspan="2">设置观察的操作</th>    <th colspan="5"> 观察触发器</th>
	</tr>
	<tr>
	    <th>创建znode</th>    <th>创建子节点</th>    <th>删除znode</th>    <th>删除子节点</th>    <th>setData</th> 
	</tr>
	
	
	<tr>
	    <td>exists</td>    <td>NodeCreated</td>    <td></td>    <td>NodeDeleted</td>    <td></td>    <td>NodeDataChanged</td>
	</tr>
	<tr>
	    <td>getData</td>    <td></td>    <td></td>    <td>NodeDeleted</td>    <td></td>    <td>NodeDataChanged</td>
	</tr>
	<tr>
	    <td>getChildren</td>    <td></td>    <td>NodeChildrenChanged</td>    <td>NodeDeleted</td>    <td>NodeChildrenChanged</td>    <td></td>
	</tr>
	
</table>


一个观察事件中包含涉及该事件的znode的路径. 因此对于NodeCreated和NodeDeleted事件来说, 可通过路径判断哪一个节点被创建或删除.  为了能够在NodeChildrenChanged事件发生后判断是哪些子节点被修改, 需要重新调用getChildren来获取新的子节点列表. 与之类似, 为了能够在NodeDataChanged事件之后获取新的子节点列表, 需要调用getData.  


每个znode被创建时都会带一个ACL列表. ACL依赖于ZooKeeper的客户端验证机制. ZooKeeper提供了以下几种身份验证方式:  
- Digest  通过用户名和密码来识别客户端  
- sasl  通过Kerberos来识别客户端  
- Ip  通过客户端IP地址来识别客户端  


exists操作不受ACL权限的限制, 因此客户端可以调用exists来检索一个znode的状态或查询一个znode是否存在.  


ACL权限  

<table>
    <tr>
	    <th>ACL权限</th>    <th>允许的操作</th>    
	</tr>
	
	<tr>
	    <td>CREATE</td>    <td>create(子节点)</td>    
	</tr>
	<tr>
	    <td rowspan="2">READ</td>    <td>getChildren</td>    
	</tr>
	<tr>
	    <td>getData</td>    
	</tr>
	<tr>
	    <td>WRITE</td>    <td>setData</td>    
	</tr>
	<tr>
	    <td>DELETE</td>    <td>delete(子节点)</td>    
	</tr>
	<tr>
	    <td>ADMIN</td>    <td>setACL</td>    
	</tr>
</table>  



ZooDefs.Ids中有一些预定义的ACL, OPEN_ACL_UNSAFE是其中之一, 它将所有的权限(不包括ADMIN权限)授予每个人.  

此外, ZooKeeper还支持插入式身份验证机制, 如果需要的话, 它可以集成第三方的身份验证系统.  


## ZooKeeper实现  

ZooKeeper服务有两种不同的运行模式．　一种是"独立模式"(standalone mode), 即只有一个ZooKeeper服务器．　这种模式比较简单，　比较适合测试环境(甚至可以在单元测试中采用), 但是这种模式不能保证高可用性和可恢复性．　在生产环境中的ZooKeeper通常以"复制模式"(replicated mode)运行于一个计算机集群上，　这个计算机集群被称为一个"集合体"(ensemble).  只有集合体中半数以上的机器处于可用状态，　它就能提供服务. 例如5个节点的集合体中, 任意2台服务出现故障, 都可以保证服务继续, 因为剩下的3台超过了半数. 6个节点的集合体也只能容忍2台机器故障, 因为如果３台机器出现故障, 剩下的3台机器没有超过集合体的半数.  一个集合体通常包含奇数台机器.  

如果少于半数的机器出现故障, 则最少有一台机器会保存最新的状态, 其余的副本最终也会更新到这个状态.  

ZooKeeper使用了Zab协议, 该协议包括了两个可以无限重复的阶段.  

1. 阶段1 领导者选举  
集合体中的所有机器通过一个选择过程来选出一台被称为"领导者"(leader)的机器. 其它的机器被称为"跟随着"(follower). 一旦半数以上(或指定数量)的跟随着已经将其状态与领导者同步, 则表明这个阶段已经完成.  

2. 阶段2 原子广播  
所有的写请求都会被转发给领导者, 再由领导者将更新广播给跟随者. 当半数以上的跟随着已经将修改持久化之后, 领导者才会提交这个更新. 然后客户端才会收到一个更新成功的响应.  

ZooKeeper是否使用Paxos? 否. ZooKeeper的Zab协议不同于众所周知的Paxos算法(Leslie Lamport, "Paxos Made Simple" ACM SIGACT News[Distributed Computing Column]). Google的Chubby锁服务是基于Paxos的.  


如果领导者出现故障, 其余的机器会选出另外一个领导者, 并和新的领导者一起继续提供服务. 随后, 如果之前的领导者恢复正常, 会成为一个跟随着. 领导者选举的过程是非常快的, 根据一个已公布的结果来看, 只需要大概200毫秒, 因此在领导者选举的过程中不会出现系统性能明显降低.  

在更新内存中的znode之前, 集合体中的所有机器都会先将更新写入磁盘. 任何一台机器都可以为读请求提供服务. 并且由于读请求只涉及内存检索, 因此非常快.  

一个跟随着可能滞后领导者几个更新. 这也表明在一个修改被提交前, 只需要集合体中半数以上机器已经将该修改持久化即可.  

每一个对znode树的更新都被赋予一个全局唯一的ID, 称为zxid(代表ZooKeeper Transaction ID). ZooKeeper要求所有的更新进行编号并排序, 它决定了分布式系统的执行顺序.  


在ZooKeeper的设计中, 以下几点保证了数据的一致性.  
- 1. 顺序一致性  来自任意特定客户端的更新都会按照其发送顺序被提交.  
- 2. 原子性  每个更新要么成功, 要么失败. 这意味着如果一个更新失败, 则不会有客户端看到这个更新的结果.  
- 3. 单一系统映像  一个客户端无论连接到哪一台服务器, 它看到的都是同样的系统视图. 这意味着, 如果一个客户端在同一个会话中连接到一台新的服务器, 它所看到的系统状态不会比之前服务器上所看到的更老.  
- 4. 持久性  一个更新一旦成功, 其结果会持久存在并且不会被撤销. 这表明更新不会受到服务器故障的影响.  
- 5. 及时性  任何客户端所看到的滞后系统视图都是有限的, 不会超过几十秒. 如果客户端之间通过ZooKeeper之外的机制进行通信, 则客户端可能发现他们所看到的ZooKeeper状态是不一致的.  


sync操作只能以异步的方式被调用. 你不需要等待sync调用的返回, ZooKeeper会保证任何后续的操作都在服务器的sync操作完成后才执行, 哪怕这些操作是在sync操作完成之前发出的.  

每个ZooKeeper客户端的配置中都包括集合体中服务器的列表. 在启动时, 客户端会尝试连接到列表中的一台服务器. 如果连接失败, 它会尝试连接到另一台服务器, 以此类推, 直到成功与一台服务器建立连接或因为所有ZooKeeper服务器都不可用而失败.  

一旦客户端与一台ZooKeeper服务器建立连接, 这台服务器就会为该客户端创建一个新的会话. 会话通常都会长期存在, 而会话过期则是一种比较罕见的事件, 但对于应用来说, 如何处理会话过期仍是非常重要的.  

只要一个会话空闲超过一定时间, 都可以通过客户端发送ping请求(也称为心跳)来保持会话不过期.(ping请求是由ZooKeeper的客户端自动发送. 因此你在代码里不需要考虑如何维护会话).  

ZooKeeper客户端可以自动地进行故障切换, 切换到另一台ZooKeeper服务器, 并且关键的是, 在另一台服务器接替故障服务器之后, 所有的会话(和相关的短暂znode)仍然是有效的.  

在故障切换过程中, 应用程序将收到断开连接和连接至服务的通知. 当客户端断开连接时, 观察通知将无法发送; 但是客户端成功恢复连接后, 这些延迟的通知还会被发送. 当然, 在客户端重新连接至另一台服务器的过程中, 如果应用程序试图执行一个操作, 这个操作将会失败. 这充分说明在真实的ZooKeeper应用中处理连接丢失异常的重要性.  


## ZooKeeper的时间参数  

"滴答"(tick time) 参数定义了ZooKeeper中的基本时间周期, 并被集合体中的服务器用来定义相互交互的时间表. 其他设置都是根据滴答参数来定义的, 或至少受它限制. 例如, 会话时间超时(session timeout)参数的值不可以小于2个滴答并且不可以大于20个滴答. 如果你试图将会话超时参数设置在这个范围之外, 它将会被自动修改到这个范围之内.  

通常将滴答参数设置为2秒(2000毫秒), 对应于允许的会话超时范围是4到40秒.  

较短的会话超时设置会较快地检测到机器故障. 要避免将会话超市时间设得太低, 因为繁忙的网络会导致数据包延迟, 从而可能会无意导致会话过期. 在这种情况下, 机器可能出现"振动"(flap)现象: 在很短的时间内反复出现离开后又重新加入组的情况.  

对于那些创建较复杂暂时状态的应用程序来说, 由于重建的代价较大, 因此比较适合设置较长的会话超时.  

将会话ID和密码保存在稳定的存储器中之后, 可以将一个应用程序正常关闭, 然后在重启应用之前凭借所保存的会话ID和密码来恢复会话环境.  

可以将这个特征看成是一种用来帮助避免会话过期的优化技术, 但不能因此忽略对会话过期异常的处理, 因为机器的意外故障也会导致会话过期, 或者, 即使应用程序是正常关闭, 也有可能因任何原因导致它没有在会话过期之前完成重启.  

一般规则是, ZooKeeper集合体中的服务器越多, 会话超时的设置应越大. 连接超时, 读超时和ping周期都被定义为集合体中服务器数量的函数, 因此集合体中服务器数量越多, 这些参数的值反而越小. 如果频繁遇到连接丢失的情况, 应考虑增大超时的设置.  

ZooKeeper对象在其生命周期中会经历几种不同的状态, 可以在任何时刻通过getState()方法来查询对象的状态.  

ZooKeeper的Watcher对象肩负双重责任: 一方面它可以被用于获得ZooKeeper状态变化的相关通知; 另一方面还以被用于获得znode变化的通知. 监视znode的变化可以使用一个专用的观察对象(将其传递给适当的读操作), 也可以通过读操作中的布尔标识来设定是否使用默认的观察.   

ZooKeeper实例可以断开然后重新连接到ZooKeeper服务, 此时它的状态就在CONNECTED和CONNECTING之间转换. 如果它断开连接, 观察会收到一个Disconnected事件. 注意, 这些状态转换都是由ZooKeeper实例自己发起的, 如果连接丢失, 它会自动尝试重新连接.  

close()方法被调用或出现会话超时(观察事件的KeeperState值为Expired)时, ZooKeeper实例会转换到第三个状态CLOSED. 一旦处于CLOSED状态, ZooKeeper对象不再被认为是活跃的(可以对States使用isAlive()方法来测试). 并且不能再用. 为了重新连接到ZooKeeper服务, 客户端必须创建一个新的ZooKeeper实例.  


  


 
 



 


  


















 








 































