
virtualbox_run_centos_7.1

# VirtualBox run centos 7.1 #

## 准备VirtualBox和CentOS镜像 ##

VirtualBox下载地址为  
Oracle下载VirtualBox   
[http://www.oracle.com/technetwork/server-storage/virtualbox/downloads/index.html](http://www.oracle.com/technetwork/server-storage/virtualbox/downloads/index.html)  
VirtualBox官方下载地址  
[https://www.virtualbox.org/wiki/Downloads](https://www.virtualbox.org/wiki/Downloads)  
VirtualBox版本为 5.0.16


CentOS ISO镜像下载  
很多地方都可以下载, 这里使用 [http://mirror.bit.edu.cn/centos/7/isos/x86_64/](http://mirror.bit.edu.cn/centos/7/isos/x86_64/)  
文章使用 CentOS-7-x86_64-Minimal-1511.iso 

## 创建CentOS虚拟机 ##
安装好VirtualBox, 下载CentOS完毕后, 开始使用VirtualBox创建CentOS虚拟机.  

### 新建虚拟电脑 ###

#### 1. 虚拟电脑名称和类型 ####
打开VirtualBox软件, 点击 **新建**, 弹出 **新建虚拟电脑** 对话框. 需要提供虚拟电脑名称和系统类型.  
**名称(N)**  centos7_minimal_demo  
**类型(T)**  选择 **Linux**  
**版本(V)**  选择 **Other Linux (64-bit)**  

点击 **下一步** 

#### 2. 内存大小 ####
设定虚拟机内存, 这是设置为 **1024** , 单位是MB.  
点击 **下一步**  

#### 3. 虚拟硬盘 ####  
有三个选项  
> (1) 不添加虚拟硬盘(D)  
> (2) 现在创建虚拟硬盘(C)  
> (3) 使用已有的虚拟硬盘文件(U)  

这里使用默认的选项, 选择 **现在创建虚拟硬盘(C)**, 点击 **创建** 进入 **创建虚拟硬盘** 对话框.  

##### 3.1 虚拟硬盘文件类型 #####  
虚拟硬盘文件类型支持  
> (1) VDI (VirtualBox 磁盘映像)  
> (2) VMDK (虚拟机磁盘)  
> (3) VHD (虚拟硬盘)  
> (4) HDD (并口硬盘)  
> (5) QED (QEMU 增强型磁盘)  
> (6) QCOW (QEMU 写入时复制)  


这里使用默认的选项, **VDI (VirtualBox 磁盘映像)**, 点击 **下一步**.  

##### 3.2 存储在物理硬盘上 #####
选择新建虚拟硬盘文件是 **动态分配** 还是 **固定大小**.  
**动态分配**的虚拟磁盘只是逐渐占用物理磁盘的空间(直至达到**分配的大小**), 不过当其内部空间不用时不会自动缩减占用的物理磁盘空间.  
**固定大小**的虚拟磁盘文件可能在某些操作系统中要花费长时间来创建, 但它往往使用起来较快.  

> (1) 动态分配(D)  
> (2) 固定大小(F)  

这里使用默认的 **动态分配(D)**, 点击 **下一步**.  

##### 3.3 文件位置和大小 #####  
可以选择自定义的文件夹存储磁盘文件, 这里使用默认的文件目录.  
将磁盘大小设置为 **30** GB, 点击 **创建**.  

整个虚拟机都创建好了, 现在需要为它指定镜像等环境配置.  



## 配置虚拟电脑 ##

选择刚才创建的虚拟机, 右键选择 **设置**  
**设置**对话框包含以下选项  
> 常规  
> 系统  
> 显示  
> 存储  
> 声音  
> 网络  
> 串口  
> USB设备  
> 共享文件夹  
> 用户界面  



### 1. 配置ISO镜像 ###

选中虚拟机, 右键选择 **设置**  
点击 **存储** 选项, 右侧出现虚拟机的存储设备. 一个**vdi磁盘**, 和一个 **没有盘片**的光盘.  
点击 **没有盘片**, 右侧出现光驱信息.  右侧还有一个 **光盘形状的图标**, 点击 **光盘形状的图标**, 在下拉列表中选择 **选择一个虚拟光盘文件...**. 选择下载好的CentOS镜像.  

选择成功后 **明细** 会列出ISO文件信息.  
**类型**  映像  
**容量大小** 603.00MB  
**位置**  (省略)  

点击 **确定** 保存对光驱信息的修改.  


### 2. 配置网络 ###

选中虚拟机, 右键选择 **设置**.  
点击 **网络** 选项, 将 **连接方式** 修改为 **桥接网卡**.  

桥接网卡会列出你本机的网卡列表, 如果本机网络使用无线网时, 应该选择无线网卡.  如果本机使用网线, 选择有线网卡.  

**高级** 里面有MAC地址或接入网线等设置. 确认 **接入网线(C)** 被选中.  

点击 **确定** 配置好网络.  


## 启动虚拟机 ##

右键刚才创建的虚拟机, 选择 **启动** --> **正常启动(N)**  

进入操作系统, 选择 "Install CentOS 7"  


## 安装CentOS 7 ##

1. 语言选择 **English** 和 **English (United States)** 默认选项, 点击 **Continue**  

2. 进入 **INSTALLATION SUMMARY** 界面  

> (2.1) 点击 **SYSTEM** 中 **INSTALLATION DESTINATION**, 可以看到一块30G的磁盘, 点击左上角 **Done** 即可完成磁盘配置.  

> (2.2) 点击 **SYSTEM** 中 **NETWORK & HOSTNAME**, 
左下角修改机器名,  **Host name** 输入 centos7-demo  
右侧需要打开网络 **Ethernet(enpOs3)** 选择 **ON**, 选择后可以看到虚拟机的IP地址. 使用桥接模式, 虚拟机和物理机在同一网段.  
点击 **Done** 完成网络配置.  


> (2.3) 点击 **LOCALIZATION** 中的 **DATE & TIME**, 选择  
**Region**  **Asia**  
**City**  **Shanghai**  
保证虚拟机时间与北京时间相同.  
**Network Time** 设置为 **ON**  
点击 **DOne** 完成设置  


点击 **Begin Installation**, 开始安装CentOS.  

**USER SETTINGS** 可以设置ROOT用户密码, 或者新建用户.  

安装完成后, 点击 **Reboot** 重启虚拟机.  

重启后, 输入用户名和密码. 开始使用CentOS.  
```  
[root@centos7-demo ~]# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 08:00:27:3a:32:48 brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.100/24 brd 192.168.1.255 scope global dynamic enp0s3
       valid_lft 7093sec preferred_lft 7093sec
    inet6 fe80::a00:27ff:fe3a:3248/64 scope link
       valid_lft forever preferred_lft forever
[root@centos7-demo ~]# df -h
Filesystem               Size  Used Avail Use% Mounted on
/dev/mapper/centos-root   28G  840M   27G   3% /
devtmpfs                 487M     0  487M   0% /dev
tmpfs                    497M     0  497M   0% /dev/shm
tmpfs                    497M  6.6M  490M   2% /run
tmpfs                    497M     0  497M   0% /sys/fs/cgroup
/dev/sda1                497M  122M  375M  25% /boot
tmpfs                    100M     0  100M   0% /run/user/0
[root@centos7-demo ~]#

```  








