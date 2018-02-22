## 英数_验证码识别 - C#调用示例代码

#### 英数_验证码识别
纯数字，纯英文，数字+英文

#### 该产品拥有以下APIs：
1. 识别英数验证码

**注意，该示例代码仅适用于 [www.apishop.net](www.apishop.net "www.apishop.net") 网站下API**
**使用该产品前，您需要通过 [https://www.apishop.net/#/api/detail/?productID=109](https://www.apishop.net/#/api/detail/?productID=109 "https://www.apishop.net/#/api/detail/?productID=109") 申请API服务**

---

#### 1.识别英数验证码

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
			string url = "https://api.apishop.net/common/checkcode/recognizeVerifyCodeSimple";
			Dictionary<string, string> param = new Dictionary<string, string>();			
			param.Add("convertJPG", ""); //是否转换成jpg格式（有少量png或gif图转成jpg格式后识别率明显提高，但并不是所有png或gif转成jpg后都会提高识别率。） 此字段为1时表示需要把图片转为jpg格式，其他值不做转换操作。			
			param.Add("imgBASE64", ""); //图片文件的base64字符串。图片大小需要小于100KB。			
			param.Add("codeType", ""); //验证码类型,最大长度为10.(10:任意长度数字（成功率有所降低）,11~19:1~9位数字,20:任意长度英文（成功率有所降低）,21~29:1~9位英文,30:任意长度英数混合（成功率有所降低）,31~39:1~9位英数混合),如“34”代表4位英数混合		
		
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