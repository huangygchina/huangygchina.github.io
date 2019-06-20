---
layout:     post
title:      安装部署OpenStack-8
subtitle:   keystone
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

	MariaDB [(none)]> CREATE DATABASE keystone;

3、授予对keystone数据库的适当访问权限：

	# MariaDB [(none)]> GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'localhost' \
	IDENTIFIED BY 'KEYSTONE_DBPASS';
	MariaDB [(none)]> GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'%' \
	IDENTIFIED BY 'KEYSTONE_DBPASS';

# 安装和配置组件 #

1、运行以下命令以安装软件包：

	# yum install openstack-keystone httpd mod_wsgi

**这个地方有个坑：**
httpd mod_wsgi需要在单独安装一次，否则httpd.service无法启动。

2、编辑/etc/keystone/keystone.conf文件并完成以下操作：

	[database]
	# ...
	connection = mysql+pymysql://keystone:KEYSTONE_DBPASS@controller/keystone
	
	[token]
	# ...
	provider = fernet

3、填充Identity服务数据库：

	# su -s /bin/sh -c "keystone-manage db_sync" keystone

4、初始化Fernet密钥存储库：

	# keystone-manage fernet_setup --keystone-user keystone --keystone-group keystone
	# keystone-manage credential_setup --keystone-user keystone --keystone-group keystone

5、引导身份服务：

	# keystone-manage bootstrap --bootstrap-password admin_123456 \
	  --bootstrap-admin-url http://controller:5000/v3/ \
	  --bootstrap-internal-url http://controller:5000/v3/ \
	  --bootstrap-public-url http://controller:5000/v3/ \
	  --bootstrap-region-id RegionOne

# 配置Apache HTTP服务器 #

1、编辑/etc/httpd/conf/httpd.conf文件

	ServerName controller

2、创建/usr/share/keystone/wsgi-keystone.conf文件的链接

	ln -s /usr/share/keystone/wsgi-keystone.conf /etc/httpd/conf.d/

# 完成安装 #

1、启动Apache HTTP服务并将其配置为在系统引导时启动：

	# systemctl enable httpd.service
	# systemctl start httpd.service

2、配置管理帐户

	$ export OS_USERNAME=admin
	$ export OS_PASSWORD=ADMIN_PASS
	$ export OS_PROJECT_NAME=admin
	$ export OS_USER_DOMAIN_NAME=Default
	$ export OS_PROJECT_DOMAIN_NAME=Default
	$ export OS_AUTH_URL=http://controller:5000/v3
	$ export OS_IDENTITY_API_VERSION=3

# 创建域，项目，用户和角色 #

1、创建新域的正式方法

	openstack domain create --description "An Example Domain" example

2、创建service 项目

	openstack project create --domain default \
	  --description "Service Project" service

3、常规（非管理员）任务应该使用非特权项目和用户。例如，本指南创建myproject项目和myuser 用户。

	openstack project create --domain default \
	  --description "Demo Project" myproject

4、创建myuser用户：

	openstack user create --domain default \
	  --password-prompt myuser

5、创建myrole角色：

	openstack role create myrole

6、将myrole角色添加到myproject项目和myuser用户

	openstack role add --project myproject --user myuser myrole

# 验证操作 #

1、取消设置临时 变量OS_AUTH_URL和OS_PASSWORD环境变量：

	unset OS_AUTH_URL OS_PASSWORD

2、作为admin用户，请求身份验证令牌：

	openstack --os-auth-url http://controller:5000/v3 \
	  --os-project-domain-name Default --os-user-domain-name Default \
	  --os-project-name admin --os-username admin token issue

3、作为myuser上一节中创建的用户，请求身份验证令牌：

	$ openstack --os-auth-url http://controller:5000/v3 \
	  --os-project-domain-name Default --os-user-domain-name Default \
	  --os-project-name myproject --os-username myuser token issue

# 创建OpenStack客户端环境脚本 #

## 创建脚本 ##

1、创建和编辑admin-openrc文件并添加以下内容

	export OS_PROJECT_DOMAIN_NAME=Default
	export OS_USER_DOMAIN_NAME=Default
	export OS_PROJECT_NAME=admin
	export OS_USERNAME=admin
	export OS_PASSWORD=admin_123456
	export OS_AUTH_URL=http://controller:5000/v3
	export OS_IDENTITY_API_VERSION=3
	export OS_IMAGE_API_VERSION=2

2、创建和编辑demo-openrc文件并添加以下内容

	export OS_PROJECT_DOMAIN_NAME=Default
	export OS_USER_DOMAIN_NAME=Default
	export OS_PROJECT_NAME=myproject
	export OS_USERNAME=myuser
	export OS_PASSWORD=123456
	export OS_AUTH_URL=http://controller:5000/v3
	export OS_IDENTITY_API_VERSION=3
	export OS_IMAGE_API_VERSION=2
--------------------- 

[参照](https://docs.openstack.org/install-guide/)

