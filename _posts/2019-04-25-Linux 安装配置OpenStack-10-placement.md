---
layout:     post
title:      安装部署OpenStack-10
subtitle:   placement
date:       2019-06-20
author:     火花
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - env
---
# 先决条件 #

1、使用数据库访问客户端以root用户身份连接到数据库服务器:

	#  mysql -u root -p

2、创建keystone数据库:

	MariaDB [(none)]> CREATE DATABASE placement;

3、授予对keystone数据库的适当访问权限：

	# MariaDB [(none)]> GRANT ALL PRIVILEGES ON placement.* TO 'placement'@'localhost' \
  IDENTIFIED BY 'placement_123456';
	MariaDB [(none)]> GRANT ALL PRIVILEGES ON placement.* TO 'placement'@'%' \
  IDENTIFIED BY 'placement_123456';

# 配置用户和端点 #

1、admin凭据来访问仅管理员CLI命令：

	. admin-openrc

2、创建Placement服务用户PLACEMENT_PASS：：

	openstack user create --domain default --password-prompt placement

6、将admin角色添加到glance用户和 service项目：

	 openstack role add --project service --user placement admin

7、创建placement服务实体：

	openstack service create --name placement \
	--description "Placement API" placement

8、创建placement服务API端点：

	openstack endpoint create --region RegionOne \
	  placement public http://controller:8778

	openstack endpoint create --region RegionOne \
	  placement internal http://controller:8778

	openstack endpoint create --region RegionOne \
	  placement admin http://controller:8778

# 安装和配置组件 #

1、运行以下命令以安装软件包：

	# yum install openstack-placement-api


2、编辑/etc/placement/placement.conf文件并完成以下操作：

	[placement_database]
	# ...
	connection = mysql+pymysql://placement:placement_123456@controller/placement
	
	[api]
	# ...
	auth_strategy = keystone
	
	[keystone_authtoken]
	# ...
	auth_url = http://controller:5000/v3
	memcached_servers = controller:11211
	auth_type = password
	project_domain_name = default
	user_domain_name = default
	project_name = service
	username = placement
	password = 123456


3、填充placement服务数据库：

	su -s /bin/sh -c "placement-manage db sync" placement

# 完成安装 #

1、重启httpd服务：

	# systemctl restart httpd

--------------------- 

[参照](https://docs.openstack.org/install-guide/)

