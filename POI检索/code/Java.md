## POI检索 - Java调用示例代码

#### POI检索
通过关键词查询在某个地区的POI信息，支持市级、区县级查询：比如在广州查询“银行”，接口将会输出所有银行的地理信息列表。

#### 该产品拥有以下APIs：
1. POI搜索
2. 周边POI搜索
3. POI多边形搜索

**注意，该示例代码仅适用于 [www.apishop.net](www.apishop.net "www.apishop.net") 网站下API**
**使用该产品前，您需要通过 [https://www.apishop.net/#/api/detail/?productID=97](https://www.apishop.net/#/api/detail/?productID=97 "https://www.apishop.net/#/api/detail/?productID=97") 申请API服务**

---

#### 1.POI搜索

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
		params.put("keyWords", ""); //查询关键字（规则： 多个关键字用“|”分割 若不指定city，并且搜索的为泛词（例如“美食”）的情况下，返回的内容为城市列表以及此城市内有多少结果符合要求。）			
		params.put("types", ""); //查询POI类型(多个类型用“|”分割； 可选值：分类代码 或 汉字（若用汉字，请严格按照附件之中的汉字填写） 分类代码由六位数字组成，一共分为三个部分，前两个数字代表大类；中间两个数字代表中类；最后两个数字代表小类。 若指定了某个大类，则所属的中类、小类都会被显示。 例如：010000为汽车服务（大类）              010100为加油站（中类）                 010101为中国石化（小类）              010900为汽车租赁（中类）             )			
		params.put("city", ""); //查询城市(可选值：城市中文、中文全拼  如：北京/beijing 填入此参数后，会尽量优先返回此城市数据，但是不一定仅局限此城市结果，若仅需要某个城市数据请调用citylimit参数。 如：在深圳市搜天安门，返回北京天安门结果。)			
		params.put("cityLimit", ""); //仅返回指定城市数据(可选值：true/false)			
		params.put("children", ""); //是否按照层级展示子POI数据(可选值：children=1)			
		params.put("offset", ""); //每页记录数据(强烈建议不超过25，若超过25可能造成访问报错)			
		params.put("page", ""); //当前页数(最大翻页数100)			
		params.put("building", ""); //建筑物的POI编号(传入建筑物POI编号之后，则只在该建筑物之内进行搜索)			
		params.put("floor", ""); //搜索楼层(若传入 建筑物的POI编号 + 楼层 ，则返回该建筑物内当前楼层的关键字搜索结果 若只传入楼层，则返回参数不完全的提示 若传入建筑物的POI编号 + 楼层，该楼层没有对应的搜索结果，则会返回建筑物之内的内容。)			
		params.put("extensions", ""); //此项默认返回基本地址信息；取值为all返回地址信息、附近POI、道路以及道路交叉口信息		
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
---

#### 2.周边POI搜索

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
		params.put("location", ""); //中心点坐标（规则： 经度和纬度用","分割，经度在前，纬度在后，经纬度小数点后不得超过6位）			
		params.put("keyWords", ""); //查询关键字（规则： 多个关键字用“|”分割 若不指定city，并且搜索的为泛词（例如“美食”）的情况下，返回的内容为城市列表以及此城市内有多少结果符合要求。）			
		params.put("types", ""); //查询POI类型(多个类型用“|”分割； 可选值：分类代码 或 汉字（若用汉字，请严格按照附件之中的汉字填写） 分类代码由六位数字组成，一共分为三个部分，前两个数字代表大类；中间两个数字代表中类；最后两个数字代表小类。 若指定了某个大类，则所属的中类、小类都会被显示。 例如：010000为汽车服务（大类）              010100为加油站（中类）                 010101为中国石化（小类）              010900为汽车租赁（中类）             )			
		params.put("city", ""); //查询城市(可选值：城市中文、中文全拼  如：北京/beijing 填入此参数后，会尽量优先返回此城市数据，但是不一定仅局限此城市结果，若仅需要某个城市数据请调用citylimit参数。 如：在深圳市搜天安门，返回北京天安门结果。)			
		params.put("radius", ""); //查询半径（取值范围:0-50000。规则：大于50000按默认值，单位：米）			
		params.put("sortrule", ""); //排序规则（规定返回结果的排序规则。 按距离排序：distance；综合排序：weight，默认为distance）			
		params.put("offset", ""); //每页记录数据(强烈建议不超过25，若超过25可能造成访问报错)			
		params.put("page", ""); //当前页数(最大翻页数100)			
		params.put("extensions", ""); //此项默认返回基本地址信息；取值为all返回地址信息、附近POI、道路以及道路交叉口信息		
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
---

#### 3.POI多边形搜索

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
		params.put("polygon", ""); //经纬度坐标对（规则：经度和纬度用","分割，经度在前，纬度在后，坐标对用"|"分割。经纬度小数点后不得超过6位。         多边形为矩形时，可传入左上右下两顶点坐标对；其他情况下首尾坐标对需相同。）			
		params.put("keyWords", ""); //查询关键字（规则： 多个关键字用“|”分割 若不指定city，并且搜索的为泛词（例如“美食”）的情况下，返回的内容为城市列表以及此城市内有多少结果符合要求。）			
		params.put("types", ""); //查询POI类型(多个类型用“|”分割； 可选值：分类代码 或 汉字（若用汉字，请严格按照附件之中的汉字填写） 分类代码由六位数字组成，一共分为三个部分，前两个数字代表大类；中间两个数字代表中类；最后两个数字代表小类。 若指定了某个大类，则所属的中类、小类都会被显示。 例如：010000为汽车服务（大类）              010100为加油站（中类）                 010101为中国石化（小类）              010900为汽车租赁（中类）             )			
		params.put("city", ""); //查询城市(可选值：城市中文、中文全拼  如：北京/beijing 填入此参数后，会尽量优先返回此城市数据，但是不一定仅局限此城市结果，若仅需要某个城市数据请调用citylimit参数。 如：在深圳市搜天安门，返回北京天安门结果。)			
		params.put("offset", ""); //每页记录数据(强烈建议不超过25，若超过25可能造成访问报错)			
		params.put("page", ""); //当前页数(最大翻页数100)			
		params.put("extensions", ""); //此项默认返回基本地址信息；取值为all返回地址信息、附近POI、道路以及道路交叉口信息		
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