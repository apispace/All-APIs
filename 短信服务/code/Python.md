## 短信服务 - Python调用示例代码

#### 短信服务
通知类和验证码短信，全国三网合一通道，5秒内到达，费用低至3.3分/条。可联系客服调整短信接收条数上限。

#### 该产品拥有以下APIs：
1. 发送短信（验证码、通知类）

**注意，该示例代码仅适用于 [www.apishop.net](www.apishop.net "www.apishop.net") 网站下API**
**使用该产品前，您需要通过 [https://www.apishop.net/#/api/detail/?productID=62](https://www.apishop.net/#/api/detail/?productID=62 "https://www.apishop.net/#/api/detail/?productID=62") 申请API服务**

---

#### 1.发送短信（验证码、通知类）

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
url = "https://api.apishop.net/communication/sms/send"
headers = None
params = {			
		"phoneNum":"" #目标手机号（运营商限制同一手机号一天发送不得超过10条，特殊需求请与我们联系）			
		"templateID":"" #短信模板ID，短信模版审核通过后即生效			
		"params":"" #短信变量字段json数组，用于替换短信模板中的@字符，长度最大20，如短信模板里有2个变量，分别用123和abc替换，那么该参数的示例为[123,"abc"]
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