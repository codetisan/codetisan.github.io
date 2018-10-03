---
published: true
layout: postlayout
thumbimg: 1346208288725.jpg
categories:
  - zookeeper
tags:
  - zookeeper
title: zookeeper集群安装配置
---

# 前置条件
JDK8
## 使用版本
**3.4.9**
# 说明
zookeeper集群**至少需要三台服务器，且只能奇数台**，以下配置假设三个ip地址分别为：IP1，IP2，IP3

# 集群部署
## 安装
1. 下载安装包

	`wget http://mirror.cogentco.com/pub/apache/zookeeper/zookeeper-3.4.9/`
2. 解压安装包

	`tar zxf zookeeper-3.4.9.tar.gz`
3. 创建链接目录

	`ln -s zookeeper-3.4.9 zookeeper`
## 配置
1. 创建zookeeper必要的目录

	`mkdir -p /macc/data/zookeeper`

	`mkdir -p /macc/logs/zookeeper`

	`mkdir -p /etc/zookeeper`

2. 配置zookeeper的配置文件，新建文件/etc/zookeeper/zoo.cfg，并填写以下内容
	
	`tickTime=2000`

	`initLimit=5`

	`syncLimit=2`

	`clientPort=2181`

	`dataDir=/macc/data/zookeeper`

	`dataLogDir=/macc/logs/zookeeper`

	`server.1={IP1}:2888:3888`
	
	`server.2={IP2}:2889:3889`
	
	`server.3={IP3}:2890:3890`

3. 创建myid文件

	在IP1服务器上执行：
	
	`echo "1" > /macc/data/zookeeper/myid`
	
	在IP2服务器上执行:
	
	`echo "2" > /macc/data/zookeeper/myid`
	
	在IP3服务器上执行:
	
	`echo "3" > /macc/data/zookeeper/myid`

## 启动
1. 在三台服务器上分别执行
	
	`${ZK_HOME}/bin/zkServer.sh start /etc/zookeeper/zoo.cfg`

2. 在三台服务器上确认集群状态，在三台服务器分别执行：

	`${ZK_HOME}/bin/zkServer.sh status /etc/zookeeper/zoo.cfg`

	其中一台输出包含：`Mode: leader`，表示这个节点是主节点
	
	另外两台输出包含：`Mode: follower`，表示这两个节点是从节点

	至此，zookeeper集群搭建完成

## 关闭
1. 在对应服务器上执行

	`${ZK_HOME}/bin/zkServer.sh stop /etc/zookeeper/zoo.cfg`
## 高可用测试
1. 将macc的zookeeper.properties配置内容修改为：
	
	**`zookeeper.servers={IP1}:2181,{IP2}:2181,{IP3}:2181`**
2. 在主服务器上将zookeeper关闭，等待10s后，再次确认另外两台的状态，发现其中一台已经自动升为主服务器
3. macc那边对应的客户端自动将zookeeper切换到新的主服务，业务端无感知（注意：如果切换过程中，刚好有job在执行，那么会抛异常：zookeeper连接不上，但是切换完成后，又会自动连接） 


# 伪集群部署
伪集群部署，是指在一台机器上部署多个zookeeper节点，组成一个集群，主要用于开发/测试zookeeper集群的自动故障转移，不可用于生产环境

## 安装
与上述安装步骤一样
## 配置
1. 创建zookeeper必要的目录

	`mkdir -p /macc/data/zookeeper-2181`

	`mkdir -p /macc/data/zookeeper-2182`

	`mkdir -p /macc/data/zookeeper-2183`

	`mkdir -p /macc/logs/zookeeper-2181`
	
	`mkdir -p /macc/logs/zookeeper-2182`

	`mkdir -p /macc/logs/zookeeper-2183`

	`mkdir -p /etc/zookeeper`

2. 配置zookeeper的配置文件

	新建文件/etc/zookeeper/zoo-2181.cfg，并填写以下内容：
	
	`tickTime=2000`

	`initLimit=5`

	`syncLimit=2`

	`clientPort=2181`

	`dataDir=/macc/data/zookeeper-2181`

	`dataLogDir=/macc/logs/zookeeper-2181`

	`server.1={IP}:2888:3888`
	
	`server.2={IP}:2889:3889`
	
	`server.3={IP}:2890:3890`

	新建文件/etc/zookeeper/zoo-2182.cfg，并填写以下内容：
	
	`tickTime=2000`

	`initLimit=5`

	`syncLimit=2`

	`clientPort=2182`

	`dataDir=/macc/data/zookeeper-2182`

	`dataLogDir=/macc/logs/zookeeper-2182`

	`server.1={IP}:2888:3888`
	
	`server.2={IP}:2889:3889`
	
	`server.3={IP}:2890:3890`

	新建文件/etc/zookeeper/zoo-2183.cfg，并填写以下内容：
	
	`tickTime=2000`

	`initLimit=5`

	`syncLimit=2`

	`clientPort=2183`

	`dataDir=/macc/data/zookeeper-2183`

	`dataLogDir=/macc/logs/zookeeper-2183`

	`server.1={IP}:2888:3888`
	
	`server.2={IP}:2889:3889`
	
	`server.3={IP}:2890:3890`

3. 创建myid文件

	
	`echo "1" > /macc/data/zookeeper-2181/myid`
	
	
	`echo "2" > /macc/data/zookeeper-2182/myid`

	
	`echo "3" > /macc/data/zookeeper-2183/myid`

## 启动
1. 分别执行
	
	`${ZK_HOME}/bin/zkServer.sh start /etc/zookeeper/zoo-2181.cfg`

	`${ZK_HOME}/bin/zkServer.sh start /etc/zookeeper/zoo-2182.cfg`

	`${ZK_HOME}/bin/zkServer.sh start /etc/zookeeper/zoo-2183.cfg`

2. 分别执行：

	`${ZK_HOME}/bin/zkServer.sh status /etc/zookeeper/zoo-2181.cfg`

	`${ZK_HOME}/bin/zkServer.sh status /etc/zookeeper/zoo-2182.cfg`

	`${ZK_HOME}/bin/zkServer.sh status /etc/zookeeper/zoo-2183.cfg`

	其中一台输出包含：`Mode: leader`，表示这个节点是主节点
	
	另外两台输出包含：`Mode: follower`，表示这两个节点是从节点

	至此，zookeeper伪集群搭建完成
## 关闭
1. 在服务器上执行

	`${ZK_HOME}/bin/zkServer.sh stop /etc/zookeeper/zoo-{端口}.cfg`

## 高可用测试
1. 将macc的zookeeper.properties配置内容修改为：
	**`zookeeper.servers={IP}:2181,{IP}:2182,{IP}:2183`**

2. 在主服务器上将zookeeper关闭，等待10s后，再次确认另外两台的状态，发现其中一台已经自动升为主服务器
3. macc那边对应的客户端自动将zookeeper切换到新的主服务，业务端无感知（注意：如果切换过程中，刚好有job在执行，那么会抛异常：zookeeper连接不上，但是切换完成后，又会自动连接） 