---
layout:     post
title:      Spring MVC
subtitle:   控制器和方法
date:       2017-04-3
author:     火花
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - java
    - Spring
---
# 控制器和方法 #
## HttpServletRequest & HttpServletResponse ##
- 可以在控制器的方法中使用 HttpServletRequest, HttpServletResponse 。  
- HttpServletRequest接口最常用的方法就是获得请求中的参数，这些参数一般是客户端表单中的数据。  
- 当服务器响应客户端的一个请求时，就要用到HttpServletResponse接口。设置响应的类型可以使用setContentType()方法。发送字符数据，可以使用getWriter()返回一个对象。

**HttpServletRequest的常用方法**

getAttributeNames():返回当前请求的所有属性的名字集合

getAttribute(String name):返回name指定的属性值

getCookies():返回客户端发送的Cookie

getsession():返回和客户端相关的session，如果没有给客户端分配session，则返回null

getsession(boolean create):返回和客户端相关的session，如果没有给客户端分配session，则创建一个session并返回

getParameter(String name):获取请求中的参数，该参数是由name指定的

getParameterValues(String name):返回请求中的参数值，该参数值是由name指定的

getCharacterEncoding():返回请求的字符编码方式

getContentLength():返回请求体的有效长度

getInputStream():获取请求的输入流中的数据

getMethod():获取发送请求的方式，如get、post

getParameterNames():获取请求中所有参数的名字

getProtocol():获取请求所使用的协议名称

getReader():获取请求体的数据流

getRemoteAddr():获取客户端的IP地址

getRemoteHost():获取客户端的名字

getServerName():返回接受请求的服务器的名字

getServerPath():获取请求的文件的路径


**HttpServletResponse的常用方法**  

addCookie(Cookie cookie)：将指定的Cookie加入到当前的响应中

addHeader(String name,String value)：将指定的名字和值加入到响应的头信息中

containsHeader(String name)：返回一个布尔值，判断响应的头部是否被设置

encodeURL(String url)：编码指定的URL

sendError(int sc)：使用指定状态码发送一个错误到客户端

sendRedirect(String location)：发送一个临时的响应到客户端

setDateHeader(String name,long date)：将给出的名字和日期设置响应的头部

setHeader(String name,String value)：将给出的名字和值设置响应的头部

setStatus(int sc)：给当前响应设置状态码

setContentType(String ContentType)：设置响应的MIME类型

**控制器中的重定向**  
使用前缀 "redirect:" ，该方法返回字符串，可以重定向到另一页面。
	public class RedirectExampleController {
	 
	    @RequestMapping(value = "/redirect", method = RequestMethod.GET)
	    public String authorInfo(Model model) {
	 
	       // Do somethong here
	 
	        return "redirect:/hello";
	    }
	}

**@RequestParam示**  
使用@RequestParam 注解将请求参数绑定到你的控制器方法参数。

	@Controller
	public class RequestParamExampleController {
	     
	    @RequestMapping("/user")
	    public String userInfo(Model model,
	            @RequestParam(value = "name", defaultValue = "Guest") String name) {
	 
	        model.addAttribute("name", name);
	 
	        if("admin".equals(name)) {
	            model.addAttribute("email", "admin@yiibai.com");
	        } else{
	            model.addAttribute("email", "Not set");
	        }
	        return "userInfo";
	    }
	  
	}

**@PathVariable示例**  
在Spring MVC中，可以使用@PathVariable注释将一个方法参数绑定到一个URI模板变量的值。

URL：http://localhost:8080/HelloSpringMVC/**web/fe/default/en/document/8108/spring-mvc-for-beginners**  
URI模板：**/web/fe/{sitePrefix}/{language}/document/{id}/{naturalText}**

	@Controller
	public class PathVariableExampleController {
	    /**
	     * @PathVariable Example:
	     *
	     */
	    @RequestMapping("/web/fe/{sitePrefix}/{language}/document/{id}/{naturalText}")
	    public String documentView(Model model,
	            @PathVariable(value = "sitePrefix") String sitePrefix,
	            @PathVariable(value = "language") String language,
	            @PathVariable(value = "id") Long id,
	            @PathVariable(value = "naturalText") String naturalText) {
	 
	        model.addAttribute("sitePrefix", sitePrefix);
	        model.addAttribute("language", language);
	        model.addAttribute("id", id);
	        model.addAttribute("naturalText", naturalText);
	 
	        String documentName = "Java tutorial for Beginners";
	        if(id == 8108) {
	            documentName = "Spring MVC for Beginners";
	        }
	 
	        model.addAttribute("documentName", documentName);
	 
	        return "documentView";
	    }
	}

**@ResponseBody示例**  
如果使用 @ResponseBody 注释到方法， spring 将尝试转换它的返回值，并自动写入到HTTP响应。在这种情况下，并不需要一个特定的视图。
	
	@Controller
	public class ResponseBodyExample1Controller {
	 
	    // Simple example, method returns String.
	    @RequestMapping(value = "/saveResult")
	    @ResponseBody
	    publicString authorInfo(Model model) {
	        return "saved";
	    }
	 
	     
	}