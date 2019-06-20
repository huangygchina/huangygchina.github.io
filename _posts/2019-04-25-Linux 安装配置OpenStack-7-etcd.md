---
layout:     post
title:      安装部署OpenStack-7
subtitle:   ectd
date:       2019-06-20
author:     火花
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - env
---
# 安装和配置组件 #

1、Install the package:

	#  yum install etcd

2、编辑/etc/etcd/etcd.conf文件:

	#[Member]
	ETCD_DATA_DIR="/var/lib/etcd/default.etcd"
	ETCD_LISTEN_PEER_URLS="http://10.0.0.11:2380"
	ETCD_LISTEN_CLIENT_URLS="http://10.0.0.11:2379"
	ETCD_NAME="controller"
	#[Clustering]
	ETCD_INITIAL_ADVERTISE_PEER_URLS="http://10.0.0.11:2380"
	ETCD_ADVERTISE_CLIENT_URLS="http://10.0.0.11:2379"
	ETCD_INITIAL_CLUSTER="controller=http://10.0.0.11:2380"
	ETCD_INITIAL_CLUSTER_TOKEN="etcd-cluster-01"
	ETCD_INITIAL_CLUSTER_STATE="new"

3、启动etcd服务并将其配置为在系统引导时启动

	# systemctl enable etcd
	# systemctl start etcd

--------------------- 

[参照](https://docs.openstack.org/install-guide/)

