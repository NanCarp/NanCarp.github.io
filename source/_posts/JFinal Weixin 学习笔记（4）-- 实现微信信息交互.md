---
title: JFinal Weixin 学习笔记（4）-- 实现微信信息交互
date: 2017-05-18 13:42:43
tags: JFinal 微信
---
----
#### 项目结构  

![项目结构](http://upload-images.jianshu.io/upload_images/5343805-cbd15bd39f5857bf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)    

####  WeixinMsgController 
 `WixinMsgController`  继承自 `MsgControllerAdapter`，后者对 `MsgController ` 的部分方法提供了默认实现，以便开发者不去关注 `MsgController` 中不需要处理的抽象方法，节省代码量。  
`getApiConfig()` 方法从 `a_little_config.txt` 文件中读取配置信息。  
`processInTextMsg()` 方法接受用户在微信中输入的文本信息，并对此作出相关回应。
现在我们实现最简单的功能，直接返回用输入的文本信息。  



	package com.nancarp.controller;
	
	import com.jfinal.weixin.sdk.api.ApiConfig;
	import com.jfinal.weixin.sdk.jfinal.MsgControllerAdapter;
	import com.jfinal.weixin.sdk.msg.in.InTextMsg;
	import com.jfinal.weixin.sdk.msg.in.event.InFollowEvent;
	import com.jfinal.weixin.sdk.msg.in.event.InMenuEvent;
	import com.jfinal.weixin.sdk.msg.out.OutTextMsg;
	import com.nancarp.utils.WeixinUtil;
	
	public class WeixinMsgController extends MsgControllerAdapter {
		
		/**
		 * 如果要支持多公众账号，只需要在此返回各个公众号对应的 ApiConfig 对象即可 可以通过在请求 url 中挂参数来动态从数据库中获取
		 * ApiConfig 属性值
		 */
		@Override
		public ApiConfig getApiConfig() {
			return WeixinUtil.getApiConfig();
		}
	
		/* 
		 * 接收文本消息事件
		 */
		@Override
		protected void processInTextMsg(InTextMsg inTextMsg) {
			// 其它文本消息直接返回原值
			OutTextMsg outMsg = new OutTextMsg(inTextMsg);
			outMsg.setContent("\t文本消息已成功接收，内容为： " + inTextMsg.getContent());
			render(outMsg);
		}
		
		@Override
		protected void processInFollowEvent(InFollowEvent inFollowEvent) {
			// TODO Auto-generated method stub
			
		}
	
		@Override
		protected void processInMenuEvent(InMenuEvent inMenuEvent) {
			// TODO Auto-generated method stub
			
		}
	
	}
  
#### WeixinUtil  
`getApiConfig()` 方法，获取 `token`、`appId`、`appSecret` 信息。
  
	package com.nancarp.utils;
	
	import com.jfinal.kit.PropKit;
	import com.jfinal.weixin.sdk.api.ApiConfig;
	
	/**
	 * 
	 * 公众平台通用接口工具类
	 */
	public class WeixinUtil {
		/**
		 * 获取配置
		 */
		public static ApiConfig getApiConfig() {
			ApiConfig ac = new ApiConfig();
	
			// 配置微信 API 相关常量
			ac.setToken(PropKit.get("token"));
			ac.setAppId(PropKit.get("appId"));
			ac.setAppSecret(PropKit.get("appSecret"));
	
			/**
			 *  是否对消息进行加密，对应于微信平台的消息加解密方式：
			 *  1：true进行加密且必须配置 encodingAesKey
			 *  2：false采用明文模式，同时也支持混合模式
			 */
			ac.setEncryptMessage(PropKit.getBoolean("encryptMessage", false));
			ac.setEncodingAesKey(PropKit.get("encodingAesKey", "setting it in config file"));
			return ac;
		}
	}
  
#### a_little_config.txt    
`token`、`appId`、`appSecret`  参数要与先前申请的测试号保持一致。[详见《微信开发前的准备》](http://www.jianshu.com/p/e47edf0a49bf)  
  
	# 微信服务器回调所用的 token
	token=weixin
	
	# 测试用的账号
	appId=wxde758064398fa9bf
	appSecret=89622f356fc2bca96ec647d55929afa5
  
#### WeixinConfig 配置  


	@Override
	public void configConstant(Constants me) {
		// 加载配置文件
		PropKit.use("a_little_config.txt");
		// 读取配置文件，判断是否为开发模式，默认 false
		me.setDevMode(PropKit.getBoolean("devMode", false));
		// ApiConfigKit 设为开发模式可以在开发阶段输出请求交互的 xml 与 json 数据
		ApiConfigKit.setDevMode(me.getDevMode());
		// 默认使用的jackson，下面示例是切换到fastJson
		//me.setJsonFactory(new FastJsonFactory());
	}
  
	@Override
	public void configRoute(Routes me) {
		// HelloWorld 路由
		me.add("/hello",HelloWorldController.class);
		// 接收用户消息和发送消息
		me.add("/msg", WeixinMsgController.class);
	}

  
#### 运行项目  
1. 启动 Tomcat  
2. 启动 ngrok  
参考 [让微信公众平台通过80端口访问本机](http://www.cnblogs.com/zyw-205520/p/4733062.html)  
  
#### 微信客户端的操作
关注测试账号，输入内容“1”，测试号回复“文本消息已成功接收，内容为： 1”，大功告成。

![微信回复信息](http://upload-images.jianshu.io/upload_images/5343805-0ebb11e9f5a49a6d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  
---
[源码地址](http://git.oschina.net/NanCarp/jfinal-weixin-nancarp)  
[JFinal Weixin 学习笔记（5）-- 微信页面跳转](http://www.jianshu.com/p/e7ecdc6a85f3)  
[JFinal Weixin 学习笔记（1）-- 目录](http://www.jianshu.com/p/0ea20e77cf29)  
  
#### 参考文章
[微信公众号开发之如何实现消息交互](http://www.jianshu.com/p/6e8caa616577)  
[JFinal Weixin 极速开发 SDK](http://www.jfinal.com/project/2)  
[JFinal weixin中的WeixinConfig配置](http://git.oschina.net/jfinal/jfinal-weixin/wikis/JFinal-weixin%E4%B8%AD%E7%9A%84WeixinConfig%E9%85%8D%E7%BD%AE)