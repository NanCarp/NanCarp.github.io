---
title: JFinal Weixin 学习笔记（6）-- 获取微信接口调用凭据
date: 2017-05-20 09:06:43
tags: JFinal 微信
---

----
#### 利用 AccessTokenApi 获取 access_token  
  
`access_token` 是公众号的全局唯一票据，公众号调用各接口时都需使用 `access_token`。  
`jfinal-wein` 提供了 `AccessTokenApi` 接口，其中:   
`getAccessTokenStr()` 方法直接获取 `access_token` 字符串，方便使用；  
`getAccessToken()` 方法从缓存中获取 `access token`，如果未取到或者 `access token` 不可用则先更新再获取。  
  
#### 修改 WeixinApiController.java  

	// 获取 access token 字符串
	public void getAccessTokenStr() {
		String accessTokenStr = AccessTokenApi.getAccessTokenStr();
		renderText(accessTokenStr);
	}

	// 获取 access token
	public void getAccessToken() {
		AccessToken accessToken = AccessTokenApi.getAccessToken();
		renderText(accessToken.getAccessToken());
	}
  
#### 修改 index.html   
  
	<body>
		<h1>欢迎来到主页</h1>
		<h3>获取接口调用凭据</h3>
		点击<a href='/api/getAccessTokenStr'>【获取 access token 字符串】</a><br>
		点击<a href='/api/getAccessToken'>【获取 access token】</a><br>
	</body>
	
#### 运行项目  
打开主页面    
  
![主页面](http://upload-images.jianshu.io/upload_images/5343805-f41c64286c149d93.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  
点击页面链接
微信服务器会返回 `access_token`，如图：  

![微信服务器返回的 access_token](http://upload-images.jianshu.io/upload_images/5343805-457cd62b7f248b14.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
    
#### 获取微信服务器 IP 地址  
如果公众号基于安全等考虑，需要获知微信服务器的 IP 地址列表，以便进行相关限制。   
可以通过 `CallbackIpApi` 接口的 `getCallbackIp()` 方法获得微信服务器 IP 地址列表。   
同上，修改 `WeixinApiController.java` 与 `index.html` 文件：    

	// 获取微信服务器IP地址
	public void getCallbackIp() {
		ApiResult apiResult = CallbackIpApi.getCallbackIp();
		renderText(apiResult.toString());
	}
	
	// index.html 页面添加链接
	点击<a href='/api/getCallbackIp'>【获取微信服务器 IP 地址】</a><br>
    
运行结果：     
  
    // 微信服务器 IP 地址， IP 地址很多，多余结果已略去
    {"ip_list":["101.226.62.77","101.226.62.78""]}
    
----
[源码地址](http://git.oschina.net/NanCarp/jfinal-weixin-nancarp)  
[JFinal Weixin 学习笔记（7）-- 微信用户管理](http://www.jianshu.com/p/061ad5a23e1f)  
[JFinal Weixin 学习笔记（1）-- 目录](http://www.jianshu.com/p/0ea20e77cf29)  
  
#### 参考文章
[jfinal-weixin-wiki 获取接口调用凭据](http://git.oschina.net/jfinal/jfinal-weixin/wikis/%E8%8E%B7%E5%8F%96%E6%8E%A5%E5%8F%A3%E8%B0%83%E7%94%A8%E5%87%AD%E6%8D%AE)