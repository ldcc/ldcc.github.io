---
layout: post
title: 一个天气预报 app
category: DEV
date: 2016-11-06
---

一个天气预报本身并不需要什么强大的逻辑维持业务：控件摆好，排版设计得体，再把从其他平台获取到的天气数据显示在你的控件上。
目前许多大公司在 Oauth 的规范下开放了自家 api ，顺带提供了一些地图或者天气的接口，以下介绍几家比较常用的天气 api：

### 百度

<http://developer.baidu.com/map/carapi-7.htm>

注册百度开发者并创建应用后就可以通过 api key 获取百度的数据了。

使用方法：

http://api.map.baidu.com/telematics/v3/weather?location=%s&output=%s&ak=%s

location 的值可以是城市或者经纬度，比如 深圳 或者 116.39277,39.933748

百度支持同时查询多个城市，在多个城市之间插入一个 "\|" 分隔符，就像这样：

?location=北京\|116.305145,39.982368\|上海\|...&output...

output 是返回数据格式，Json 或 XML

ak 就是 api key 了，把申请的 key 直接填进去就行了 

### 新浪

<http://open.weibo.com/wiki/Base/weather>

同样需要注册为开发者获取 api key 才可以使用。

使用方法：

XML: 

http://api.map.sina.com.cn/base/weather.xml?city_name=北京&source=appkey

Json: 

http://api.map.sina.com.cn/base/weather.json?city_name=北京&source=appkey

使用方法都差不多，就不再详细了。

### 聚合数据

<https://www.juhe.cn/docs/api/id/39>

同上需要 api key。

使用方法：

http://v.juhe.cn/weather/index?请求参数

请求参数参考：<https://www.juhe.cn/box/index/id/39>

## 数据的获取和解析

### 获取

以上三个接口，百度和新浪都在返回的数据中包含了天气图标的 url （虽然很丑），只有百度能够单次获取多个城市的天气，但聚合数据返回的数据是最多、最全、范围最广的，所以我还是首先推荐使用聚合数据的接口作为数据源。

获取数据的步骤十分简单，发送请求、得到一个流、保存到内存中以供使用，Android 原生自带的 `HttpURLConnection` 就是最好的选择：

{% highlight java %}

URL url = new URL(URL);
HttpURLConnection conn = (HttpURLConnection) url.openConnection();
conn.setRequestMethod("GET");
InputStream is = conn.getInputStream();

{% endhighlight %}

获取 Json 数据：

{% highlight java %}

BufferedReader br = new BufferedReader(new InputStreamReader(is));
StringBuilder json = new StringBuilder();
String line;
while ((line = br.readLine()) != null) json.append(line);

{% endhighlight %}

获取 XML 数据：

{% highlight java %}

DocumentBuilderFactory factory = DocumentBuilderFactory.newInstance();
DocumentBuilder builder = factory.newDocumentBuilder();
Document document = builder.parse(is);

{% endhighlight %}

### 解析

得到数据后就可以开始解析数据了，以 Json 为例，通过 `getJSONObject` 和 `getJSONArray` 获取更底层的 Json 对象，最后使用 `getString(String name)` 即可得到具体数据，以聚合数据为例：

{% highlight java %}

JSONObject response = new JSONObject(json.toString);
String code = response.getString("resultcode");
if (code.equals("200")) {
	JSONObject result = response.getJSONObject("result");
	setNowWeather(result.getJSONObject("sk"), result.getJSONObject("today"));
	setFutureList(result.getJSONArray("future"));
	// 这里我使用了两个数据类 `NowWeather` 和 `FutureWeather`
	// 在 set 方法中直接传入 Json 或 Json 数组，再到实际的方法中配置数据
	// 由于未来天气的天数过多，不适合将所有数据放在一个单独的类中，所以我把它放到一个 List 中
	return "finish";
} else {
	return "获取Json数据失败";
}

{% endhighlight %}

XML 同理，XML 中的每一个标签都是一个 Element ，每一个符合标准的 XML 数据，最外层有且仅有一个 Element ，所以第一个 Element 要用 Document 的 `getDocumentElement()` 获取。
除此以外，每一个 Element 都可以用 `getElementByTagName(String name)` 获取到一个包含多个 Element 的 `NodeList` 对象，每一个 Element 都可以用 `getTextContent()` 获取它包含的文本数据。
所以也是要不断地重复去调用这些方法去获取实际数据。

>	
Json 实际上是一个 HashTable, XML 实际上是一棵 DOM 树，实际上只要清楚他们的结构就可以写出更为通用的解析过程，就像多叉树递归一样简单。

## 完成？

其实到这里已经完成了 2/3 了，只要再为你的 ListView 配置一个 Adapter 把数据显示出来，再修改下排版，适配屏幕大小...
