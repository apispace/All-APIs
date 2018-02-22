## 驾考题库 - Python调用示例代码

#### 驾考题库
获取驾考题目与答案

#### 该产品拥有以下APIs：
1. 获取驾考题目与答案

**注意，该示例代码仅适用于 [www.apishop.net](www.apishop.net "www.apishop.net") 网站下API**
**使用该产品前，您需要通过 [https://www.apishop.net/#/api/detail/?productID=74](https://www.apishop.net/#/api/detail/?productID=74 "https://www.apishop.net/#/api/detail/?productID=74") 申请API服务**

---

#### 1.获取驾考题目与答案

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
url = "https://api.apishop.net/transport/exam/getQuestions"
headers = None
params = {			
		"subject":"" #选择考试科目类型：1为科目一；2为科目二；3为科目三；4为科目四			
		"model":"" #驾照类型,有c1、c2、b1、b2、a1、a2，当subject=4时可省略			
		"testType":"" #测试类型：rand代表随机测试（随机100个题目）；order代表顺序测试（所选科目全部题目）
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