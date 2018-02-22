## 公交、地铁路线规划 - Python调用示例代码

#### 公交、地铁路线规划
公交、驾车、骑行、步行查询，返回json格式的检索数据。

#### 该产品拥有以下APIs：
1. 路线查询

**注意，该示例代码仅适用于 [www.apishop.net](www.apishop.net "www.apishop.net") 网站下API**
**使用该产品前，您需要通过 [https://www.apishop.net/#/api/detail/?productID=105](https://www.apishop.net/#/api/detail/?productID=105 "https://www.apishop.net/#/api/detail/?productID=105") 申请API服务**

---

#### 1.路线查询

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
url = "https://api.apishop.net/common/direction/queryRoutes"
headers = None
params = {			
		"origin":"" #起点名称或经纬度，或者可同时提供名称和经纬度，此时经纬度优先级高，将作为导航依据，名称只负责展示。若为步行路径规划，则起终点间直线距离不应超过200公里。如“广州塔”、“广州塔|23.114155,113.318977”			
		"destination":"" #终点名称或经纬度，或者可同时提供名称和经纬度，此时经纬度优先级高，将作为导航依据，名称只负责展示。若为步行路径规划，则起终点间直线距离不应超过200公里。如“广州塔”、“广州塔|23.114155,113.318977”			
		"mode":"" #导航模式，包括：driving（驾车）、walking（步行）、transit（公交）、riding（骑行）,默认为transit			
		"region":"" #所在城市，公交、步行导航时该参数必填。			
		"origin_region":"" #起始点所在城市，驾车导航时必填。			
		"destination_region":"" #终点所在城市，驾车导航时必填。			
		"coord_type":"" #坐标类型，默认为bd09ll。允许的值为：bd09ll（百度经纬度坐标）、bd09mc（百度摩卡托坐标）、gcj02（国测局加密坐标）、wgs84（gps设备获取的坐标）。			
		"ret_coordtype":"" #可选，默认为百度经纬度坐标：bd09ll 可选值：  bd09ll：百度经纬度坐标  gcj02：国测局坐标			
		"waypoints":"" #途经点集合，包括一个或多个用竖线字符"|"分隔的地址名称或经纬度。支持驾车、步行方案，最多支持5个途经点。			
		"tactics":"" #导航策略,默认为12。可选值， 10：不走高速；  11：常规路线，即多数人常走的一条路线，不受路况影响，可用于用车估价；  12：距离较短，即距离相对较短的一条路线，但并不一定是一条优质路线；  13：躲避拥堵
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