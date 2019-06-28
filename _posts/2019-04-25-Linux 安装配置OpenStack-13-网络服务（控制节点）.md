---
layout:     post
title:      安装部署OpenStack-13
subtitle:   网络服务（nova）-控制节点
date:       2019-06-20
author:     火花
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - env
---
# controller node #

# 先决条件 #

1、使用数据库访问客户端以root用户身份连接到数据库服务器:

	#  mysql -u root -p

2、创建nova_api, nova, and nova_cell0数据库:

	MariaDB [(none)]> CREATE DATABASE neutron;

3、授予对数据库的适当访问权限：

	MariaDB [(none)]> GRANT ALL PRIVILEGES ON neutron.* TO 'neutron'@'localhost' \
	  IDENTIFIED BY 'neutron_123456';
	MariaDB [(none)]> GRANT ALL PRIVILEGES ON neutron.* TO 'neutron'@'%' \
	  IDENTIFIED BY 'neutron_123456';


# 配置用户和端点 #

1、admin凭据来访问仅管理员CLI命令：

	. admin-openrc

2、创建nova服务用户：

	openstack user create --domain default --password-prompt nova

6、将admin角色添加到glance用户和 service项目：

	openstack role add --project service --user nova admin

7、创建Compute服务实体：

	openstack service create --name nova \
	  --description "OpenStack Compute" compute

8、创建Compute服务API端点：

	openstack endpoint create --region RegionOne \
	  compute public http://controller:8774/v2.1

	openstack endpoint create --region RegionOne \
	  compute internal http://controller:8774/v2.1

	openstack endpoint create --region RegionOne \
	  compute admin http://controller:8774/v2.1

# 安装和配置组件 #

1、运行以下命令以安装软件包：

	yum install openstack-nova-api openstack-nova-conductor \
	  openstack-nova-novncproxy openstack-nova-scheduler


2、编辑/etc/nova/nova.conf文件并完成以下操作：

	[DEFAULT]
	# ...
	enabled_apis = osapi_compute,metadata
	
	[api_database]
	# ...
	connection = mysql+pymysql://nova:NOVA_DBPASS@controller/nova_api
	
	[database]
	# ...
	connection = mysql+pymysql://nova:NOVA_DBPASS@controller/nova

	[DEFAULT]
	# ...
	transport_url = rabbit://openstack:RABBIT_PASS@controller
	
	[api]
	# ...
	auth_strategy = keystone
	
	[keystone_authtoken]
	# ...
	auth_url = http://controller:5000/v3
	memcached_servers = controller:11211
	auth_type = password
	project_domain_name = Default
	user_domain_name = Default
	project_name = service
	username = nova
	password = NOVA_PASS
	
	[DEFAULT]
	# ...
	my_ip = 10.0.0.11
	
	[DEFAULT]
	# ...
	use_neutron = true
	firewall_driver = nova.virt.firewall.NoopFirewallDriver
	
	[vnc]
	enabled = true
	# ...
	server_listen = $my_ip
	server_proxyclient_address = $my_ip
	
	[glance]
	# ...
	api_servers = http://controller:9292
	
	[oslo_concurrency]
	# ...
	lock_path = /var/lib/nova/tmp
	
	[placement]
	# ...
	region_name = RegionOne
	project_domain_name = Default
	project_name = service
	auth_type = password
	user_domain_name = Default
	auth_url = http://controller:5000/v3
	username = placement
	password = PLACEMENT_PASS

3、填充nova-api服务数据库：

	su -s /bin/sh -c "placement-manage db sync" placement

4、注册cell0数据库：

	# su -s /bin/sh -c "nova-manage cell_v2 map_cell0" nova

5、创建cell1单元格：

	# su -s /bin/sh -c "nova-manage cell_v2 create_cell --name=cell1 --verbose" nova

6、填充nova数据库：

	# su -s /bin/sh -c "nova-manage db sync" nova

7、验证nova cell0和cell1是否正确注册：

	# su -s /bin/sh -c "nova-manage cell_v2 list_cells" nova

# 完成安装 #

1、重启httpd服务：

	# systemctl enable openstack-nova-api.service \
	  openstack-nova-scheduler.service \
	  openstack-nova-conductor.service openstack-nova-novncproxy.service
	
	# systemctl start openstack-nova-api.service \
	  openstack-nova-scheduler.service \
	  openstack-nova-conductor.service openstack-nova-novncproxy.service

 官网上的openstack-nova-consoleauth这个是什么鬼？？？？

--------------------- 

[参照](https://docs.openstack.org/install-guide/)

