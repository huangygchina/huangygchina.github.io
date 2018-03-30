---
layout:     post
title:      【React Native】的实现原理
subtitle:   实现原理
date:       2018-03-30
author:     火花
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - ReactNative
---
# 【React Native】的实现原理 #
## React Native背景 ##
**React**
>React 是由Facebook推出的一个JavaScript框架，主要用于前段开发。  
React 采用组件化方式简化Web开发  
React 可以高效的绘制界面  
React是采用JSX语法，一种JS语法糖，方便快速开发。

**Native**  
Native App
>Native App:指使用原生API开发App,比如iOS用OC语言开发

## React Native原理 ##

>React Native底层也会把React转换为原生API  
React Native底层解析原生API是分开实现的，iOS一套，安卓一套。

**React Native如何把React转化为原生API**  
>React Native会在一开始生成OC模块表，然后把这个模块表传入JS中，JS参照模块表，就能间接调用OC的代码。

## React Native是如何做到JS和OC交互 ##
iOS原生API有个JavaScriptCore框架，通过它就能实现JS和OC交互  
1.首先写好JSX代码（React框架就是使用JSX语法）  
2.把JSX代码解析成javaScript代码  
3.OC读取JS文件  
4.把javaScript代码读取出来，利用JavaScriptCore执行  
5.javaScript代码返回一个数组，数组中会描述OC对象，OC对象的属性，OC对象所需要执行的方法，这样就能让这个对象设置属性，并且调用方法。  
6.执行[RCTJSCExecutor injectJSONText] -> 往JS中插入OC模块表  
7.执行完JS代码，回调OC，调用OC中的组件  
8.完成UI渲染

## React Native加载JS源码流程(iOS) ##
1.加载远程服务器中JS代码

	[RCTJavaScriptLoader loadBundleAtURL]

2.开启异步加载JS代码  

	attemptAsynchronousLoadOfBundleAtURL(C函数)

3.让批量交接对象执行源代码

	[RCTBatchedBridge executeSourceCode:sourceCode]  

交给JS执行者（RCTJSCExecutor）执行源码)真正执行JS代码的是RCTJSCExecutor对象
 
	[RCTJSCExecutor executeApplicationScript]

5.发送JS代码执行完成通知  

	[postNotificationName:RCTJavaScriptDidLoadNotification]

6.RCTRootView监听到RCTJavaScriptDidLoadNotification通知  
7.创建RCTRootContentView  
8.获取RCTBridge中的RCTUIManager注册RCTRootView,并且记录RCTRootView，_viewRegistry  
9.通知JS运行App  

	[RCTRootView runApplication:bridge]

10.通过桥接对象让JS调用AppRegistry

	[RCTBridge enqueueJSCall:@"AppRegistry" method:@"runApplication" args:@[moduleName, appParameters]completion:NULL]

11.通过批量桥架让JS执行AppRegistry方法 

	[RCTBatchedBridge _actuallyInvokeAndProcessModule:module method:method arguments:args queue:RCTJSThread] 
 
12.让JS执行者调用JS代码 

	[RCTJSCExecutor _executeJSCall:bridgeMethod arguments:@[module, method, args] unwrapResult:unwrapResult callback:onComplete] 
 
13.执行完JS代码，就能获取执行JS结果，是一个数组，OC需要做的事情都会保存到这个数组中  
14.处理执行完JS代码返回的结果对象

	[RCTBatchedBridge _processResponse:json error:error]  

15.处理JS返回的数据，JS会返回的方法调用数组：按顺序描述需要调用哪个对象的方法，一组调用包含（module，method，arguments）

	[RCTBatchedBridge handleBuffer]  

16.遍历数组，依次执行原生方法

	[self callNativeModule:[moduleIDs[index] integerValue]method:[methodIDs[index] integerValue]params:paramsArrays[index]]

## React NativeUI控件渲染流程(iOS) ##
1.通知JS运行App

	[RCTRootView runApplication:bridge]

2.处理执行完JS代码(runApplication)返回的相应，包含需要添加多少子控件的信息。

	[RCTBatchedBridge _processResponse:json error:error]

3.批量桥架对象调用批量处理完成方法

	[RCTBatchedBridge batchDidComplete]

4.RCTUIManager调用批量处理完成的方法，就会开始去加载rootView的子控件。

	[RCTUIManager batchDidComplete]

5.通过JS执行OC代码，让UI管理者创建子控件View 

	[RCTUIManager createView:viewName:rootTag:props]

	RCT_EXPORT_METHOD(createView:(nonnull NSNumber *)reactTag
             viewName:(NSString *)viewName
             rootTag:(__unused NSNumber *)rootTag
             props:(NSDictionary *)props) 

>注意每创建一个UIView,就会创建一个RCTShadowView，与UIView一一对应  
RCTShadowView:保存对应UIView的布局和子控件,管理UIView的加载

6.布局RCTRootView和增加子控件

	[RCTUIManager _layoutAndMount]

7.给RCTRootView对应的RCTRootShadowView设置子控件

	[RCTUIManager setChildren:reactTags:]

注意：此方法也是JS调用OC方法  
8.遍历子控件数组，给RCTRootShadowView插入所有子控件

	[RCTRootShadowView insertReactSubview:view atIndex:index++]

9.处理保存在RCTShadowView中属性，就会去布局RCTShadowView对应UIView的所有子控件

	[RCTShadowView processUpdatedProperties:parentProperties:]

10.给原生View添加子控件 

	[RCTView didUpdateReactSubviews]

11.完成UI渲染

## React Native事件处理流程(iOS) ##
1.在创建RCTRootContentView的时候，内部会创建RCTTouchHandler
>RCTTouchHandler:继承UIGestureRecognizer，也就是它就是一个手势
它会作为RCTRootContentView的手势，这样点击RCTRootContentView，就会触发RCTTouchHandler
RCTTouchHandler:内部实现了touchBegin等触摸方法，用来处理触摸事件

2.在创建RCTTouchHandler的时候，内部会创建RCTEventDispatcher  
>RCTEventDispatcher:用来把事件处理传递给JS的方法处理，也就是当UI界面产生事件，就会执行JS的代码处理。

3.通过RCTRootContentView截获点击事件
>产生事件就会去触犯RCTRootContentView中的RCTTouchHandler对象。

4.当产生事件的时候,会执行

	[RCTTouchHandler touchBegin]

5.RCTTouchHandler的touch方法，会执行

	[RCTTouchHandler _updateAndDispatchTouches:eventName:]

内部会创建RCTTouchEvent事件对象

6.让事件分发对象调用发送事件对象,内部会把事件保存到_eventQueue（事件队列中）

	[RCTEventDispatcher sendEvent:event]

7.让事件分发对象冲刷事件队列，就是获取事件队列中所有事件执行

	[RCTEventDispatcher flushEventsQueue]

8.遍历事件队列，一个一个分发事件分发事件的本质：就是去执行JS的代码，相应事件。

	[RCTEventDispatcher dispatchEvent:event]

9.让桥架对象调用JS处理事件本质：就是产生事件调用JS代码

	[RCTBatchedBridge enqueueJSCall:[[event class] moduleDotMethod] args:[event arguments]];

10.这样就能完成把UI事件交给JS代码相应






