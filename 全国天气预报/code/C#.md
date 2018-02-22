## 全国天气预报 - C#调用示例代码

#### 全国天气预报
数据来自国家气象局，可根据地名、经纬度GPS、IP查询对应地区的预报。

#### 该产品拥有以下APIs：
1. 根据地区或id查询7天天气
2. 根据地名查询对应的id
3. 通过地区或id查询24小时天气
4. 地区或地区id查询未来15天天气
5. 根据地区或id查询历史天气
6. 根据景点查询天气
7. 根据GPS经纬度查询天气
8. 根据区号或邮编查询天气

**注意，该示例代码仅适用于 [www.apishop.net](www.apishop.net "www.apishop.net") 网站下API**
**使用该产品前，您需要通过 [https://www.apishop.net/#/api/detail/?productID=76](https://www.apishop.net/#/api/detail/?productID=76 "https://www.apishop.net/#/api/detail/?productID=76") 申请API服务**

---

#### 1.根据地区或id查询7天天气

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
			string url = "https://api.apishop.net/common/weather/getWeatherByArea";
			Dictionary<string, string> param = new Dictionary<string, string>();			
			param.Add("need3HourForcast", ""); //是否需要当天每3/6小时一次的天气预报列表：0代表不需要，1代表需要			
			param.Add("needAlarm", ""); //是否需要天气预警：0代表不需要，1代表需要			
			param.Add("needHourData", ""); //是否需要每小时数据的累积数组。本半小时刷一次实时状态，数组最大长度为48。每天0点长度初始化为0。0代表不需要，1代表需要			
			param.Add("needMoreDay", ""); //是否需要返回7天数据中的后4天：0代表不需要，1代表需要			
			param.Add("area", ""); //地区名：area和areaID参数必须二选一输入，如果都输入，以id为准			
			param.Add("areaID", ""); //地区ID：area和areaID参数必须二选一输入，如果都输入，以id为准		
		
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
---

#### 2.根据地名查询对应的id

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
			string url = "https://api.apishop.net/common/weather/getAreaID";
			Dictionary<string, string> param = new Dictionary<string, string>();			
			param.Add("area", ""); //地区名		
		
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
---

#### 3.通过地区或id查询24小时天气

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
			string url = "https://api.apishop.net/common/weather/get24HoursWeatherByArea";
			Dictionary<string, string> param = new Dictionary<string, string>();			
			param.Add("area", ""); //地区名			
			param.Add("areaID", ""); //地区ID		
		
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
---

#### 4.地区或地区id查询未来15天天气

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
			string url = "https://api.apishop.net/common/weather/get15DaysWeatherByArea";
			Dictionary<string, string> param = new Dictionary<string, string>();			
			param.Add("area", ""); //地区名			
			param.Add("areaID", ""); //地区ID		
		
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
---

#### 5.根据地区或id查询历史天气

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
			string url = "https://api.apishop.net/common/weather/getHistoryWeatherByArea";
			Dictionary<string, string> param = new Dictionary<string, string>();			
			param.Add("area", ""); //地区名			
			param.Add("areaID", ""); //地区ID			
			param.Add("month", ""); //查询的月份：格式yyyyMM（数据自2015年1月开始)		
		
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
---

#### 6.根据景点查询天气

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
			string url = "https://api.apishop.net/common/weather/getWeatherBySpot";
			Dictionary<string, string> param = new Dictionary<string, string>();			
			param.Add("need3HourForcast", ""); //是否需要当天每3/6小时一次的天气预报列表：0代表不需要，1代表需要			
			param.Add("needAlarm", ""); //是否需要天气预警：0代表不需要，1代表需要			
			param.Add("needHourData", ""); //是否需要每小时数据的累积数组：0代表不需要，1代表需要			
			param.Add("needIndex", ""); //是否需要返回指数数据：0代表不需要，1代表需要			
			param.Add("needMoreDay", ""); //是否需要返回7天数据中的后4天：0代表不需要，1代表需要			
			param.Add("spot", ""); //景点名称		
		
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
---

#### 7.根据GPS经纬度查询天气

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
			string url = "https://api.apishop.net/common/weather/getWeatherByGPS";
			Dictionary<string, string> param = new Dictionary<string, string>();			
			param.Add("need3HourForcast", ""); //是否需要当天每3/6小时一次的天气预报列表：0代表不需要，1代表需要			
			param.Add("needAlarm", ""); //是否需要天气预警：0代表不需要，1代表需要			
			param.Add("needHourData", ""); //是否需要每小时数据的累积数组：0代表不需要，1代表需要			
			param.Add("needIndex", ""); //是否需要返回指数数据：0代表不需要，1代表需要			
			param.Add("needMoreDay", ""); //是否需要返回7天数据中的后4天：0代表不需要，1代表需要			
			param.Add("from", ""); //坐标来源： 1代表GPS设备获取的角度坐标、2代表GPS获取的米制坐标、sogou地图所用坐标;、3代表google地图、soso地图、aliyun地图、mapabc地图和amap地图所用坐标 、4代表3中列表地图坐标对应的米制坐标、5代表百度地图采用的经纬度坐标、6代表百度地图采用的米制坐标、7代表mapbar地图坐标			
			param.Add("lat", ""); //经度：仅支持国内经纬度			
			param.Add("lng", ""); //纬度：仅支持国内经纬度		
		
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
---

#### 8.根据区号或邮编查询天气

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
			string url = "https://api.apishop.net/common/weather/getWeatherByPhonePostCode";
			Dictionary<string, string> param = new Dictionary<string, string>();			
			param.Add("need3HourForcast", ""); //是否需要当天每3/6小时一次的天气预报列表：0代表不需要，1代表需要			
			param.Add("needAlarm", ""); //是否需要天气预警：0代表不需要，1代表需要			
			param.Add("needHourData", ""); //是否需要每小时数据的累积数组：0代表不需要，1代表需要			
			param.Add("needIndex", ""); //是否需要返回指数数据：0代表不需要，1代表需要			
			param.Add("needMoreDay", ""); //是否需要返回7天数据中的后4天：0代表不需要，1代表需要			
			param.Add("phoneCode", ""); //电话区号：电话区号和邮编必须二选一输入			
			param.Add("postCode", ""); //邮编：电话区号和邮编必须二选一输入		
		
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