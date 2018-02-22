## 公交、地铁路线规划 - Java调用示例代码

#### 公交、地铁路线规划
公交、驾车、骑行、步行查询，返回json格式的检索数据。

#### 该产品拥有以下APIs：
1. 路线查询

**注意，该示例代码仅适用于 [www.apishop.net](www.apishop.net "www.apishop.net") 网站下API**
**使用该产品前，您需要通过 [https://www.apishop.net/#/api/detail/?productID=105](https://www.apishop.net/#/api/detail/?productID=105 "https://www.apishop.net/#/api/detail/?productID=105") 申请API服务**

---

#### 1.路线查询

```
package net.apishop.www.controller;

import java.io.DataOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.UnsupportedEncodingException;
import java.net.HttpURLConnection;
import java.net.MalformedURLException;
import java.net.URL;
import java.net.URLEncoder;
import java.util.HashMap;
import java.util.Map;
import com.alibaba.fastjson.JSONObject;

/**
 * httpUrlConnection访问远程接口工具
 */
public class Api
{

	/**
	 * 方法体说明：向远程接口发起请求，返回字节流类型结果
	 * param url 接口地址
	 * param requestMethod 请求方式
	 * param params 传递参数     重点：参数值需要用Base64进行转码
	 * return InputStream 返回结果
	 */
	public static InputStream httpRequestToStream(String url, String requestMethod, Map<String, String> params)
	{
		InputStream is = null;
		try
		{
			String parameters = "";
			boolean hasParams = false;
			// 将参数集合拼接成特定格式，如name=zhangsan&age=24
			for (String key : params.keySet())
			{
				String value = URLEncoder.encode(params.get(key), "UTF-8");
				parameters += key + "=" + value + "&";
				hasParams = true;
			}
			if (hasParams)
			{
				parameters = parameters.substring(0, parameters.length() - 1);
			}

			// 请求方式是否为get
			boolean isGet = "get".equalsIgnoreCase(requestMethod);
			// 请求方式是否为post
			boolean isPost = "post".equalsIgnoreCase(requestMethod);
			if (isGet)
			{
				url += "?" + parameters;
			}

			URL u = new URL(url);
			HttpURLConnection conn = (HttpURLConnection) u.openConnection();

			// 请求的参数类型(使用restlet框架时，为了兼容框架，必须设置Content-Type为“”空)
			conn.setRequestProperty("Content-Type", "application/octet-stream");
			// conn.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
			// 设置连接超时时间
			conn.setConnectTimeout(50000);
			// 设置读取返回内容超时时间
			conn.setReadTimeout(50000);
			// 设置向HttpURLConnection对象中输出，因为post方式将请求参数放在http正文内，因此需要设置为ture，默认false
			if (isPost)
			{
				conn.setDoOutput(true);
			}
			// 设置从HttpURLConnection对象读入，默认为true
			conn.setDoInput(true);
			// 设置是否使用缓存，post方式不能使用缓存
			if (isPost)
			{
				conn.setUseCaches(false);
			}
			// 设置请求方式，默认为GET
			conn.setRequestMethod(requestMethod);

			// post方式需要将传递的参数输出到conn对象中
			if (isPost)
			{
				DataOutputStream dos = new DataOutputStream(conn.getOutputStream());
				dos.writeBytes(parameters);
				dos.flush();
				dos.close();
			}

			// 从HttpURLConnection对象中读取响应的消息
			// 执行该语句时才正式发起请求
			is = conn.getInputStream();
		}
		catch(UnsupportedEncodingException e)
		{
			e.printStackTrace();
		}
		catch(MalformedURLException e)
		{
			e.printStackTrace();
		}
		catch(IOException e)
		{
			e.printStackTrace();
		}
		return is;
	}

	public static void main(String args[])
    {
		String url = "https://api.apishop.net/common/postcode/getPostCodeByAddr";
		String requestMethod = "POST";
		Map<String, String> params = new HashMap<String, String>();			
		params.put("origin", ""); //起点名称或经纬度，或者可同时提供名称和经纬度，此时经纬度优先级高，将作为导航依据，名称只负责展示。若为步行路径规划，则起终点间直线距离不应超过200公里。如“广州塔”、“广州塔|23.114155,113.318977”			
		params.put("destination", ""); //终点名称或经纬度，或者可同时提供名称和经纬度，此时经纬度优先级高，将作为导航依据，名称只负责展示。若为步行路径规划，则起终点间直线距离不应超过200公里。如“广州塔”、“广州塔|23.114155,113.318977”			
		params.put("mode", ""); //导航模式，包括：driving（驾车）、walking（步行）、transit（公交）、riding（骑行）,默认为transit			
		params.put("region", ""); //所在城市，公交、步行导航时该参数必填。			
		params.put("origin_region", ""); //起始点所在城市，驾车导航时必填。			
		params.put("destination_region", ""); //终点所在城市，驾车导航时必填。			
		params.put("coord_type", ""); //坐标类型，默认为bd09ll。允许的值为：bd09ll（百度经纬度坐标）、bd09mc（百度摩卡托坐标）、gcj02（国测局加密坐标）、wgs84（gps设备获取的坐标）。			
		params.put("ret_coordtype", ""); //可选，默认为百度经纬度坐标：bd09ll 可选值：  bd09ll：百度经纬度坐标  gcj02：国测局坐标			
		params.put("waypoints", ""); //途经点集合，包括一个或多个用竖线字符"|"分隔的地址名称或经纬度。支持驾车、步行方案，最多支持5个途经点。			
		params.put("tactics", ""); //导航策略,默认为12。可选值， 10：不走高速；  11：常规路线，即多数人常走的一条路线，不受路况影响，可用于用车估价；  12：距离较短，即距离相对较短的一条路线，但并不一定是一条优质路线；  13：躲避拥堵		
		String result = null;
		try
		{
			InputStream is = httpRequestToStream(url, requestMethod, params);
			byte[] b = new byte[is.available()];
			is.read(b);
			result = new String(b);
		}
		catch(IOException e)
		{
		    e.printStackTrace();
		}
		if (result != null)
		{
		    JSONObject jsonObject = JSONObject.parseObject(result);
		    String status_code = jsonObject.getString("statusCode");
		    if (status_code == "000000")
		    {
			// 状态码为000000, 说明请求成功
			System.out.println("请求成功：" + jsonObject.getString("result"));
		    }
		    else
		    {
			// 状态码非000000, 说明请求失败
			System.out.println("请求失败：" + jsonObject.getString("desc"));
		    }
		}
		else
		{
		    // 返回内容异常，发送请求失败，以下可根据业务逻辑自行修改
		    System.out.println("发送请求失败");
		}
	}
}
```