---
title: JFinal Weixin 学习笔记（3）-- Eclipse 搭建微信项目框架
date: 2017-05-17 15:13:43
tags: JFinal 微信
---

----
#### 新建 Eclipse 项目  

新建 Dynamic Web Project  
项目名： jfinal-weixin-nancarp   
服务器： Tomcat v8.0  

![](http://upload-images.jianshu.io/upload_images/5343805-dbcb971fe77773e9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

#### 导入 jar 包  

项目必需以下 jar 包，或者更高版本    

    // jfinal 核心包
    --jfinal-2.2.jar
    
    // jfinal 微信开发 jar 包
    --jfinal-weixin-1.8-bin-with-src.jar
    
    // 可选Json处理包，必选其一
    --fastjson-1.2.6.jar
    //或者
    --jackson-databind-2.4.3.jar
    ----jackson-annotations-2.4.0.jar
    ----jackson-core-2.4.3.jar

所需 jar 包可以在 JFinal 社区下载：http://www.jfinal.com/  

#### 搭建 JFinal 框架    
项目结构  ：

![](http://upload-images.jianshu.io/upload_images/5343805-a4b5f842c6e7d478.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
  
**WeixinConfig.java：**  

	package com.nancarp.config;
	
	import com.jfinal.config.Constants;
	import com.jfinal.config.Handlers;
	import com.jfinal.config.Interceptors;
	import com.jfinal.config.JFinalConfig;
	import com.jfinal.config.Plugins;
	import com.jfinal.config.Routes;
	import com.jfinal.kit.PropKit;
	import com.jfinal.template.Engine;
	import com.nancarp.controller.HelloWorldController;
	
	public class WeixinConfig extends JFinalConfig{
	
		@Override
		public void configConstant(Constants me) {
			// 加载配置文件
			PropKit.use("a_little_config.txt");
			// 读取配置文件，判断是否为开发模式，默认 false
			me.setDevMode(PropKit.getBoolean("devMode", false));
		}
	
		@Override
		public void configRoute(Routes me) {
			// HelloWorld 路由
			me.add("/hello",HelloWorldController.class);
		}
	
		@Override
		public void configEngine(Engine me) {
			
		}
	
		@Override
		public void configPlugin(Plugins me) {
			
		}
	
		@Override
		public void configInterceptor(Interceptors me) {
			
		}
	
		@Override
		public void configHandler(Handlers me) {
			
		}
	
	}
  
**HelloWorldController.java：**  

	package com.nancarp.controller;
	
	import com.jfinal.core.Controller;
	
	public class HelloWorldController extends Controller {
		public void index() {
			renderText("Hello World!");
		}
	}
  
**a_little_config.txt：**  

	# 数据库相关设置
	jdbcUrl=jdbc:mysql://localhost/__数据库名__?characterEncoding=utf8&zeroDateTimeBehavior=convertToNull
	user=_数据库账号_
	password=_数据库密码_
	
	# 开发模式
	devMode = true
	
	# 微信服务器回调所用的 token
	token=weixin
	
	# 测试用的账号
	appId=wxde758064398fa9bf
	appSecret=89622f356fc2bca96ec647d55929afa5
	#webAppId=___web appid___
	#webAppSecret=_________web appSecret____
	
	#是否对消息进行加密，是否对消息进行加密，对应于微信平台的消息加解密方式，false支持明文模式及兼容模式，true支持安全模式及兼容模式
	encryptMessage=false
	encodingAesKey=5C8csBd4cG4c9039OKDnNaZmYlkAzXV31piXQFRZKHC
	
	#####业务逻辑中的华丽的分割线#########
	#授权域名
	domain=http://********.com
	subscribe_rul=http://mp.weixin.qq.com/s?__biz=MzA4MDA2OTA0Mg==&mid=208184833&idx=1&sn=d9e615e45902c3c72db6c24b65c4af3e#rd
	host=http://*****.com
  
**web.xml：**  

	<?xml version="1.0" encoding="UTF-8"?>
	<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://xmlns.jcp.org/xml/ns/javaee" xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd" id="WebApp_ID" version="3.1">
	  <filter>
			<filter-name>jfinal</filter-name>
			<filter-class>com.jfinal.core.JFinalFilter</filter-class>
			<init-param>
				<param-name>configClass</param-name>
				<param-value>com.nancarp.config.WeixinConfig</param-value>
			</init-param>
		</filter>
		
		<filter-mapping>
			<filter-name>jfinal</filter-name>
			<url-pattern>/*</url-pattern>
		</filter-mapping>
	</web-app>  
  
#### 项目部署到 Tomcat 上    


![](http://upload-images.jianshu.io/upload_images/5343805-cbee124232eabea5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
  
修改 server.xml 文件    

    <Connector connectionTimeout="20000" port="8080" protocol="HTTP/1.1" redirectPort="8443"/>
    // 修改端口号为 80 ，因为微信只支持 80 或 443 端口
    <Connector connectionTimeout="20000" port="80" protocol="HTTP/1.1" redirectPort="8443"/>


    <Context docBase="jfinal-weixin-nancarp" path="/jfinal-weixin-nancarp" reloadable="true" source="org.eclipse.jst.jee.server:jfinal-weixin-nancarp"/>
    // path 修改为"/"，这样 url 就不需要带上项目名
    <Context docBase="jfinal-weixin-nancarp" path="/" reloadable="true" source="org.eclipse.jst.jee.server:jfinal-weixin-nancarp"/>
  
#### 运行项目
启动 tomcat，在浏览器输入 [localhost/hello](http://localhost/hello)，回车。  
看到熟悉的 "Hello World" 就说明框架搭建成功。
  
---
[源码地址](http://git.oschina.net/NanCarp/jfinal-weixin-nancarp)  
[JFinal Weixin 学习笔记（4）-- 实现微信信息交互](http://www.jianshu.com/p/191abc4da94d)  
[JFinal Weixin 学习笔记（1）-- 目录](http://www.jianshu.com/p/0ea20e77cf29)





#### 参考文章
[JFinal-weixin Jar包依赖](http://git.oschina.net/jfinal/jfinal-weixin/wikis/JFinal-weixin-1.6-Jar%E4%BE%9D%E8%B5%96)