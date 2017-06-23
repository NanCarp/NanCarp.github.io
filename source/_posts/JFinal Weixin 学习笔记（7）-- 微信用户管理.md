---
title: JFinal Weixin 学习笔记（7）-- 微信用户管理
date: 2017-05-23 11:04:43
tags: JFinal 微信
---

----
#### GroupsApi 接口  

开发者可以使用 `GroupsApi` 接口，对公众平台的分组进行查询、创建、修改、删除等操作。  
  
相关方法：  
`create(String name)` 方法：  
创建分组，一个公众账号，最多支持创建 100 个分组  
参数 name： 分组名  
  
`get()` 方法：  
查询所有分组  
  
`getId(String openid)` 方法：   
通过用户的OpenID查询其所在的 GroupID  
参数 openid： 普通用户的标识，对当前开发者帐号唯一  
  
`update(int id, String name)` 方法：   
修改分组名  
参数 id：分组 id，由微信分配  
参数 name： 分组名字（30 个字符以内）  
  
#### 简单实现  
修改 `WeixinApiController.java`   

	// 获取用户分组列表
	public void getGroups () {
		ApiResult apiResult = GroupsApi.get();
		renderJson(apiResult.getList("groups"));
	}
	
	// 创建分组
	public void createGroup () {
		Date date = new Date();
		GroupsApi.create(date.toString());
		ApiResult apiResult = GroupsApi.get();
		renderJson(apiResult.getList("groups"));
	}
	
	// 删除分组
	public void deleteGroup () {
		ApiResult apiResult = null;
		// 获取当前用户分组列表
		apiResult = GroupsApi.get();
		// 结果转化成 JSONArray 对象
		JSONArray jsonArray = JSON.parseObject(apiResult.toString()).getJSONArray("groups");
		// 删除 id >= 100 的用户分组
		for(int i = 0, size = jsonArray.size(); i < size; i++){
			// 转化成 JSONObject 对象
			JSONObject jsonObject = (JSONObject) jsonArray.get(i);
			// 获取分组 id
			int id = jsonObject.getInteger("id");
			if (id >= 100){
				// 删除用户分组
				GroupsApi.delete(id);
			}
		}
		// 获取删除后的用户数组
		apiResult = GroupsApi.get();
		renderJson(apiResult.getList("groups"));
	}
	
	// 修改分组名
	public void updateGroup(){
		String msg = new String();
		
		// 更新前
		GroupsApi.create("原分组名");
		ApiResult apiResult = GroupsApi.get();
		msg += "更新前： " + apiResult.toString() + "\n";
		
		// 结果转化成 JSONArray 对象
		JSONArray jsonArray = JSON.parseObject(apiResult.toString()).getJSONArray("groups");
		// 更新用户分组
		for(int i = 0, size = jsonArray.size(); i < size; i++){
			// 转化成 JSONObject 对象
			JSONObject jsonObject = (JSONObject) jsonArray.get(i);
			// 获取分组名
			String origin = jsonObject.getString("name");
			if ("原分组名".equals(origin)){
				// 更新用户分组
				GroupsApi.update(jsonObject.getInteger("id"), "新分组名");
				break;
			}
		}
		
		// 更新后
		msg += "更新后： " + GroupsApi.get().toString() + "\n";
		renderText(msg);
	}
  
修改 `index.html` 

	<h3>用户管理</h3>
	点击<a href='/api/getGroups'>【获取用户分组列表】</a><br>
	点击<a href='/api/createGroup'>【创建用户分组】</a><br>
	点击<a href='/api/deleteGroup'>【删除用户分组】</a><br>
	点击<a href='/api/updateGroup'>【修改用户分组名】</a><br>
  
#### 运行结果
  
主页面：
![主页](http://upload-images.jianshu.io/upload_images/5343805-250d7a21f6ea6191.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

获取用户分组列表：  
![获取用户分组列表](http://upload-images.jianshu.io/upload_images/5343805-8c170d7c51dac536.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

创建用户分组：  
![创建用户分组](http://upload-images.jianshu.io/upload_images/5343805-1f12e305ac37050f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

删除用户分组：  
![删除用户分组](http://upload-images.jianshu.io/upload_images/5343805-154a6af55b6672cc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

修改用户分组名：  
![修改用户分组名](http://upload-images.jianshu.io/upload_images/5343805-4156f14aba45b9df.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  
----
[源码地址](http://git.oschina.net/NanCarp/jfinal-weixin-nancarp)  
[JFinal Weixin 学习笔记（8）-- 微信自定义菜单](http://www.jianshu.com/p/0039f943f91d) 
[JFinal Weixin 学习笔记（1）-- 目录](http://www.jianshu.com/p/0ea20e77cf29)  

#### 参考文章  
[jfinal-weixin-wiki 用户管理](http://git.oschina.net/jfinal/jfinal-weixin/wikis/%E7%94%A8%E6%88%B7%E7%AE%A1%E7%90%86)