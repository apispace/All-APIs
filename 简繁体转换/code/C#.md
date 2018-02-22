## 简繁体转换 - C#调用示例代码

#### 简繁体转换
简繁体转换

#### 该产品拥有以下APIs：
1. 简繁体转换

**注意，该示例代码仅适用于 [www.apishop.net](www.apishop.net "www.apishop.net") 网站下API**
**使用该产品前，您需要通过 [https://www.apishop.net/#/api/detail/?productID=121](https://www.apishop.net/#/api/detail/?productID=121 "https://www.apishop.net/#/api/detail/?productID=121") 申请API服务**

---

#### 1.简繁体转换

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
			string url = "https://api.apishop.net/common/convert/chineseConvert";
			Dictionary<string, string> param = new Dictionary<string, string>();			
			param.Add("words", ""); //带转换的中文(最大长度65535)			
			param.Add("type", ""); //转换类型[1:简体到繁体,2:繁体到简体,3:香港繁体（香港小学学习字词表标准）到简体,4:简体到香港繁体（香港小学学习字词表标准）,5:简体到台湾正体,6:简体到繁体（台湾正体标准）并转换为台湾常用词汇,7: 繁体到香港繁体（香港小学学习字词表标准）,8:繁体到台湾正体,9:台湾正体到简体,10:繁体（台湾正体标准）到简体并转换为中国大陆常用词汇],默认为1		
		
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