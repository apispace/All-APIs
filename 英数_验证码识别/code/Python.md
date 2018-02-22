## 英数_验证码识别 - Python调用示例代码

#### 英数_验证码识别
纯数字，纯英文，数字+英文

#### 该产品拥有以下APIs：
1. 识别英数验证码

**注意，该示例代码仅适用于 [www.apishop.net](www.apishop.net "www.apishop.net") 网站下API**
**使用该产品前，您需要通过 [https://www.apishop.net/#/api/detail/?productID=109](https://www.apishop.net/#/api/detail/?productID=109 "https://www.apishop.net/#/api/detail/?productID=109") 申请API服务**

---

#### 1.识别英数验证码

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
url = "https://api.apishop.net/common/checkcode/recognizeVerifyCodeSimple"
headers = None
params = {			
		"convertJPG":"" #是否转换成jpg格式（有少量png或gif图转成jpg格式后识别率明显提高，但并不是所有png或gif转成jpg后都会提高识别率。） 此字段为1时表示需要把图片转为jpg格式，其他值不做转换操作。			
		"imgBASE64":"" #图片文件的base64字符串。图片大小需要小于100KB。			
		"codeType":"" #验证码类型,最大长度为10.(10:任意长度数字（成功率有所降低）,11~19:1~9位数字,20:任意长度英文（成功率有所降低）,21~29:1~9位英文,30:任意长度英数混合（成功率有所降低）,31~39:1~9位英数混合),如“34”代表4位英数混合
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