---
layout:     post
title:      安装部署OpenStack-12
subtitle:   计算服务（nova）-计算节点
date:       2019-06-20
author:     火花
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - env
---
# compute node #


# 安装和配置组件 #

1、运行以下命令以安装软件包：

	# yum install openstack-nova-compute


2、编辑/etc/nova/nova.conf文件并完成以下操作：

	[DEFAULT]
	# ...
	enabled_apis = osapi_compute,metadata
	
	[DEFAULT]
	# ...
	transport_url = rabbit://openstack:123456@controller
	
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
	password = 123456
	
	[DEFAULT]
	# ...
	my_ip = 10.50.10.36
	
	[DEFAULT]
	# ...
	use_neutron = true
	firewall_driver = nova.virt.firewall.NoopFirewallDriver
	
	[vnc]
	# ...
	enabled = true
	server_listen = 0.0.0.0
	server_proxyclient_address = $my_ip
	novncproxy_base_url = http://controller:6080/vnc_auto.html
	
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
	password = 123456

# 完成安装 #

1、确定您的计算节点是否支持虚拟机的硬件加速：

	egrep -c '(vmx|svm)' /proc/cpuinfo

如果此命令返回值，则计算节点支持硬件加速，通常不需要其他配置。

2、启动Compute服务及其依赖项，并将它们配置为在系统引导时自动启动：

	# systemctl enable libvirtd.service openstack-nova-compute.service
	# systemctl start libvirtd.service openstack-nova-compute.service

 官网上的openstack-nova-consoleauth这个是什么鬼？？？？

--------------------- 

[参照](https://docs.openstack.org/install-guide/)

