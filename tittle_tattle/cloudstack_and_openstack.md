
# cloudstack and openstack  

 
http://www.ibm.com/developerworks/cn/cloud/library/1303_chenyz_cloudstack/
 
 
http://docs.cloudstack.apache.org/projects/cloudstack-installation/en/4.4/qig.html  

```  
vim /etc/yum.repos.d/cloudstack.repo
[cloudstack]
name=cloudstack
baseurl=http://cloudstack.apt-get.eu/rhel/4.4/
enabled=1
gpgcheck=0
 
 
```  


```  
http://docs.cloudstack.apache.org/projects/cloudstack-installation/en/4.4/installation.html
Minimum System Requirements
Management Server, Database, and Storage System Requirements
The machines that will run the Management Server and MySQL database must meet the following requirements. The same machines can also be used to provide primary and secondary storage, such as via localdisk or NFS. The Management Server may be placed on a virtual machine.
    Operating system:
        Preferred: CentOS/RHEL 6.3+ or Ubuntu 12.04(.1)
    64-bit x86 CPU (more cores results in better performance)
    4 GB of memory
    250 GB of local disk (more results in better capability; 500 GB recommended)
    At least 1 NIC
    Statically allocated IP address
    Fully qualified domain name as returned by the hostname command
 
Host/Hypervisor System Requirements
The host is where the cloud services run in the form of guest virtual machines. Each host is one machine that meets the following requirements:
    Must support HVM (Intel-VT or AMD-V enabled).
    64-bit x86 CPU (more cores results in better performance)
    Hardware virtualization support required
    4 GB of memory
    36 GB of local disk
    At least 1 NIC
```  

```  
CloudStack
  -- 2008年由Cloud.com开发, 有商业和开源两个版本. 开源版本采用GPL v2 license.
  -- 2011年7月Cloud.com被Citrix(思杰)收购, CloudStack全部开源.
  -- 2012年4月Citrix将CloudStack贡献给Apache基金会, 改为Apache 2.0 license.
OpenStack
  -- Rackspace和NASA合作开发, 2010年10月开源, 采用Apache 2.0 license.
  -- 2011年Rackspace宣布成立OpenStack基金会, 将OpenStack贡献给该基金会.

OpenStack 使用 RabbitMQ做消息通信, 有人提出100各节点RabbitMQ会出现问题. Joe表示SwiftStack使用zeroMQ代替了RabbitMQ.
大规模部署,可以将每个zone作为独立的单元进行部署, 各自独立运作, 在上层另外建一套调度系统.
CloudStack
  10K resources managed per management server node
  Scales out horizontally (must disable stats collector)
  Real production deployment of tens of thousands of resources
CloudStack
  -- Hypervisor XenServer, XCP, OVM, vMWare, KVM, Bare Metal
  -- 数据库 MySQL
  -- 编码 Java
 
  -- 二级存储 NAS, 后端可以Pass-through给Wsift
  -- 主存储 现有的存储设备, NAS, Iscsi, FC-SAN, Local Disk
     Ceph (CloudStack 4.0)
  -- S3 Like 集成 Caringo, 提供S3功能
OpenStack
  -- Hypervisor XenServer, OpenXen, OVM, vMWare, KVM, QEMU, LXC (Linux Container)
  -- 数据库 PostgreSQL, MySQL, SQLite
  -- 编码 Python, 各模块独立
  -- 存储 Swift (Object storage) Cinder(Block storage)
产品成熟度 CloudStack全球商用案例超过100个, OpenStack不到10个(不包含仅用OpenStack Swift存储模块)
社区管理
  CloudStack是Apache基金会项目, 作为全球最大的开源项目管理基金会, 我们更相信他们对开源项目的管理能力
  OpenStack是由OpenStack基金会董事会管理
开发语言 CloudStack采用Java, C  OpenStack采用Python

```  

