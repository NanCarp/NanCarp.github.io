---
title: JFinal Weixin 学习笔记（9）-- 微信账号二维码
date: 2017-05-24 17:02:43
tags: JFinal 微信
---

## 接口介绍  
----
#### 生成带参数的二维码 `QrcodeApi`  
为了满足用户渠道推广分析和用户帐号绑定等场景的需要，公众平台提供了生成带参数二维码的接口。使用该接口可以获得多个带不同场景值的二维码，用户扫描后，公众号可以接收到事件推送。    
1、临时二维码，是有过期时间的，最长可以设置为在二维码生成后的30天（即2592000秒）后过期，但能够生成较多数量。临时二维码主要用于帐号绑定等不要求二维码永久保存的业务场景  。
2、永久二维码，是无过期时间的，但数量较少（目前为最多10万个）。永久二维码主要用于适用于帐号绑定、用户来源统计等场景。  
  
```
/**
 * 创建临时二维码
 * @param expireSeconds 该二维码有效时间，以秒为单位。 最大不超过604800（即7天）。
 * @param sceneId 场景值ID，临时二维码时为32位非0整型
 * @return ApiResult 二维码信息
 */
public static ApiResult createTemporary(int expireSeconds, int sceneId);

/**
 * 创建永久二维码
 * @param sceneId 场景值ID，永久二维码时最大值为100000（目前参数只支持1--100000）
 * @return ApiResult 二维码信息
 */
public static ApiResult createPermanent(int sceneId);

/**
 * 创建永久二维码
 * @param sceneStr 场景值ID（字符串形式的ID），字符串类型，长度限制为1到64，仅永久二维码支持此字段
 * @return ApiResult 二维码信息
 */
public static ApiResult createPermanent(String sceneStr);

/**
 * 通过ticket换取二维码地址，拼接二维码连接，可直接通过<img>显示
 * @param ticket 换取二维码参数
 * @return String url
 */
public static String getShowQrcodeUrl(String ticket);
```
  
#### 长链接转短链接接口 `ShorturlApi`  
将一条长链接转成短链接。  
主要使用场景： 开发者用于生成二维码的原链接（商品、支付二维码等）太长导致扫码速度和成功率下降，将原长链接通过此接口转成短链接再生成二维码将大大提升扫码速度和成功率。
  
```
/**
 * 长链接转短链接接口
 * @param longUrl 需要转换的长链接，支持http://、https://、weixin://wxpay 格式的url
 * @return ApiResult 短连接信息
 */
public static ApiResult getShortUrl(String longUrl);
```
  
## 代码  
----
#### WeixinApiController.java  
  
	// 生成带参数的二维码
	// 创建临时二维码
	public void createTemporary(){
		ApiResult apiResult = null;
		// 过期时间
		int expireSeconds = 604800;
		// 场景值
		int sceneId = 111;
		// 生成临时二维码
		apiResult = QrcodeApi.createTemporary(expireSeconds, sceneId);
		// 获取 ticket,拼接 url 
        JSONObject jsonObjec = JSONObject.parseObject(apiResult.getJson());
        String ticket = jsonObjec.getString("ticket");
        String outMessage = "apiResult: " + apiResult.getJson() +"\n"
        		+ "QrcodeUrl: " + QrcodeApi.getShowQrcodeUrl(ticket);
        renderText(outMessage);
	}
	
	// 长链接转短链接接口 ShorturlApi
	public void getShortUrl(){
		ApiResult apiResult = null;
		String longUrl = "https://mp.weixin.qq.com/cgi-bin/showqrcode?ticket=gQHT7zwAAAAAAAAAAS5odHRwOi8vd2VpeGluLnFxLmNvbS9xLzAyemlhcVFPa0JmUWoxM01GRk5wMW0AAgRw7x9ZAwSAOgkA";
        apiResult = ShorturlApi.getShortUrl(longUrl);
        JSONObject jsonObjec = JSONObject.parseObject(apiResult.getJson());
        String shorUrl = jsonObjec.getString("short_url");
		String outMessage = "apiResult: " + apiResult.getJson() +"\n"
				+ "longUrl: " + longUrl + "\n\n"
        		+ "shorUrl: " + shorUrl;
        renderText(outMessage);
	}


#### index.html  

	<h3>微信账号二维码</h3>
	点击<a href='/api/createTemporary'>【创建临时二维码】</a><br>
	点击<a href='/api/getShortUrl'>【长链接转短链接】</a><br>
  
## 运行结果
----

![测试页面](http://upload-images.jianshu.io/upload_images/5343805-9f40e18fe79d47a5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

![创建临时二维码](http://upload-images.jianshu.io/upload_images/5343805-9d519c902e908093.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![长链接转短链接](http://upload-images.jianshu.io/upload_images/5343805-7a07dbca0607d4d1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

----
[源码地址](http://git.oschina.net/NanCarp/jfinal-weixin-nancarp)  
[JFinal Weixin 学习笔记（1）-- 目录](http://www.jianshu.com/p/0ea20e77cf29)  

#### 参考文章
[微信开发之推广支持](http://blog.csdn.net/zyw_java/article/details/54024672)
[jfinal-weixin-wiki 帐号管理](http://git.oschina.net/jfinal/jfinal-weixin/wikis/%E5%B8%90%E5%8F%B7%E7%AE%A1%E7%90%86#长链接转短链接接口shorturlapi)