---
layout:     post
title:      Spring MVC-视图解析器
subtitle:   视图解析器(ViewResolver)
date:       2017-04-9
author:     火花
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - java
    - Spring
    - jpa
---
## 1、Spring-data-jpa的基本介绍； ##
**Spring-data-jpa的基本介绍：**  
JPA诞生的缘由是为了整合第三方ORM框架，建立一种标准的方式，百度百科说是JDK为了实现ORM的天下归一，目前也是在按照这个方向发展，但是还没能完全实现。在ORM框架中，Hibernate是一支很大的部队，使用很广泛，也很方便，能力也很强，同时Hibernate也是和JPA整合的比较良好，我们可以认为JPA是标准，事实上也是，JPA几乎都是接口，实现都是Hibernate在做，宏观上面看，在JPA的统一之下Hibernate很良好的运行。

在使用持久化工具的时候，一般都有一个对象来操作数据库，在原生的Hibernate中叫做Session，在JPA中叫做EntityManager，在MyBatis中叫做SqlSession，通过这个对象来操作数据库。使用Spring-data-jpa进行开发的过程中，常用的功能，几乎不需要写一条sql语句，至少企业级应用基本上可以不用写任何一条sql，当然spring-data-jpa也提供自己写sql的方式，这个就看个人怎么选择。

