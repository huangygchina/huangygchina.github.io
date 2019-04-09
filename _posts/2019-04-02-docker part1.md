---
layout:     post
title:      Docker install
subtitle:   Get Docker CE for CentOS
date:       2019-04-01
author:     火花
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - env
---
## Uninstall old versions ##
	$ sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine

## Install Docker CE ##
SET UP THE REPOSITORY

	$ sudo yum install -y yum-utils \
	  device-mapper-persistent-data \
	  lvm2

	$ sudo yum-config-manager \
	    --add-repo \
	    https://download.docker.com/linux/centos/docker-ce.repo

	$ sudo yum-config-manager --enable docker-ce-nightly

	$ sudo yum-config-manager --enable docker-ce-test

INSTALL DOCKER CE

	$ sudo yum install docker-ce docker-ce-cli containerd.io(默认安装最新版本)

	$ yum list docker-ce --showduplicates | sort -r

	$ sudo yum install docker-ce-<VERSION_STRING> docker-ce-cli-<VERSION_STRING> containerd.io（安装指定版本）

Start Docker

	$ sudo systemctl start docker

	$ sudo docker run hello-world（运行sample镜像）

[参照](https://docs.docker.com/install/linux/docker-ce/centos/)

