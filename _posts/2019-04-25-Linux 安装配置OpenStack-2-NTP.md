---
layout:     post
title:      安装部署OpenStack-2
subtitle:   NTP服务
date:       2019-06-19
author:     火花
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - env
---
# 控制节点 #
**1、Install the packages:**

	# yum install chrony
	
**2、edit /etc/chrony.conf**

	server NTP_SERVER iburst

**3、To enable other nodes to connect to the chrony daemon on the controller node, add this key to the same chrony.conf file**

	allow 10.0.0.0/24

**4、Restart the NTP service:**

	# systemctl enable chronyd.service
	# systemctl start chronyd.service

# 其他节点 #
**1、Install the packages:**

	# yum install chrony
	
**2、edit /etc/chrony.conf**

	server NTP_SERVER iburst

**3、Restart the NTP service:**

	# systemctl enable chronyd.service
	# systemctl start chronyd.service

# 验证操作 #

	# chronyc sources

--------------------- 

[参照](https://docs.openstack.org/install-guide/)

