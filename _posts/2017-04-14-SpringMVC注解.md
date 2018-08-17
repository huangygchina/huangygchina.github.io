---
layout:     post
title:      Spring MVC-注解
subtitle:   注解说明
date:       2017-04-14
author:     火花
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - java
    - Spring
---
**@Controller**  
标记类作为Controller。

**@RestController**  
1)如果只是使用@RestController注解Controller，则Controller中的方法无法返回jsp页面，配置的视图解析器InternalResourceViewResolver不起作用，返回的内容就是Return 里的内容。   
2)如果需要返回到指定页面，则需要用 @Controller配合视图解析器InternalResourceViewResolver才行。   
3)如果需要返回JSON，XML或自定义mediaType内容到页面，则需要在对应的方法上加上@ResponseBody注解。



