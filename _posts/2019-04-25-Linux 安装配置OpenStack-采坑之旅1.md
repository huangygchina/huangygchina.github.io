---
layout:     post
title:      安装部署OpenStack-采坑之旅
subtitle:   启动httpd服务失败
date:       2019-06-20
author:     火花
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - env
---
# 现象 #

按照官网，安装配置keystone服务后，启动httpd.service服务失败。

报错：语法错误。

	6月 20 15:13:54 localhost.localdomain httpd[3673]: AH00526: Syntax error on ...
	6月 20 15:13:54 localhost.localdomain httpd[3673]: Invalid command 'WSGIDaem...

虽然执行了命令：

	yum install openstack-keystone httpd mod_wsgi

但是不知道为何mod_wsgi未被安装。导致以上错误。

# 原因 #
未能成功安装mod_wsgi

# 对策 #
重新执行：

	yum install httpd mod_wsgi

安装完毕后，启动httpd.service正常。







--------------------- 

[参照](https://docs.openstack.org/install-guide/)

