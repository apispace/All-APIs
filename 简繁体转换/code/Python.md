## 简繁体转换 - Python调用示例代码

#### 简繁体转换
简繁体转换

#### 该产品拥有以下APIs：
1. 简繁体转换

**注意，该示例代码仅适用于 [www.apishop.net](www.apishop.net "www.apishop.net") 网站下API**
**使用该产品前，您需要通过 [https://www.apishop.net/#/api/detail/?productID=121](https://www.apishop.net/#/api/detail/?productID=121 "https://www.apishop.net/#/api/detail/?productID=121") 申请API服务**

---

#### 1.简繁体转换

```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
# 测试环境: python2.7
# 安装requests依赖 => pip install requests/ easy_install requests

# 导入requests依赖
import requests
import json
import sys

reload(sys)
sys.setdefaultencoding('utf-8')


def apishop_send_request(method, url, params=None, headers=None):
    '''
    转发请求到目的主机
    @param method str 请求方法
    @param url str 请求地址
    @param params dict 请求参数
    @param headers dict 请求头
    '''
    method = str.upper(method)
    if method == 'POST':
        return requests.post(url=url, data=params, headers=headers)
    elif method == 'GET':
        return requests.get(url=url, params=params, headers=headers)
    else:
        return None


method = "POST"
url = "https://api.apishop.net/common/convert/chineseConvert"
headers = None
params = {			
		"words":"" #带转换的中文(最大长度65535)			
		"type":"" #转换类型[1:简体到繁体,2:繁体到简体,3:香港繁体（香港小学学习字词表标准）到简体,4:简体到香港繁体（香港小学学习字词表标准）,5:简体到台湾正体,6:简体到繁体（台湾正体标准）并转换为台湾常用词汇,7: 繁体到香港繁体（香港小学学习字词表标准）,8:繁体到台湾正体,9:台湾正体到简体,10:繁体（台湾正体标准）到简体并转换为中国大陆常用词汇],默认为1
}
result = apishop_send_request(method=method, url=url, params=params, headers=headers)
if result:
    body = result.text
    response = json.loads(body)
    status_code = response["statusCode"]
    if (status_code == '000000'):
        # 状态码为000000, 说明请求成功
        print('请求成功：%s' % (body,))
    else:
        # 状态码非000000, 说明请求失败
        print('请求失败: %s' % (body,))
else:
    # 返回内容异常，发送请求失败
    print('发送请求失败')


```