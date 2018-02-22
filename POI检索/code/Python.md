## POI检索 - Python调用示例代码

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
url = "https://api.apishop.net/common/POI/queryPOI"
headers = None
params = {			
		"keyWords":"" #查询关键字（规则： 多个关键字用“|”分割 若不指定city，并且搜索的为泛词（例如“美食”）的情况下，返回的内容为城市列表以及此城市内有多少结果符合要求。）			
		"types":"" #查询POI类型(多个类型用“|”分割； 可选值：分类代码 或 汉字（若用汉字，请严格按照附件之中的汉字填写） 分类代码由六位数字组成，一共分为三个部分，前两个数字代表大类；中间两个数字代表中类；最后两个数字代表小类。 若指定了某个大类，则所属的中类、小类都会被显示。 例如：010000为汽车服务（大类）              010100为加油站（中类）                 010101为中国石化（小类）              010900为汽车租赁（中类）             )			
		"city":"" #查询城市(可选值：城市中文、中文全拼  如：北京/beijing 填入此参数后，会尽量优先返回此城市数据，但是不一定仅局限此城市结果，若仅需要某个城市数据请调用citylimit参数。 如：在深圳市搜天安门，返回北京天安门结果。)			
		"cityLimit":"" #仅返回指定城市数据(可选值：true/false)			
		"children":"" #是否按照层级展示子POI数据(可选值：children=1)			
		"offset":"" #每页记录数据(强烈建议不超过25，若超过25可能造成访问报错)			
		"page":"" #当前页数(最大翻页数100)			
		"building":"" #建筑物的POI编号(传入建筑物POI编号之后，则只在该建筑物之内进行搜索)			
		"floor":"" #搜索楼层(若传入 建筑物的POI编号 + 楼层 ，则返回该建筑物内当前楼层的关键字搜索结果 若只传入楼层，则返回参数不完全的提示 若传入建筑物的POI编号 + 楼层，该楼层没有对应的搜索结果，则会返回建筑物之内的内容。)			
		"extensions":"" #此项默认返回基本地址信息；取值为all返回地址信息、附近POI、道路以及道路交叉口信息
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

#### 2.周边POI搜索

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
url = "https://api.apishop.net/common/POI/queryAroundPOI"
headers = None
params = {			
		"location":"" #中心点坐标（规则： 经度和纬度用","分割，经度在前，纬度在后，经纬度小数点后不得超过6位）			
		"keyWords":"" #查询关键字（规则： 多个关键字用“|”分割 若不指定city，并且搜索的为泛词（例如“美食”）的情况下，返回的内容为城市列表以及此城市内有多少结果符合要求。）			
		"types":"" #查询POI类型(多个类型用“|”分割； 可选值：分类代码 或 汉字（若用汉字，请严格按照附件之中的汉字填写） 分类代码由六位数字组成，一共分为三个部分，前两个数字代表大类；中间两个数字代表中类；最后两个数字代表小类。 若指定了某个大类，则所属的中类、小类都会被显示。 例如：010000为汽车服务（大类）              010100为加油站（中类）                 010101为中国石化（小类）              010900为汽车租赁（中类）             )			
		"city":"" #查询城市(可选值：城市中文、中文全拼  如：北京/beijing 填入此参数后，会尽量优先返回此城市数据，但是不一定仅局限此城市结果，若仅需要某个城市数据请调用citylimit参数。 如：在深圳市搜天安门，返回北京天安门结果。)			
		"radius":"" #查询半径（取值范围:0-50000。规则：大于50000按默认值，单位：米）			
		"sortrule":"" #排序规则（规定返回结果的排序规则。 按距离排序：distance；综合排序：weight，默认为distance）			
		"offset":"" #每页记录数据(强烈建议不超过25，若超过25可能造成访问报错)			
		"page":"" #当前页数(最大翻页数100)			
		"extensions":"" #此项默认返回基本地址信息；取值为all返回地址信息、附近POI、道路以及道路交叉口信息
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

#### 3.POI多边形搜索

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
url = "https://api.apishop.net/common/POI/queryPolygonPOI"
headers = None
params = {			
		"polygon":"" #经纬度坐标对（规则：经度和纬度用","分割，经度在前，纬度在后，坐标对用"|"分割。经纬度小数点后不得超过6位。         多边形为矩形时，可传入左上右下两顶点坐标对；其他情况下首尾坐标对需相同。）			
		"keyWords":"" #查询关键字（规则： 多个关键字用“|”分割 若不指定city，并且搜索的为泛词（例如“美食”）的情况下，返回的内容为城市列表以及此城市内有多少结果符合要求。）			
		"types":"" #查询POI类型(多个类型用“|”分割； 可选值：分类代码 或 汉字（若用汉字，请严格按照附件之中的汉字填写） 分类代码由六位数字组成，一共分为三个部分，前两个数字代表大类；中间两个数字代表中类；最后两个数字代表小类。 若指定了某个大类，则所属的中类、小类都会被显示。 例如：010000为汽车服务（大类）              010100为加油站（中类）                 010101为中国石化（小类）              010900为汽车租赁（中类）             )			
		"city":"" #查询城市(可选值：城市中文、中文全拼  如：北京/beijing 填入此参数后，会尽量优先返回此城市数据，但是不一定仅局限此城市结果，若仅需要某个城市数据请调用citylimit参数。 如：在深圳市搜天安门，返回北京天安门结果。)			
		"offset":"" #每页记录数据(强烈建议不超过25，若超过25可能造成访问报错)			
		"page":"" #当前页数(最大翻页数100)			
		"extensions":"" #此项默认返回基本地址信息；取值为all返回地址信息、附近POI、道路以及道路交叉口信息
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