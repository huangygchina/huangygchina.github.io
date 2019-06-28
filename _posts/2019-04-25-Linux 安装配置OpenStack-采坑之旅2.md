---
layout:     post
title:      安装部署OpenStack-采坑之旅2
subtitle:   nova-status upgrade check失败
date:       2019-06-20
author:     火花
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - env
---
# 现象 #

执行nova-status upgrade check命令失败。

# 原因 #
官方的bug。

# 对策 #
vi /etc/httpd/conf.d/00-placement-api.conf
<virtualhost *:8778=""> 中增加下面内容
<Directory /usr/bin>
<IfVersion >= 2.4>
    Require all granted
</IfVersion>
<IfVersion < 2.4>
    Order allow,deny
    Allow from all
</IfVersion>
</Directory>

重启服务。

--------------------- 

[参照](https://ask.openstack.org/en/question/122313/on-stein-nova-status-upgrade-check-check-failed/)