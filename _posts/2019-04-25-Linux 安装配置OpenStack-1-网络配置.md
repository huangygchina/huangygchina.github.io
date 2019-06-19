---
layout:     post
title:      安装部署OpenStack-1
subtitle:   网络配置
date:       2019-06-19
author:     火花
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - env
---
# 控制节点 #
**1、Edit the file: /etc/sysconfig/network-scripts/ifcfg-eno1**

	DEVICE=INTERFACE_NAME
	TYPE=Ethernet
	ONBOOT="yes"
	BOOTPROTO="none"

**2、Set the hostname of the node to controller.**

	# controller
	10.50.10.49       controller
	
	# compute1
	10.50.10.36       compute1

	# block1
	10.50.10.26       block1


# 计算节点 #
**1、Edit the file: /etc/sysconfig/network-scripts/ifcfg-eno1**

	DEVICE=INTERFACE_NAME
	TYPE=Ethernet
	ONBOOT="yes"
	BOOTPROTO="none"

**2、Set the hostname of the node to controller.**

	# controller
	10.50.10.49       controller
	
	# compute1
	10.50.10.36       compute1
	
	# block1
	10.50.10.26       block1

# Block storage node #

	# controller
	10.50.10.49       controller
	
	# compute1
	10.50.10.36       compute1
	
	# block1
	10.50.10.26       block1


--------------------- 
作者：Good-Y 
来源：CSDN 
原文：https://blog.csdn.net/qq_39452428/article/details/80781403 
版权声明：本文为博主原创文章，转载请附上博文链接！

[参照](https://blog.csdn.net/qq_39452428/article/details/80781403)

