---
layout:     post
title:      安装部署OpenStack-6
subtitle:   Memecached
date:       2019-06-20
author:     火花
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - env
---
# 安装和配置组件 #

1、Install the package:

	# yum install memcached python-memcached

2、编辑/etc/sysconfig/memcached文件:

	OPTIONS="-l 127.0.0.1,::1,controller"

3、启动Memcached服务并将其配置为在系统引导时启动

	# systemctl enable memcached.service
	# systemctl start memcached.service

--------------------- 

[参照](https://docs.openstack.org/install-guide/)

