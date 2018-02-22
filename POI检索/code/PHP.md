## POI检索 - PHP调用示例代码

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
<?php
$method = "POST";
$url = "https://api.apishop.net/common/POI/queryPOI";
$headers = NULL;
$params = array(			
		"keyWords"=>"" //查询关键字（规则： 多个关键字用“|”分割 若不指定city，并且搜索的为泛词（例如“美食”）的情况下，返回的内容为城市列表以及此城市内有多少结果符合要求。）			
		"types"=>"" //查询POI类型(多个类型用“|”分割； 可选值：分类代码 或 汉字（若用汉字，请严格按照附件之中的汉字填写） 分类代码由六位数字组成，一共分为三个部分，前两个数字代表大类；中间两个数字代表中类；最后两个数字代表小类。 若指定了某个大类，则所属的中类、小类都会被显示。 例如：010000为汽车服务（大类）              010100为加油站（中类）                 010101为中国石化（小类）              010900为汽车租赁（中类）             )			
		"city"=>"" //查询城市(可选值：城市中文、中文全拼  如：北京/beijing 填入此参数后，会尽量优先返回此城市数据，但是不一定仅局限此城市结果，若仅需要某个城市数据请调用citylimit参数。 如：在深圳市搜天安门，返回北京天安门结果。)			
		"cityLimit"=>"" //仅返回指定城市数据(可选值：true/false)			
		"children"=>"" //是否按照层级展示子POI数据(可选值：children=1)			
		"offset"=>"" //每页记录数据(强烈建议不超过25，若超过25可能造成访问报错)			
		"page"=>"" //当前页数(最大翻页数100)			
		"building"=>"" //建筑物的POI编号(传入建筑物POI编号之后，则只在该建筑物之内进行搜索)			
		"floor"=>"" //搜索楼层(若传入 建筑物的POI编号 + 楼层 ，则返回该建筑物内当前楼层的关键字搜索结果 若只传入楼层，则返回参数不完全的提示 若传入建筑物的POI编号 + 楼层，该楼层没有对应的搜索结果，则会返回建筑物之内的内容。)			
		"extensions"=>"" //此项默认返回基本地址信息；取值为all返回地址信息、附近POI、道路以及道路交叉口信息		
);

$result = apishop_curl($method, $url, $headers, $params);
If ($result) {
   $body = json_decode($result["body"], TRUE);
   $status_code = $body["statusCode"];
   If ($status_code == "000000") {
       //状态码为000000, 说明请求成功
       echo "请求成功：" . $result["body"];
   } else {
       //状态码非000000, 说明请求失败
       echo "请求失败：" . $result["body"];
   }
} else {
   //返回内容异常，发送请求失败，以下可根据业务逻辑自行修改
   echo "发送请求失败";
}

/**
  * 转发请求到目的主机
  * @param $method string 请求方法
  * @param $URL string 请求地址
  * @param null $headers 请求头
  * @param null $param 请求参数
  * @return array|bool
  */
function apishop_curl(&$method, &$URL, &$headers = NULL, &$param = NULL)
{
   // 初始化请求
   $require = curl_init($URL);
   // 判断是否HTTPS
   $isHttps = substr($URL, 0, 8) == "https://" ? TRUE : FALSE;
   // 设置请求方式
   switch ($method) {
       case "GET":
           curl_setopt($require, CURLOPT_CUSTOMREQUEST, "GET");
           break;
       case "POST":
           curl_setopt($require, CURLOPT_CUSTOMREQUEST, "POST");
           break;
       default:
           return FALSE;
   }
   if ($param) {
       curl_setopt($require, CURLOPT_POSTFIELDS, $param);
   }
   if ($isHttps) {
       // 跳过证书检查
       curl_setopt($require, CURLOPT_SSL_VERIFYPEER, FALSE);
       // 检查证书中是否设置域名
       curl_setopt($require, CURLOPT_SSL_VERIFYHOST, 2);
   }
   if ($headers) {
   // 设置请求头
   curl_setopt($require, CURLOPT_HTTPHEADER, $headers);
   }
   // 返回结果不直接输出
   curl_setopt($require, CURLOPT_RETURNTRANSFER, TRUE);
   // 重定向
   curl_setopt($require, CURLOPT_FOLLOWLOCATION, TRUE);
   // 把返回头包含再输出中
   curl_setopt($require, CURLOPT_HEADER, TRUE);
   // 发送请求
   $response = curl_exec($require);
   // 获取头部长度
   $headerSize = curl_getinfo($require, CURLINFO_HEADER_SIZE);
   // 关闭请求
   curl_close($require);
   if ($response) {
       // 返回头部字符串
       $header = substr($response, 0, $headerSize);
       // 返回体
       $body = substr($response, $headerSize);
       // 过滤隐藏非法字符
       $bodyTemp = json_encode(array(
            0 => $body
       ));
       $bodyTemp = str_replace("﻿", "", $bodyTemp);
       $bodyTemp = json_decode($bodyTemp, TRUE);
       $body = trim($bodyTemp[0]);
       // 将返回结果头部转成数组
       $respondHeaders = array();
       $header_rows = array_filter(explode(PHP_EOL, $header), "trim");
       foreach ($header_rows as $row) {
           $keylen = strpos($row, ":");
           if ($keylen) {
               $respondHeaders[] = array(
                   "key" => substr($row, 0, $keylen),
                   "value" => trim(substr($row, $keylen + 1))
               );
           }
       }
       return array(
           "headers" => $respondHeaders,
           "body" => $body
       );
   } else {
       return FALSE;
   }
}
```
---

#### 2.周边POI搜索

```
<?php
$method = "POST";
$url = "https://api.apishop.net/common/POI/queryAroundPOI";
$headers = NULL;
$params = array(			
		"location"=>"" //中心点坐标（规则： 经度和纬度用","分割，经度在前，纬度在后，经纬度小数点后不得超过6位）			
		"keyWords"=>"" //查询关键字（规则： 多个关键字用“|”分割 若不指定city，并且搜索的为泛词（例如“美食”）的情况下，返回的内容为城市列表以及此城市内有多少结果符合要求。）			
		"types"=>"" //查询POI类型(多个类型用“|”分割； 可选值：分类代码 或 汉字（若用汉字，请严格按照附件之中的汉字填写） 分类代码由六位数字组成，一共分为三个部分，前两个数字代表大类；中间两个数字代表中类；最后两个数字代表小类。 若指定了某个大类，则所属的中类、小类都会被显示。 例如：010000为汽车服务（大类）              010100为加油站（中类）                 010101为中国石化（小类）              010900为汽车租赁（中类）             )			
		"city"=>"" //查询城市(可选值：城市中文、中文全拼  如：北京/beijing 填入此参数后，会尽量优先返回此城市数据，但是不一定仅局限此城市结果，若仅需要某个城市数据请调用citylimit参数。 如：在深圳市搜天安门，返回北京天安门结果。)			
		"radius"=>"" //查询半径（取值范围:0-50000。规则：大于50000按默认值，单位：米）			
		"sortrule"=>"" //排序规则（规定返回结果的排序规则。 按距离排序：distance；综合排序：weight，默认为distance）			
		"offset"=>"" //每页记录数据(强烈建议不超过25，若超过25可能造成访问报错)			
		"page"=>"" //当前页数(最大翻页数100)			
		"extensions"=>"" //此项默认返回基本地址信息；取值为all返回地址信息、附近POI、道路以及道路交叉口信息		
);

$result = apishop_curl($method, $url, $headers, $params);
If ($result) {
   $body = json_decode($result["body"], TRUE);
   $status_code = $body["statusCode"];
   If ($status_code == "000000") {
       //状态码为000000, 说明请求成功
       echo "请求成功：" . $result["body"];
   } else {
       //状态码非000000, 说明请求失败
       echo "请求失败：" . $result["body"];
   }
} else {
   //返回内容异常，发送请求失败，以下可根据业务逻辑自行修改
   echo "发送请求失败";
}

/**
  * 转发请求到目的主机
  * @param $method string 请求方法
  * @param $URL string 请求地址
  * @param null $headers 请求头
  * @param null $param 请求参数
  * @return array|bool
  */
function apishop_curl(&$method, &$URL, &$headers = NULL, &$param = NULL)
{
   // 初始化请求
   $require = curl_init($URL);
   // 判断是否HTTPS
   $isHttps = substr($URL, 0, 8) == "https://" ? TRUE : FALSE;
   // 设置请求方式
   switch ($method) {
       case "GET":
           curl_setopt($require, CURLOPT_CUSTOMREQUEST, "GET");
           break;
       case "POST":
           curl_setopt($require, CURLOPT_CUSTOMREQUEST, "POST");
           break;
       default:
           return FALSE;
   }
   if ($param) {
       curl_setopt($require, CURLOPT_POSTFIELDS, $param);
   }
   if ($isHttps) {
       // 跳过证书检查
       curl_setopt($require, CURLOPT_SSL_VERIFYPEER, FALSE);
       // 检查证书中是否设置域名
       curl_setopt($require, CURLOPT_SSL_VERIFYHOST, 2);
   }
   if ($headers) {
   // 设置请求头
   curl_setopt($require, CURLOPT_HTTPHEADER, $headers);
   }
   // 返回结果不直接输出
   curl_setopt($require, CURLOPT_RETURNTRANSFER, TRUE);
   // 重定向
   curl_setopt($require, CURLOPT_FOLLOWLOCATION, TRUE);
   // 把返回头包含再输出中
   curl_setopt($require, CURLOPT_HEADER, TRUE);
   // 发送请求
   $response = curl_exec($require);
   // 获取头部长度
   $headerSize = curl_getinfo($require, CURLINFO_HEADER_SIZE);
   // 关闭请求
   curl_close($require);
   if ($response) {
       // 返回头部字符串
       $header = substr($response, 0, $headerSize);
       // 返回体
       $body = substr($response, $headerSize);
       // 过滤隐藏非法字符
       $bodyTemp = json_encode(array(
            0 => $body
       ));
       $bodyTemp = str_replace("﻿", "", $bodyTemp);
       $bodyTemp = json_decode($bodyTemp, TRUE);
       $body = trim($bodyTemp[0]);
       // 将返回结果头部转成数组
       $respondHeaders = array();
       $header_rows = array_filter(explode(PHP_EOL, $header), "trim");
       foreach ($header_rows as $row) {
           $keylen = strpos($row, ":");
           if ($keylen) {
               $respondHeaders[] = array(
                   "key" => substr($row, 0, $keylen),
                   "value" => trim(substr($row, $keylen + 1))
               );
           }
       }
       return array(
           "headers" => $respondHeaders,
           "body" => $body
       );
   } else {
       return FALSE;
   }
}
```
---

#### 3.POI多边形搜索

```
<?php
$method = "POST";
$url = "https://api.apishop.net/common/POI/queryPolygonPOI";
$headers = NULL;
$params = array(			
		"polygon"=>"" //经纬度坐标对（规则：经度和纬度用","分割，经度在前，纬度在后，坐标对用"|"分割。经纬度小数点后不得超过6位。         多边形为矩形时，可传入左上右下两顶点坐标对；其他情况下首尾坐标对需相同。）			
		"keyWords"=>"" //查询关键字（规则： 多个关键字用“|”分割 若不指定city，并且搜索的为泛词（例如“美食”）的情况下，返回的内容为城市列表以及此城市内有多少结果符合要求。）			
		"types"=>"" //查询POI类型(多个类型用“|”分割； 可选值：分类代码 或 汉字（若用汉字，请严格按照附件之中的汉字填写） 分类代码由六位数字组成，一共分为三个部分，前两个数字代表大类；中间两个数字代表中类；最后两个数字代表小类。 若指定了某个大类，则所属的中类、小类都会被显示。 例如：010000为汽车服务（大类）              010100为加油站（中类）                 010101为中国石化（小类）              010900为汽车租赁（中类）             )			
		"city"=>"" //查询城市(可选值：城市中文、中文全拼  如：北京/beijing 填入此参数后，会尽量优先返回此城市数据，但是不一定仅局限此城市结果，若仅需要某个城市数据请调用citylimit参数。 如：在深圳市搜天安门，返回北京天安门结果。)			
		"offset"=>"" //每页记录数据(强烈建议不超过25，若超过25可能造成访问报错)			
		"page"=>"" //当前页数(最大翻页数100)			
		"extensions"=>"" //此项默认返回基本地址信息；取值为all返回地址信息、附近POI、道路以及道路交叉口信息		
);

$result = apishop_curl($method, $url, $headers, $params);
If ($result) {
   $body = json_decode($result["body"], TRUE);
   $status_code = $body["statusCode"];
   If ($status_code == "000000") {
       //状态码为000000, 说明请求成功
       echo "请求成功：" . $result["body"];
   } else {
       //状态码非000000, 说明请求失败
       echo "请求失败：" . $result["body"];
   }
} else {
   //返回内容异常，发送请求失败，以下可根据业务逻辑自行修改
   echo "发送请求失败";
}

/**
  * 转发请求到目的主机
  * @param $method string 请求方法
  * @param $URL string 请求地址
  * @param null $headers 请求头
  * @param null $param 请求参数
  * @return array|bool
  */
function apishop_curl(&$method, &$URL, &$headers = NULL, &$param = NULL)
{
   // 初始化请求
   $require = curl_init($URL);
   // 判断是否HTTPS
   $isHttps = substr($URL, 0, 8) == "https://" ? TRUE : FALSE;
   // 设置请求方式
   switch ($method) {
       case "GET":
           curl_setopt($require, CURLOPT_CUSTOMREQUEST, "GET");
           break;
       case "POST":
           curl_setopt($require, CURLOPT_CUSTOMREQUEST, "POST");
           break;
       default:
           return FALSE;
   }
   if ($param) {
       curl_setopt($require, CURLOPT_POSTFIELDS, $param);
   }
   if ($isHttps) {
       // 跳过证书检查
       curl_setopt($require, CURLOPT_SSL_VERIFYPEER, FALSE);
       // 检查证书中是否设置域名
       curl_setopt($require, CURLOPT_SSL_VERIFYHOST, 2);
   }
   if ($headers) {
   // 设置请求头
   curl_setopt($require, CURLOPT_HTTPHEADER, $headers);
   }
   // 返回结果不直接输出
   curl_setopt($require, CURLOPT_RETURNTRANSFER, TRUE);
   // 重定向
   curl_setopt($require, CURLOPT_FOLLOWLOCATION, TRUE);
   // 把返回头包含再输出中
   curl_setopt($require, CURLOPT_HEADER, TRUE);
   // 发送请求
   $response = curl_exec($require);
   // 获取头部长度
   $headerSize = curl_getinfo($require, CURLINFO_HEADER_SIZE);
   // 关闭请求
   curl_close($require);
   if ($response) {
       // 返回头部字符串
       $header = substr($response, 0, $headerSize);
       // 返回体
       $body = substr($response, $headerSize);
       // 过滤隐藏非法字符
       $bodyTemp = json_encode(array(
            0 => $body
       ));
       $bodyTemp = str_replace("﻿", "", $bodyTemp);
       $bodyTemp = json_decode($bodyTemp, TRUE);
       $body = trim($bodyTemp[0]);
       // 将返回结果头部转成数组
       $respondHeaders = array();
       $header_rows = array_filter(explode(PHP_EOL, $header), "trim");
       foreach ($header_rows as $row) {
           $keylen = strpos($row, ":");
           if ($keylen) {
               $respondHeaders[] = array(
                   "key" => substr($row, 0, $keylen),
                   "value" => trim(substr($row, $keylen + 1))
               );
           }
       }
       return array(
           "headers" => $respondHeaders,
           "body" => $body
       );
   } else {
       return FALSE;
   }
}
```