
# linux_command.md  

```  

$ netstat -tln
$ netstat -an|grep 6381
$ netstat -an|grep "10.171.93.226:6381"
$ netstat -an|grep "0.0.0.0.0:3306"
$ tail -f mae-data-active-order-runtime.2015-01-22.0.log  |grep "ERROR"
$ cat mae-wifi-front.2015-01-26.0.log |grep "testuuid"
 
# rpm -q jdk-7u55-linux-x64.rpm
package jdk-7u55-linux-x64.rpm is not installed
# rpm -ivh jdk-7u55-linux-x64.rpm

https://hub.docker.com/r/sonatype/nexus/~/dockerfile/
# curl --fail --silent --location --retry 3 -O --header "Cookie: oraclelicense=accept-securebackup-cookie; " http://download.oracle.com/otn-pub/java/jdk/7u76-b13/jdk-7u76-linux-x64.rpm

# curl --fail --silent --location --retry 3 -O https://download.sonatype.com/nexus/oss/nexus-2.11.4-01-bundle.tar.gz

 
内存
$ free
$ free -m
$ cat /proc/meminfo # 机器的内存使用信息
$ cat /proc/pid/maps  # pid为进程号，显示当前进程所占用的虚拟地址
$ cat /proc/pid/statm  # 进程所占用的内存
 
查看版本
cat /proc/version
uname -a
uname -r
lsb_release -a
cat /etc/issue
 
find / -name somename
 
 
du -h
du -sh
du -sh ./*
du -sh /var/*
 
cat /proc/version
Linux version 2.6.32-431.el6.x86_64 (mockbuild@c6b8.bsys.dev.centos.org) (gcc version 4.4.7 20120313 (Red Hat 4.4.7-4) (GCC) ) #1 SMP Fri Nov 22 03:15:09 UTC 2013

uname -a
Linux root 2.6.32-431.el6.x86_64 #1 SMP Fri Nov 22 03:15:09 UTC 2013 x86_64 x86_64 x86_64 GNU/Linux

uname -r
2.6.32-431.el6.x86_64

lsb_release -a
-bash: lsb_release: command not found
 
cat /etc/issue
CentOS release 6.5 (Final)
Kernel \r on an \m

 时间
date -R
Wed, 04 Feb 2015 16:01:03 +0800
# date
Wed Feb  4 16:01:18 CST 2015
# ntpdate time.nist.gov
 
# linux 无法同步时间
# ntpdate time.nist.gov
Error resolving time.nist.gov: Servname not supported for ai_socktype (-8)
28 Feb 00:36:07 ntpdate[24869]: Can't find host time.nist.gov: Servname not supported for ai_socktype (-8)
28 Feb 00:36:07 ntpdate[24869]: no servers can be used, exiting

# vim /etc/services
ntp  123/tcp
ntp  123/udp
# ntpdate time.nist.gov
 
# vim /etc/sysconfig/network
# vim /etc/hosts
 
# vim /etc/crontab
# cd /var/spool/cron
 
 iptables
# Generated by iptables-save v1.4.7 on Fri Apr 10 11:58:51 2015
*filter
:INPUT ACCEPT [0:0]
:FORWARD ACCEPT [0:0]
:OUTPUT ACCEPT [0:0]
-A INPUT -m state --state NEW -m tcp -p tcp --dport 22 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 2181 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 3306 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 6380:6385 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 8080 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 8880 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 30590:30599 -j ACCEPT
COMMIT
# Completed on Fri Apr 10 11:58:51 2015
 
rpm
rpm -ivh example.rpm
 
 
镜像位置
http://mirrors.pubyun.com/centos/7.0.1406/isos/x86_64/CentOS-7.0-1406-x86_64-DVD.iso
http://mirror.neu.edu.cn/centos/7.0.1406/isos/x86_64/CentOS-7.0-1406-x86_64-DVD.iso
http://mirrors.aliyun.com/centos/7.0.1406/isos/x86_64/CentOS-7.0-1406-x86_64-DVD.iso
http://mirrors.cqu.edu.cn/CentOS/7.0.1406/isos/x86_64/CentOS-7.0-1406-x86_64-DVD.iso
http://mirrors.163.com/centos/7.0.1406/isos/x86_64/CentOS-7.0-1406-x86_64-DVD.iso
http://mirror.bit.edu.cn/centos/7.0.1406/isos/x86_64/CentOS-7.0-1406-x86_64-DVD.iso
http://mirrors.yun-idc.com/centos/7.0.1406/isos/x86_64/CentOS-7.0-1406-x86_64-DVD.iso
http://centos.ustc.edu.cn/centos/7.0.1406/isos/x86_64/CentOS-7.0-1406-x86_64-DVD.iso
http://ftp.sjtu.edu.cn/centos/7.0.1406/isos/x86_64/CentOS-7.0-1406-x86_64-DVD.iso
 
http://mirrors.aliyun.com/
若使用阿里云服务器，将源的域名从mirrors.aliyun.com改为mirrors.aliyuncs.com,不占用公网流量。
http://mirrors.163.com/
 
包含apache
http://mirror.bit.edu.cn/
http://mirrors.ustc.edu.cn/

 
硬件地址最小由 00:00:00:00:00:00 到 FF:FF:FF:FF:FF:FF (16 进位法)， 这 6 bytes 当中，前 3bytes 为厂商的代码，后 3bytes 则是该厂商自行设定的装置码了。

 
磁盘扩展
# pvdisplay

# vgdisplay

# lvdisplay


# fdisk -l
# pvscan


lvextend
http://man.linuxde.net/lvextend

先摘除磁盘,然后缩减文件系统,再缩减逻辑分区
http://bbs.chinaunix.net/thread-3773669-1-1.html
http://bbs.chinaunix.net/forum.php?mod=viewthread&tid=3771222
# umount /home
# e2fsck -f /dev/mapper/VolGroup-lv_home
# resize2fs /dev/mapper/VolGroup-lv_home 150G
# lvreduce -L 150G /dev/mapper/VolGroup-lv_home
# mount /dev/mapper/VolGroup-lv_home /home



lvreduce
http://man.linuxde.net/lvreduce

 

[root@dev /]# df -h
Filesystem            Size  Used Avail Use% Mounted on
/dev/mapper/VolGroup-lv_root
                       50G   14G   34G  29% /
tmpfs                 5.8G     0  5.8G   0% /dev/shm
/dev/sda1             477M   33M  419M   8% /boot
/dev/mapper/VolGroup-lv_home
                      862G   72M  818G   1% /home


[root@dev /]# df -lh
Filesystem            Size  Used Avail Use% Mounted on
/dev/mapper/VolGroup-lv_root
                       50G   15G   33G  32% /
tmpfs                 5.8G     0  5.8G   0% /dev/shm
/dev/sda1             477M   33M  419M   8% /boot
/dev/mapper/VolGroup-lv_home
                      148G   60M  140G   1% /home


# vgs
# vgs
  VG       #PV #LV #SN Attr   VSize   VFree
  VolGroup   1   3   0 wz--n- 931.02g 725.16g


# pvs
  PV         VG       Fmt  Attr PSize   PFree
  /dev/sda2  VolGroup lvm2 a--  931.02g 725.16g

# lvcreate -L 300G VolGroup
  Logical volume "lvol0" created


# lvcreate -L 300G VolGroup
  Logical volume "lvol1" created


# lvdisplay

LV Path                /dev/VolGroup/lvol0
LV Path                /dev/VolGroup/lvol1

# mkfs.ext4 /dev/VolGroup/lvol0
# mkfs.ext4 /dev/VolGroup/lvol1

# mount /dev/VolGroup/lvol0 /var/log
# mount /dev/VolGroup/lvol1 /opt

# lvremove /dev/VolGroup/lvol1
# lvremove /dev/VolGroup/lvol0

使用扩展命令
# lvextend -L +450G /dev/mapper/VolGroup-lv_root
# resize2fs /dev/mapper/VolGroup-lv_root


http://blog.sina.com.cn/s/blog_6ce9e88701014jgl.html
# vim /etc/fstab
/dev/VolGroup/lvol0          /var/log                ext4    defaults        1 2
/dev/VolGroup/lvol1          /opt                    ext4    defaults        1 2



http://blog.csdn.net/q123456789098/article/details/7821198
物理存储介质（The physical media）
物理卷（physical volume）
卷组（Volume Group）
逻辑卷（logical volume）
PE（physical extent）
LE（logical extent）




[root@dev /]# df -hal
Filesystem            Size  Used Avail Use% Mounted on
/dev/mapper/VolGroup-lv_root
                       50G   16G   31G  35% /
proc                     0     0     0    - /proc
sysfs                    0     0     0    - /sys
devpts                   0     0     0    - /dev/pts
tmpfs                 5.8G     0  5.8G   0% /dev/shm
/dev/sda1             477M   33M  419M   8% /boot
none                     0     0     0    - /proc/sys/fs/binfmt_misc
/dev/mapper/VolGroup-lv_home
                      148G   60M  140G   1% /home





# fdisk -l
# fdisk /dev/sda

WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
         switch off the mode (command 'c') and change display units to
         sectors (command 'u').

Command (m for help): m
Command action
   a   toggle a bootable flag
   b   edit bsd disklabel
   c   toggle the dos compatibility flag
   d   delete a partition
   l   list known partition types
   m   print this menu
   n   add a new partition
   o   create a new empty DOS partition table
   p   print the partition table
   q   quit without saving changes
   s   create a new empty Sun disklabel
   t   change a partition's system id
   u   change display/entry units
   v   verify the partition table
   w   write table to disk and exit
   x   extra functionality (experts only)


 CentOS搭建本地yum源（http方式）
http://my.oschina.net/u/1461927/blog/372147

CentOS yum 源的配置与使用
http://www.cnblogs.com/mchina/archive/2013/01/04/2842275.html


```  
