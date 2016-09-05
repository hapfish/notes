
# file_system.md   文件系统  


# 简介  

操作系统中负责管理和存储文件信息的软件机构成为文件管理系统, 简称文件系统.  

文件系统由三部分组成: 文件系统的接口, 对对象操作和管理的软件集合, 对象及属性.  


# RAW
RAW是文件系统是一种磁盘未经处理或者未经格式化产生的文件系统. 可能有以下几种来源  

- 没有格式化  
- 格式化中途取消操作  
- 磁盘出现坏道  
- 磁盘出现不可预知错误  
- 病毒导致  



# Windows文件系统  

## FAT16  
在Windows 9x下, FAT16支持的分区最大为2GB.  

## FAT32  
支持磁盘达32GB, 但不能支持小于512MB的分区.  

## NTFS  
NTFS是一个基于安全性的文件系统


# CDFS 
CDFS是大部分光盘的文件系统, 只有小部分使用其它文件系统能够.  


# Linux 文件系统  

## ext2  
一般在x86电脑系统中, 簇最大为4KB, 单一文件最大2048, 文件系统容量上限为16384G(16T).  

## ext3  
最大2TB文件和16TB文件系统.  

## ext4  
最大支持16TB文件和 1EB(1EB = 1025PB, 1PB = 1024TB)文件系统.  

##　BTRFS  
Oracle 2007年宣布并进行中的copt-on-write文件系统. 目标是取代ext4.  

## Reiser FS 
linux 内核2.4.1版本开始支持,处理小于1k文件时，　效率可以比ext2快约10倍.  


# Solaris

## ZFS 
ZFS是一个具有高存储量，文件系统与卷管理概念整合，崭新的磁盘逻辑结构的轻量级文件系统, 也是一个存储池管理系统. ZFS是一个使用CDDL协议条款授权的开源项目.  


# Mac  

## HFS 
分层文件系统(Hierarchical File System).  

## HFS+  


# JFS 

JFS(JOURNAL FILE SYSTEM) 一种字节级日志文件系统, 为面向事务的高性能系统而开发. 　 在IBM的AIX系统经过长时间的测试, 2000年2月在开放资源许可证下移植到Linux.  


# VMFS 
VMware Virtual Machine File System, 是一种高性能的集群文件系统.  


# 分布式文件系统  


## GFS  
Google File System  

## HDFS  


## Ceph 





































