---
layout:     post
title:      安装部署OpenStack-5
subtitle:   RabbitMq
date:       2019-06-19
author:     火花
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - env
---
# 安装和配置组件 #

1、Install the package:

	# yum install rabbitmq-server

2、Start the message queue service and configure it to start when the system boots:

	# systemctl enable rabbitmq-server.service
	# systemctl start rabbitmq-server.service

3、Add the openstack user:

	# rabbitmqctl add_user openstack RABBIT_PASS
	
	Creating user "openstack" ...
4、Permit configuration, write, and read access for the openstack user:
	
	# rabbitmqctl set_permissions openstack ".*" ".*" ".*"
	
	Setting permissions for user "openstack" in vhost "/" ...

5、rabbitmq安装后，无法直接访问管理页面。需执行以下命令：

	rabbitmq-plugins enable rabbitmq_management

--------------------- 

[参照](https://docs.openstack.org/install-guide/)

