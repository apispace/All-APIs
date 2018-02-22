## 公交、地铁路线规划 - PHP调用示例代码

#### 公交、地铁路线规划
公交、驾车、骑行、步行查询，返回json格式的检索数据。

#### 该产品拥有以下APIs：
1. 路线查询

**注意，该示例代码仅适用于 [www.apishop.net](www.apishop.net "www.apishop.net") 网站下API**
**使用该产品前，您需要通过 [https://www.apishop.net/#/api/detail/?productID=105](https://www.apishop.net/#/api/detail/?productID=105 "https://www.apishop.net/#/api/detail/?productID=105") 申请API服务**

---

#### 1.路线查询

```
<?php
$method = "POST";
$url = "https://api.apishop.net/common/direction/queryRoutes";
$headers = NULL;
$params = array(			
		"origin"=>"" //起点名称或经纬度，或者可同时提供名称和经纬度，此时经纬度优先级高，将作为导航依据，名称只负责展示。若为步行路径规划，则起终点间直线距离不应超过200公里。如“广州塔”、“广州塔|23.114155,113.318977”			
		"destination"=>"" //终点名称或经纬度，或者可同时提供名称和经纬度，此时经纬度优先级高，将作为导航依据，名称只负责展示。若为步行路径规划，则起终点间直线距离不应超过200公里。如“广州塔”、“广州塔|23.114155,113.318977”			
		"mode"=>"" //导航模式，包括：driving（驾车）、walking（步行）、transit（公交）、riding（骑行）,默认为transit			
		"region"=>"" //所在城市，公交、步行导航时该参数必填。			
		"origin_region"=>"" //起始点所在城市，驾车导航时必填。			
		"destination_region"=>"" //终点所在城市，驾车导航时必填。			
		"coord_type"=>"" //坐标类型，默认为bd09ll。允许的值为：bd09ll（百度经纬度坐标）、bd09mc（百度摩卡托坐标）、gcj02（国测局加密坐标）、wgs84（gps设备获取的坐标）。			
		"ret_coordtype"=>"" //可选，默认为百度经纬度坐标：bd09ll 可选值：  bd09ll：百度经纬度坐标  gcj02：国测局坐标			
		"waypoints"=>"" //途经点集合，包括一个或多个用竖线字符"|"分隔的地址名称或经纬度。支持驾车、步行方案，最多支持5个途经点。			
		"tactics"=>"" //导航策略,默认为12。可选值， 10：不走高速；  11：常规路线，即多数人常走的一条路线，不受路况影响，可用于用车估价；  12：距离较短，即距离相对较短的一条路线，但并不一定是一条优质路线；  13：躲避拥堵		
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