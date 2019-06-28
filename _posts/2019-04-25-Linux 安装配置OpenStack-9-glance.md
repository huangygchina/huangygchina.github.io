---
layout:     post
title:      安装部署OpenStack-9
subtitle:   glance
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

2、创建glance数据库:

	MariaDB [(none)]> CREATE DATABASE glance;

3、授予对keystone数据库的适当访问权限：

	# MariaDB [(none)]> GRANT ALL PRIVILEGES ON glance.* TO 'glance'@'localhost' \
  IDENTIFIED BY 'glance_123456';
	MariaDB [(none)]> GRANT ALL PRIVILEGES ON glance.* TO 'glance'@'%' \
  IDENTIFIED BY 'glance_123456';

4、admin凭据来访问仅管理员CLI命令：

	. admin-openrc

5、创建glance用户：

	openstack user create --domain default --password-prompt glance

6、将admin角色添加到glance用户和 service项目：

	openstack role add --project service --user glance admin

7、创建glance服务实体：

	openstack service create --name glance \
	  --description "OpenStack Image" image

8、创建Image服务API端点：

	$ openstack endpoint create --region RegionOne \
	  image public http://controller:9292

	$ openstack endpoint create --region RegionOne \
	  image internal http://controller:9292

	$ openstack endpoint create --region RegionOne \
	  image admin http://controller:9292

# 安装和配置组件 #

1、运行以下命令以安装软件包：

	# yum install openstack-glance


2、编辑/etc/glance/glance-api.conf文件并完成以下操作：

	[database]
	# ...
	connection = mysql+pymysql://glance:glance_123456@controller/glance
	
	[keystone_authtoken]
	# ...
	www_authenticate_uri  = http://controller:5000
	auth_url = http://controller:5000
	memcached_servers = controller:11211
	auth_type = password
	project_domain_name = Default
	user_domain_name = Default
	project_name = service
	username = glance
	password = GLANCE_PASS
	
	[paste_deploy]
	# ...
	flavor = keystone

	[glance_store]
	# ...
	stores = file,http
	default_store = file
	filesystem_store_datadir = /var/lib/glance/images/

3、编辑/etc/glance/glance-registry.conf文件并完成以下操作：

	[database]
	# ...
	connection = mysql+pymysql://glance:glance_123456@controller/glance

	[keystone_authtoken]
	# ...
	www_authenticate_uri = http://controller:5000
	auth_url = http://controller:5000
	memcached_servers = controller:11211
	auth_type = password
	project_domain_name = Default
	user_domain_name = Default
	project_name = service
	username = glance
	password = 123456
	
	[paste_deploy]
	# ...
	flavor = keystone

4、填充Image服务数据库：

	su -s /bin/sh -c "glance-manage db_sync" glance

# 完成安装 #

1、启动Image服务并将其配置为在系统引导时启动：：

	# systemctl enable openstack-glance-api.service \
	  openstack-glance-registry.service
	# systemctl start openstack-glance-api.service \
	  openstack-glance-registry.service

--------------------- 

[参照](https://docs.openstack.org/install-guide/)

