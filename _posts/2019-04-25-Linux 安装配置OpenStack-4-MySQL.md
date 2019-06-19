---
layout:     post
title:      安装部署OpenStack-4
subtitle:   MySQL
date:       2019-06-19
author:     火花
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - env
---
**注意：OpenStack包的设置需要在所有节点上完成：控制器，计算和块存储节点。**
# 安装和配置组件 #

安装MySQL版本

	# yum install mariadb mariadb-server python2-PyMySQL

创建和编辑/etc/my.cnf.d/openstack.cnf文件

	[mysqld]
	bind-address = 10.0.0.11
	
	default-storage-engine = innodb
	innodb_file_per_table = on
	max_connections = 4096
	collation-server = utf8_general_ci
	character-set-server = utf8

# 完成安装 #

1、启动数据库服务并将其配置为在系统引导时启动：

	# systemctl enable mariadb.service
	# systemctl start mariadb.service

2、通过运行mysql_secure_installation 脚本来保护数据库服务

	# mysql_secure_installation

--------------------- 

[参照](https://docs.openstack.org/install-guide/)

