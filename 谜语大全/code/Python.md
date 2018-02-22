## 谜语大全 - Python调用示例代码

#### 谜语大全
字谜、动物、灯谜、物品、儿童、植物、及成语等各种类型的谜语。

#### 该产品拥有以下APIs：
1. 谜语查询

**注意，该示例代码仅适用于 [www.apishop.net](www.apishop.net "www.apishop.net") 网站下API**
**使用该产品前，您需要通过 [https://www.apishop.net/#/api/detail/?productID=95](https://www.apishop.net/#/api/detail/?productID=95 "https://www.apishop.net/#/api/detail/?productID=95") 申请API服务**

---

#### 1.谜语查询

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
url = "https://api.apishop.net/common/riddle/queryRiddle"
headers = None
params = {			
		"classID":"" #成语类别ID（1：字谜；2：动物谜语；3：灯谜；4：物品谜语；5：儿童谜语；6：植物谜语；7：趣味谜语；8：数字谜语；9：搞笑谜语；10：经典谜语；11：成语谜语），如“1”			
		"keyWord":"" #关键字，如“鸡”，为空则随机获取			
		"pageNum":"" #页码，如“1”，默认第一页			
		"pageSize":"" #每页条数，如“1”，最大为2
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