## 全国天气预报 - Python调用示例代码

#### 全国天气预报
数据来自国家气象局，可根据地名、经纬度GPS、IP查询对应地区的预报。

#### 该产品拥有以下APIs：
1. 根据地区或id查询7天天气
2. 根据地名查询对应的id
3. 通过地区或id查询24小时天气
4. 地区或地区id查询未来15天天气
5. 根据地区或id查询历史天气
6. 根据景点查询天气
7. 根据GPS经纬度查询天气
8. 根据区号或邮编查询天气

**注意，该示例代码仅适用于 [www.apishop.net](www.apishop.net "www.apishop.net") 网站下API**
**使用该产品前，您需要通过 [https://www.apishop.net/#/api/detail/?productID=76](https://www.apishop.net/#/api/detail/?productID=76 "https://www.apishop.net/#/api/detail/?productID=76") 申请API服务**

---

#### 1.根据地区或id查询7天天气

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
url = "https://api.apishop.net/common/weather/getWeatherByArea"
headers = None
params = {			
		"need3HourForcast":"" #是否需要当天每3/6小时一次的天气预报列表：0代表不需要，1代表需要			
		"needAlarm":"" #是否需要天气预警：0代表不需要，1代表需要			
		"needHourData":"" #是否需要每小时数据的累积数组。本半小时刷一次实时状态，数组最大长度为48。每天0点长度初始化为0。0代表不需要，1代表需要			
		"needMoreDay":"" #是否需要返回7天数据中的后4天：0代表不需要，1代表需要			
		"area":"" #地区名：area和areaID参数必须二选一输入，如果都输入，以id为准			
		"areaID":"" #地区ID：area和areaID参数必须二选一输入，如果都输入，以id为准
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
---

#### 2.根据地名查询对应的id

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
url = "https://api.apishop.net/common/weather/getAreaID"
headers = None
params = {			
		"area":"" #地区名
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
---

#### 3.通过地区或id查询24小时天气

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
url = "https://api.apishop.net/common/weather/get24HoursWeatherByArea"
headers = None
params = {			
		"area":"" #地区名			
		"areaID":"" #地区ID
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
---

#### 4.地区或地区id查询未来15天天气

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
url = "https://api.apishop.net/common/weather/get15DaysWeatherByArea"
headers = None
params = {			
		"area":"" #地区名			
		"areaID":"" #地区ID
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
---

#### 5.根据地区或id查询历史天气

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
url = "https://api.apishop.net/common/weather/getHistoryWeatherByArea"
headers = None
params = {			
		"area":"" #地区名			
		"areaID":"" #地区ID			
		"month":"" #查询的月份：格式yyyyMM（数据自2015年1月开始)
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
---

#### 6.根据景点查询天气

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
url = "https://api.apishop.net/common/weather/getWeatherBySpot"
headers = None
params = {			
		"need3HourForcast":"" #是否需要当天每3/6小时一次的天气预报列表：0代表不需要，1代表需要			
		"needAlarm":"" #是否需要天气预警：0代表不需要，1代表需要			
		"needHourData":"" #是否需要每小时数据的累积数组：0代表不需要，1代表需要			
		"needIndex":"" #是否需要返回指数数据：0代表不需要，1代表需要			
		"needMoreDay":"" #是否需要返回7天数据中的后4天：0代表不需要，1代表需要			
		"spot":"" #景点名称
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
---

#### 7.根据GPS经纬度查询天气

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
url = "https://api.apishop.net/common/weather/getWeatherByGPS"
headers = None
params = {			
		"need3HourForcast":"" #是否需要当天每3/6小时一次的天气预报列表：0代表不需要，1代表需要			
		"needAlarm":"" #是否需要天气预警：0代表不需要，1代表需要			
		"needHourData":"" #是否需要每小时数据的累积数组：0代表不需要，1代表需要			
		"needIndex":"" #是否需要返回指数数据：0代表不需要，1代表需要			
		"needMoreDay":"" #是否需要返回7天数据中的后4天：0代表不需要，1代表需要			
		"from":"" #坐标来源： 1代表GPS设备获取的角度坐标、2代表GPS获取的米制坐标、sogou地图所用坐标;、3代表google地图、soso地图、aliyun地图、mapabc地图和amap地图所用坐标 、4代表3中列表地图坐标对应的米制坐标、5代表百度地图采用的经纬度坐标、6代表百度地图采用的米制坐标、7代表mapbar地图坐标			
		"lat":"" #经度：仅支持国内经纬度			
		"lng":"" #纬度：仅支持国内经纬度
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
---

#### 8.根据区号或邮编查询天气

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
url = "https://api.apishop.net/common/weather/getWeatherByPhonePostCode"
headers = None
params = {			
		"need3HourForcast":"" #是否需要当天每3/6小时一次的天气预报列表：0代表不需要，1代表需要			
		"needAlarm":"" #是否需要天气预警：0代表不需要，1代表需要			
		"needHourData":"" #是否需要每小时数据的累积数组：0代表不需要，1代表需要			
		"needIndex":"" #是否需要返回指数数据：0代表不需要，1代表需要			
		"needMoreDay":"" #是否需要返回7天数据中的后4天：0代表不需要，1代表需要			
		"phoneCode":"" #电话区号：电话区号和邮编必须二选一输入			
		"postCode":"" #邮编：电话区号和邮编必须二选一输入
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