---
title: JFinal Weixin 学习笔记（5）-- 微信页面跳转
date: 2017-05-18 16:42:43
tags: JFinal 微信
---

----
#### 项目结构  

![项目结构](http://upload-images.jianshu.io/upload_images/5343805-568813dea75afbc0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
  
#### WeixinApiController  
`WeixinApiController` 继承 `ApiController` ，所有使用 Api 的 controller 都需要继承此类。
目前没有使用 jfinal-weixin 接口，只是简单地跳转到主页。
  
	package com.nancarp.controller;
	
	import com.jfinal.weixin.sdk.api.ApiConfig;
	import com.jfinal.weixin.sdk.jfinal.ApiController;
	import com.nancarp.utils.WeixinUtil;
	
	public class WeixinApiController extends ApiController {
	
		/**
		 * 如果要支持多公众账号，只需要在此返回各个公众号对应的 ApiConfig 对象即可 可以通过在请求 url 中挂参数来动态从数据库中获取
		 * ApiConfig 属性值
		 */
		@Override
		public ApiConfig getApiConfig() {
			return WeixinUtil.getApiConfig();
		}
		
		// 主页
		public void index(){
			render("index.html");
		}
	
	}
  
#### Constants 
`Constants` 类保存项目中用到的常量
    
	package com.nancarp.utils;
	
	import com.jfinal.kit.PropKit;
	
	public class Constants {
		// 获取域名
	    public static String host = PropKit.get("host");
	    
	}
  
#### WeixinConfig 增加路由

  
	@Override
	public void configRoute(Routes me) {
		// HelloWorld 路由
		me.add("/hello",HelloWorldController.class);
		// 接收用户消息和发送消息
		me.add("/msg", WeixinMsgController.class);
		// 操作微信接口
		me.add("/api", WeixinApiController.class,"/api");
	}
  
#### 修改 WeixinMsgController  
`url`  要以 `http://` 或 `https://` 开头，否则微信返回的信息只是单纯的文本。
  
	@Override
	protected void processInTextMsg(InTextMsg inTextMsg) {
		// 文本内容
		String msgContent = inTextMsg.getContent().trim();
		// 回复主页链接
		if ("1".equals(msgContent) || "主页".equals(msgContent)){
			OutTextMsg outMsg = new OutTextMsg(inTextMsg);
			String url = Constants.host + "/api/index";
			String urlStr = "<a href=\""+url+"\">点击跳转主页</a>";
			outMsg.setContent(urlStr);
			render(outMsg);
		} else {
			// 其它文本消息直接返回原值
			OutTextMsg outMsg = new OutTextMsg(inTextMsg);
			outMsg.setContent("\t文本消息已成功接收，内容为： " + inTextMsg.getContent());
			render(outMsg);
		}
	}
  
#### index.html  
  
	<!DOCTYPE html>
	<html>
	<head>
	<meta charset="UTF-8">
	<title>主页</title>
	</head>
	<body>
		<h1>欢迎来到主页</h1>
	</body>
	</html>
  
#### 启动项目  
不出意外，将会得到如下结果：
![回复消息](http://upload-images.jianshu.io/upload_images/5343805-36366efdc5f74778.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![主页](http://upload-images.jianshu.io/upload_images/5343805-c579c9fbffedc9e7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  
----
[源码地址](http://git.oschina.net/NanCarp/jfinal-weixin-nancarp)  
[JFinal Weixin 学习笔记（6）-- 获取微信接口调用凭据](http://www.jianshu.com/p/cfb13d02465f)
[JFinal Weixin 学习笔记（1）-- 目录](http://www.jianshu.com/p/0ea20e77cf29)  
  
#### 参考文章
[微信公众号开发之如何实现消息交互](http://www.jianshu.com/p/6e8caa616577)  
[JFinal Weixin 极速开发 SDK](http://www.jfinal.com/project/2)