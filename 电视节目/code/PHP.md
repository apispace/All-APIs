## 电视节目 - PHP调用示例代码

#### 电视节目
央视及各地卫视的电视节目时间表，包括本周及下周的电视节目内容

#### 该产品拥有以下APIs：
1. 获取电视台分类
2. 获取电视频道
3. 获取电视节目的详情

**注意，该示例代码仅适用于 [www.apishop.net](www.apishop.net "www.apishop.net") 网站下API**
**使用该产品前，您需要通过 [https://www.apishop.net/#/api/detail/?productID=129](https://www.apishop.net/#/api/detail/?productID=129 "https://www.apishop.net/#/api/detail/?productID=129") 申请API服务**

---

#### 1.获取电视台分类

```
<?php
$method = "POST";
$url = "https://api.apishop.net/common/tv/queryCategory";
$headers = NULL;
$params = array(		
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

#### 2.获取电视频道

```
<?php
$method = "POST";
$url = "https://api.apishop.net/common/tv/queryTVChannel";
$headers = NULL;
$params = array(			
		"categoryName"=>"" //频道分类名称（从获取频道分类接口获得，如“央视”）		
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

#### 3.获取电视节目的详情

```
<?php
$method = "POST";
$url = "https://api.apishop.net/common/tv/queryTVProgram";
$headers = NULL;
$params = array(			
		"id"=>"" //频道ID（从获取频道接口获得）			
		"date"=>"" //日期（格式：yyyy-mm-dd,如“2017-09-27”），默认当天		
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