---
title: JFinal Weixin 学习笔记（2）-- 微信开发前的准备工作
date: 2017-05-17 10:23:43
tags: JFinal 微信
---

----
#### 在微信公众平台注册成为开发者  

微信公众平台官网：  
[https://mp.weixin.qq.com/](http://www.baidu.com/link?url=tYmv6hajaTZAcKBeumvkCUp3yPpwyhcDxbxYTS3V3dO0O4Dv80GZXTlwxYn_8awE)
注册可以参考百度经验上的回答：  
https://jingyan.baidu.com/article/6525d4b134051eac7d2e9417.html

微信公众平台主页面：  
![](http://upload-images.jianshu.io/upload_images/5343805-9f4bdf2be9481016.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 接口的测试账号申请  
个人账号权限比较少，建议申请测试账号：  
[进入微信公众帐号测试号申请系统](http://mp.weixin.qq.com/debug/cgi-bin/sandbox?t=sandbox/login)
  
或者在开发者工具中进入公众平台测试账号：  
![](http://upload-images.jianshu.io/upload_images/5343805-c6400ba5740ad959.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](http://upload-images.jianshu.io/upload_images/5343805-e55f2cc1cef0ad40.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  
#### 公众平台测试账号相关设置
  
1. 测试账号信息  
appID（应用 ID）、appsecret（应用秘钥）  
![](http://upload-images.jianshu.io/upload_images/5343805-d5dc7bbc1050ac0c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  
2. 接口配置  
URL： 填写自己的服务器地址，**注意结尾一定要加上“/msg”**。  
关于服务器资源的获取，可以用 ngrok 来将自己本地的 tomcat 接口映射为 80 端口：  
[让微信公众平台通过80端口访问本机](http://www.cnblogs.com/zyw-205520/p/4733062.html)  
Token： 自定义  
![](http://upload-images.jianshu.io/upload_images/5343805-61056e30d665d56b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

3. JS 接口安全域名  
填写服务器域名，**开头不需要加上“ http:// ”**。  
![](http://upload-images.jianshu.io/upload_images/5343805-3e11ef73ba57dfd2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
  
到此为止，最基础的配置就已经做好了，可以进行下一步的工作。  

----
[源码地址](http://git.oschina.net/NanCarp/jfinal-weixin-nancarp)  
[JFinal Weixin 学习笔记（3）-- Eclipse 搭建项目框架](http://www.jianshu.com/p/fbf80b794c65)  
[JFinal Weixin 学习笔记（1）-- 目录](http://www.jianshu.com/p/0ea20e77cf29)    
  
#### 参考文章
[微信公众平台测试帐号的注册与使用 - 人生无赖 - 博客园](https://www.baidu.com/link?url=DmdinFOZnfkROj6jj0xrJI-4d67eguNmo9J6XTqfnolcWwzr-8sFam7KQ_rDGHz9s1Xlt9_G-TmwftIXoQo90K&wd=&eqid=c1d1a0a50006354f00000005591bacff)
[微信公众号开发之成为开发者模式](http://www.jianshu.com/p/a87d4b9f9424)
[微信公众平台技术文档 ](https://mp.weixin.qq.com/wiki)