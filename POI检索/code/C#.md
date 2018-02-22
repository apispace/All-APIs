## POI检索 - C#调用示例代码

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
			string url = "https://api.apishop.net/common/POI/queryPOI";
			Dictionary<string, string> param = new Dictionary<string, string>();			
			param.Add("keyWords", ""); //查询关键字（规则： 多个关键字用“|”分割 若不指定city，并且搜索的为泛词（例如“美食”）的情况下，返回的内容为城市列表以及此城市内有多少结果符合要求。）			
			param.Add("types", ""); //查询POI类型(多个类型用“|”分割； 可选值：分类代码 或 汉字（若用汉字，请严格按照附件之中的汉字填写） 分类代码由六位数字组成，一共分为三个部分，前两个数字代表大类；中间两个数字代表中类；最后两个数字代表小类。 若指定了某个大类，则所属的中类、小类都会被显示。 例如：010000为汽车服务（大类）              010100为加油站（中类）                 010101为中国石化（小类）              010900为汽车租赁（中类）             )			
			param.Add("city", ""); //查询城市(可选值：城市中文、中文全拼  如：北京/beijing 填入此参数后，会尽量优先返回此城市数据，但是不一定仅局限此城市结果，若仅需要某个城市数据请调用citylimit参数。 如：在深圳市搜天安门，返回北京天安门结果。)			
			param.Add("cityLimit", ""); //仅返回指定城市数据(可选值：true/false)			
			param.Add("children", ""); //是否按照层级展示子POI数据(可选值：children=1)			
			param.Add("offset", ""); //每页记录数据(强烈建议不超过25，若超过25可能造成访问报错)			
			param.Add("page", ""); //当前页数(最大翻页数100)			
			param.Add("building", ""); //建筑物的POI编号(传入建筑物POI编号之后，则只在该建筑物之内进行搜索)			
			param.Add("floor", ""); //搜索楼层(若传入 建筑物的POI编号 + 楼层 ，则返回该建筑物内当前楼层的关键字搜索结果 若只传入楼层，则返回参数不完全的提示 若传入建筑物的POI编号 + 楼层，该楼层没有对应的搜索结果，则会返回建筑物之内的内容。)			
			param.Add("extensions", ""); //此项默认返回基本地址信息；取值为all返回地址信息、附近POI、道路以及道路交叉口信息		
		
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

#### 2.周边POI搜索

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
			string url = "https://api.apishop.net/common/POI/queryAroundPOI";
			Dictionary<string, string> param = new Dictionary<string, string>();			
			param.Add("location", ""); //中心点坐标（规则： 经度和纬度用","分割，经度在前，纬度在后，经纬度小数点后不得超过6位）			
			param.Add("keyWords", ""); //查询关键字（规则： 多个关键字用“|”分割 若不指定city，并且搜索的为泛词（例如“美食”）的情况下，返回的内容为城市列表以及此城市内有多少结果符合要求。）			
			param.Add("types", ""); //查询POI类型(多个类型用“|”分割； 可选值：分类代码 或 汉字（若用汉字，请严格按照附件之中的汉字填写） 分类代码由六位数字组成，一共分为三个部分，前两个数字代表大类；中间两个数字代表中类；最后两个数字代表小类。 若指定了某个大类，则所属的中类、小类都会被显示。 例如：010000为汽车服务（大类）              010100为加油站（中类）                 010101为中国石化（小类）              010900为汽车租赁（中类）             )			
			param.Add("city", ""); //查询城市(可选值：城市中文、中文全拼  如：北京/beijing 填入此参数后，会尽量优先返回此城市数据，但是不一定仅局限此城市结果，若仅需要某个城市数据请调用citylimit参数。 如：在深圳市搜天安门，返回北京天安门结果。)			
			param.Add("radius", ""); //查询半径（取值范围:0-50000。规则：大于50000按默认值，单位：米）			
			param.Add("sortrule", ""); //排序规则（规定返回结果的排序规则。 按距离排序：distance；综合排序：weight，默认为distance）			
			param.Add("offset", ""); //每页记录数据(强烈建议不超过25，若超过25可能造成访问报错)			
			param.Add("page", ""); //当前页数(最大翻页数100)			
			param.Add("extensions", ""); //此项默认返回基本地址信息；取值为all返回地址信息、附近POI、道路以及道路交叉口信息		
		
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

#### 3.POI多边形搜索

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
			string url = "https://api.apishop.net/common/POI/queryPolygonPOI";
			Dictionary<string, string> param = new Dictionary<string, string>();			
			param.Add("polygon", ""); //经纬度坐标对（规则：经度和纬度用","分割，经度在前，纬度在后，坐标对用"|"分割。经纬度小数点后不得超过6位。         多边形为矩形时，可传入左上右下两顶点坐标对；其他情况下首尾坐标对需相同。）			
			param.Add("keyWords", ""); //查询关键字（规则： 多个关键字用“|”分割 若不指定city，并且搜索的为泛词（例如“美食”）的情况下，返回的内容为城市列表以及此城市内有多少结果符合要求。）			
			param.Add("types", ""); //查询POI类型(多个类型用“|”分割； 可选值：分类代码 或 汉字（若用汉字，请严格按照附件之中的汉字填写） 分类代码由六位数字组成，一共分为三个部分，前两个数字代表大类；中间两个数字代表中类；最后两个数字代表小类。 若指定了某个大类，则所属的中类、小类都会被显示。 例如：010000为汽车服务（大类）              010100为加油站（中类）                 010101为中国石化（小类）              010900为汽车租赁（中类）             )			
			param.Add("city", ""); //查询城市(可选值：城市中文、中文全拼  如：北京/beijing 填入此参数后，会尽量优先返回此城市数据，但是不一定仅局限此城市结果，若仅需要某个城市数据请调用citylimit参数。 如：在深圳市搜天安门，返回北京天安门结果。)			
			param.Add("offset", ""); //每页记录数据(强烈建议不超过25，若超过25可能造成访问报错)			
			param.Add("page", ""); //当前页数(最大翻页数100)			
			param.Add("extensions", ""); //此项默认返回基本地址信息；取值为all返回地址信息、附近POI、道路以及道路交叉口信息		
		
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