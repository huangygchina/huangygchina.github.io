---
layout:     post
title:      Java反射机制
subtitle:   Java反射机制应用
date:       2017-4-24
author:     火花
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - java
---
## 反射的源头（Class类） ##
Class类的实例表示正在运行的Java应用程序中的类和接口。

如果要想使用Class类进行操作，就必须首先产生Class类这个对象，一共有三种方法：

（1）Object类中提供了一个返回Class类的方法，定义如下：  

	public final Class<?getClass() 
（2）利用“类.class”取得。  
（3）利用Class类的Static方法取得。  

	public static Class<?> forName(String className)  
	                        throws ClassNotFoundException

class类实例化对象：  

	public T newInstance()  
	              throws InstantiationException,  
	                     IllegalAccessException  

## 利用反射操作构造方法   ##
在Class类中有两个方法可以获取类中的构造方法，分别是：  
获取类中所有的构造方法  

	public Constructor<?>[] getConstructors()  
	                                 throws SecurityException

获取类中指定的构造方法：


	public Constructor<T> getConstructor(Class<?>... parameterTypes)  
	                              throws NoSuchMethodException,  
	                                     SecurityException 

## 利用反射调用类中的方法 ##
获取类中的方法可以分为两大类，每个大类中又可以分为两小类，分别是：  
**获取包括父类集成而来的方法：**  
获取全部方法：

	public Method[] getMethods()  
	                    throws SecurityException  

获取指定方法：  

	public Method getMethod(String name,Class<?>... parameterTypes)  
	                                   throws NoSuchMethodException,  
	                                    SecurityException 

**获取本类中定义的方法：**  
获取全部方法：

	public Method[] getDeclaredMethods()  
	                            throws SecurityException

获取指定方法：

	public Method getDeclaredMethod(String name,  
	                                Class<?>... parameterTypes)  
	                         throws NoSuchMethodException,  
	                                SecurityException  

**需要用到Method类中的如下几种方法：**  
**getModifiers()：**以整数形式返回此 Method 对象所表示方法的 Java 语言修饰符。  
**getReturnType()：**返回一个 Class 对象，该对象描述了此Method 对象所表示的方法的正式返回类型。  
**getName()：**以 String 形式返回此 Method 对象表示的方法名称。  
**getParameterTypes()：**按照声明顺序返回 Class 对象的数组，这些对象描述了此Method 对象所表示的方法的形参类型。  
**getExceptionTypes()：**返回 Class 对象的数组，这些对象描述了声明将此Method 对象表示的底层方法抛出的异常类型。



[参照代码](https://github.com/huangygchina/Reflect4Java.git)

