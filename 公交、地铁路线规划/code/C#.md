## 公交、地铁路线规划 - C#调用示例代码

#### 公交、地铁路线规划
公交、驾车、骑行、步行查询，返回json格式的检索数据。

#### 该产品拥有以下APIs：
1. 路线查询

**注意，该示例代码仅适用于 [www.apishop.net](www.apishop.net "www.apishop.net") 网站下API**
**使用该产品前，您需要通过 [https://www.apishop.net/#/api/detail/?productID=105](https://www.apishop.net/#/api/detail/?productID=105 "https://www.apishop.net/#/api/detail/?productID=105") 申请API服务**

---

#### 1.路线查询

```
using System;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Text;
using System.Web.Script.Serialization;

namespace apishop_sdk
{
	class Program
	{
		/**
		 * 转发请求到目的主机
		 * @param method string 请求方法
		 * @param url string 请求地址
		 * @param params Dictionary<string,string> 请求参数
		 * @param headers Dictionary<string,string> 请求头
		 * @return string
		 **/
		static string apishop_send_request(string method, string url, Dictionary<string, string> param, Dictionary<string, string> headers)
		{
			string result = string.Empty;
			try
			{
				string paramData = "";
				if (param != null && param.Count > 0)
				{
					StringBuilder sbuilder = new StringBuilder();
					foreach (var item in param)
					{
						if (sbuilder.Length > 0)
						{
							sbuilder.Append("&");
						}
						sbuilder.Append(item.Key + "=" + item.Value);
					}
					paramData = sbuilder.ToString();
				}
				method = method.ToUpper();
				if (method == "GET")
				{
					url = string.Format("{0}?{1}", url, paramData);
				}
				HttpWebRequest wbRequest = (HttpWebRequest)WebRequest.Create(url);
				if (method == "GET")
				{
					wbRequest.Method = "GET";
				}
				else if (method == "POST")
				{
					wbRequest.Method = "POST";
					wbRequest.ContentType = "application/x-www-form-urlencoded";
					wbRequest.ContentLength = Encoding.UTF8.GetByteCount(paramData);
					using (Stream requestStream = wbRequest.GetRequestStream())
					{
						using (StreamWriter swrite = new StreamWriter(requestStream))
						{
							swrite.Write(paramData);
						}
					}
				}

				HttpWebResponse wbResponse = (HttpWebResponse)wbRequest.GetResponse();
				using (Stream responseStream = wbResponse.GetResponseStream())
				{
					using (StreamReader sread = new StreamReader(responseStream))
					{
						result = sread.ReadToEnd();
					}
				}
			}
			catch
			{
				return "";
			}
			return result;
		}

		class Response
		{
			public string statusCode;
		}
		
		static void Main(string[] args)
		{
			string method = "POST";
			string url = "https://api.apishop.net/common/direction/queryRoutes";
			Dictionary<string, string> param = new Dictionary<string, string>();			
			param.Add("origin", ""); //起点名称或经纬度，或者可同时提供名称和经纬度，此时经纬度优先级高，将作为导航依据，名称只负责展示。若为步行路径规划，则起终点间直线距离不应超过200公里。如“广州塔”、“广州塔|23.114155,113.318977”			
			param.Add("destination", ""); //终点名称或经纬度，或者可同时提供名称和经纬度，此时经纬度优先级高，将作为导航依据，名称只负责展示。若为步行路径规划，则起终点间直线距离不应超过200公里。如“广州塔”、“广州塔|23.114155,113.318977”			
			param.Add("mode", ""); //导航模式，包括：driving（驾车）、walking（步行）、transit（公交）、riding（骑行）,默认为transit			
			param.Add("region", ""); //所在城市，公交、步行导航时该参数必填。			
			param.Add("origin_region", ""); //起始点所在城市，驾车导航时必填。			
			param.Add("destination_region", ""); //终点所在城市，驾车导航时必填。			
			param.Add("coord_type", ""); //坐标类型，默认为bd09ll。允许的值为：bd09ll（百度经纬度坐标）、bd09mc（百度摩卡托坐标）、gcj02（国测局加密坐标）、wgs84（gps设备获取的坐标）。			
			param.Add("ret_coordtype", ""); //可选，默认为百度经纬度坐标：bd09ll 可选值：  bd09ll：百度经纬度坐标  gcj02：国测局坐标			
			param.Add("waypoints", ""); //途经点集合，包括一个或多个用竖线字符"|"分隔的地址名称或经纬度。支持驾车、步行方案，最多支持5个途经点。			
			param.Add("tactics", ""); //导航策略,默认为12。可选值， 10：不走高速；  11：常规路线，即多数人常走的一条路线，不受路况影响，可用于用车估价；  12：距离较短，即距离相对较短的一条路线，但并不一定是一条优质路线；  13：躲避拥堵		
		
			Dictionary<string, string> headers = null;
			string result = apishop_send_request(method, url, param, headers);
			if (result == "")
			{
				//返回内容异常，发送请求失败
				Console.WriteLine("发送请求失败");
				return;
			}

			Response res = new JavaScriptSerializer().Deserialize<Response>(result);
			if (res.statusCode == "000000")
			{
				//状态码为000000, 说明请求成功
				Console.WriteLine(string.Format("请求成功: {0}", result));
			}
			else
			{
				//状态码非000000, 说明请求失败
				Console.WriteLine(string.Format("请求失败: {0}", result));
			}
			Console.ReadLine();
		}
	}
}

```