---
title: JFinal Weixin 学习笔记（8）-- 微信自定义菜单
date: 2017-05-23 11:05:43
tags: JFinal 微信
---

----
#### 自定义菜单接口  

```
/**
 * 查询菜单
 */
MenuApi.getMenu();

/**
 * 创建菜单
 */
MenuApi.createMenu(String jsonStr);

/**
 * 自定义菜单删除接口
 */
MenuApi.deleteMenu();
```
  
#### WeinxinApiController.java  
`createMenu(String jsonStr)` 接受 json 格式的字符串，我们根据微信技术文档拼接自定义菜单字符串：

	/**
	 * 创建菜单
	 */
	public void createMenu() {
    	String path = Constants.HOST;
    	String jsonstr = "{" +
				"	\"button\": [" +
				"		{" +
				"			\"name\": \"一级菜单1\"," +
				"			\"sub_button\": [" +
				"				{\"name\": \"二级菜单11\",\"type\": \"view\",\"url\": \"" + path + "/api\"}," +
				"				{\"name\": \"二级菜单12\",\"type\": \"view\",\"url\": \"" + path + "/api\"}," +
				"				{\"name\": \"二级菜单13\",\"type\": \"view\",\"url\": \"" + path + "/api\"}," +
				"				{\"name\": \"二级菜单14\",\"type\": \"view\",\"url\": \"" + path + "/api\"}," +
				"				{\"name\": \"二级菜单15\",\"type\": \"view\",\"url\": \"" + path + "/api\"}]" +
				"		}," +
				"		{" +
                "			\"name\": \"一级菜单2\"," +
				"			\"sub_button\": [" +
				"				{\"name\": \"测　　试\",\"type\": \"view\",\"url\": \"" + path + "/api/index\"}," +
                "				{\"name\": \"click\",\"type\": \"click\",\"key\": \"22\"}," +
                "				{\"name\": \"二级菜单23\",\"type\": \"view\",\"url\": \"" + path + "/api\"}," +
                "				{\"name\": \"百度\",\"type\": \"view\",\"url\": \"http://www.baidu.com\"}," +
				"				{\"name\": \"二级菜单25\",\"type\": \"view\",\"url\": \"" + path + "/api\"}]" +
				"		}," +
				"		{" +
				"			\"name\": \"一级菜单3\"," +
				"			\"sub_button\": ["+
				"				{\"name\": \"二级菜单31\",\"type\": \"view\",\"url\": \"" + path + "/api\"}," +
				"				{\"name\": \"二级菜单32\",\"type\": \"view\",\"url\": \"" + path + "/api\"}," +
				"				{\"name\": \"二级菜单33\",\"type\": \"view\",\"url\": \"" + path + "/api\"}," +
				"				{\"name\": \"二级菜单34\",\"type\": \"view\",\"url\": \"" + path + "/api\"}," +
				"				{\"name\": \"二级菜单35\",\"type\": \"view\",\"url\": \"" + path + "/api\"}]" +
				"		}" +
				"	]" +
				"}";
    	ApiResult apiResult = MenuApi.createMenu(jsonstr);
    	renderText(apiResult.getJson());
    }
  
自定义菜单接口可实现多种类型按钮，详见：[微信公众平台技术文档](https://mp.weixin.qq.com/wiki)。
这里使用了两种：
1、`click`：点击推事件用户点击 click 类型按钮后，微信服务器会通过消息接口推送消息类型为 event 的结构给开发者（参考消息接口指南），并且带上按钮中开发者填写的 key 值，开发者可以通过自定义的 key 值与用户进行交互；
2、`view`：跳转 URL 用户点击 view 类型按钮后，微信客户端将会打开开发者在按钮中填写的网页 URL，可与网页授权获取用户基本信息接口结合，获得用户基本信息。


`getMenu()` 方法，查询自定义菜单：

    /**
	 * 查询菜单
	 */
	public void getMenu() {
		ApiResult apiResult = MenuApi.getMenu();
		renderText(apiResult.getJson());
	}
   
#### WeixinMsgController.java  
`processInMenuEvent(InMenuEvent inMenuEvent)`  方法接受菜单中 `click` 类型按钮的点击事件

	// 自定义菜单事件
	@Override
	protected void processInMenuEvent(InMenuEvent inMenuEvent) {
		OutTextMsg outMsg = new OutTextMsg(inMenuEvent);
	    outMsg.setContent("processInMenuEvent() 方法测试成功");
	    render(outMsg);
	}
  
#### index.html  

	<h3>自定义菜单</h3>
	点击<a href='/api/createMenu'>【创建自定义菜单】</a><br>
	点击<a href='/api/getMenu'>【获取自定义菜单】</a><br>
  
#### 运行  

点击创建菜单，微信客户端不会立马刷新。想立马看到效果，可以取消关注再关注。
微信菜单：
![一级菜单](http://upload-images.jianshu.io/upload_images/5343805-9d135134a26c0b34.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![二级菜单](http://upload-images.jianshu.io/upload_images/5343805-989b1cdda1ae9bf0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![三级菜单](http://upload-images.jianshu.io/upload_images/5343805-9db99bf82a64d269.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  
点击 `click22` 
![Paste_Image.png](http://upload-images.jianshu.io/upload_images/5343805-1f2158641ce18284.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  
新增了“测试”按钮，可以直接跳转主页面：

![测试页面](http://upload-images.jianshu.io/upload_images/5343805-70dcadb1095aebd1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  
点击“获取自定义菜单”  
得到 json 格式的菜单数据：

![菜单数据](http://upload-images.jianshu.io/upload_images/5343805-72c4afa586b48cc0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  
----
[源码地址](http://git.oschina.net/NanCarp/jfinal-weixin-nancarp)  
[JFinal Weixin 学习笔记（9）-- 微信账号二维码](http://www.jianshu.com/p/2d740d059fa0)
[JFinal Weixin 学习笔记（1）-- 目录](http://www.jianshu.com/p/0ea20e77cf29)  

#### 参考文章
[微信公众号开发之自定义菜单](http://blog.csdn.net/zyw_java/article/details/61415114)
[jfinal-weixin-wiki 自定义菜单接口](http://git.oschina.net/jfinal/jfinal-weixin/wikis/%E8%87%AA%E5%AE%9A%E4%B9%89%E8%8F%9C%E5%8D%95%E6%8E%A5%E5%8F%A3)