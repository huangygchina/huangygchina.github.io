---
layout:     post
title:      安装部署OpenStack-3
subtitle:   OpenStack包
date:       2019-06-19
author:     火花
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - env
---
**注意：OpenStack包的设置需要在所有节点上完成：控制器，计算和块存储节点。**

安装stein版本

	# yum install centos-release-openstack-stein

1、升级所有节点上的包：

	# yum upgrade

2、安装OpenStack客户端：

	# yum install python-openstackclient

3、安装 openstack-selinux软件包以自动管理OpenStack服务的安全策略：

	# yum install openstack-selinux

--------------------- 

[参照](https://docs.openstack.org/install-guide/)

