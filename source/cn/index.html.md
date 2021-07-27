---
title: Exchange官方API文档

language_tabs: # must be one of https://git.io/vQNgJ
  

toc_footers:

includes:
  
language: 简体中文

other_language: English

url: /wbfapi/en

present_url: /wbfapi/cn

active: active

other_active:

menu: 菜单

create_api: 创建 API Key

spot_goods: 现货

spot_goods_active: active

contract: 永续合约

contract_active: active

contract_url: 'https://doc.wbfutures.pro/index.html'

searchText: 搜索

search: true

code_clipboard: true
---

# 介绍

欢迎使用WBF开发者文档，此文档是WBF API的唯一官方文档. 

本文档提供了相关API的使用方法介绍。RESTful API包含了资产，订单及行情等接口。Websocket则提供了行情相关的接口及推送服务。WBF API提供的能力会在此持续更新，请大家及时关注。

你可以通过点击上方菜单来切换获取不同业务的API，还可通过点击右上方的语言按钮来切换文档语言。

文档右侧是针对请求参数以及响应结果的示例。


# 快速开始

## 接入准备
如需使用API，请先登录网页端，通过【用户中心】-【API】创建一个API key，再据此文档详情进行开发和交易。

您可以点击 [这里](https://www.wbfex.biz/personal/apiManagement) 创建 API Key。

每个用户可创建5组API Key，每组API key可以绑定5个不同的IP地址。API key一旦绑定了IP地址，则只能从绑定的IP地址使用该API key调用API接口。出于安全考虑，强烈建议您为API key绑定相应的IP地址。

创建成功后请务必记住以下信息：

- `API Key`  API 访问密钥
- `Secret Key` 签名认证加密所使用的密钥

## 接口类型
WBF为用户提供两种接口，您可根据自己的使用场景和偏好来选择适合的方式进行查询行情、交易。

**REST API**

REST，即Representational State Transfer的缩写，是一种流行的互联网传输架构。它具有结构清晰、符合标准、易于理解、扩展方便的，正得到越来越多网站的采用。其优点如右：

- 在RESTful架构中，每一个URL代表一种资源；
- 客户端和服务器之间，传递这种资源的某种表现层；
- 客户端通过四个HTTP指令，对服务器端资源进行操作，实现“表现层状态转化”。 

交易或资产等一次性操作，建议开发者使用REST API进行操作

**WebSocket API**

WebSocket是HTML5一种新的协议（Protocol）。它实现了客户端与服务器全双工通信，通过一次简单的握手就可以建立客户端和服务器连接，服务器可以根据业务规则主动推送信息给客户端。

市场行情和买卖深度等信息，建议开发者使用WebSocket API进行获取。

### 接口鉴权

以上两种接口均包含公共接口和私有接口两种类型。

公共接口可用于获取基础信息和行情数据。公共接口无需认证即可调用。

私有接口可用于交易管理和账户管理。每个私有请求必须使用您的API Key进行签名验证。

## 接入URLs

**REST API**

`https://openapi.wbfex.biz`

**WebSocket**

`wss://ws.wbfex.biz/kline-api/ws`

为保证API服务的稳定性，建议使用日本AWS云服务器进行访问。如使用中国大陆境内的客户端服务器，连接的稳定性将难以保证。


## <span id="a4">签名认证</span>

**签名说明**

API 请求在通过 internet 传输的过程中极有可能被篡改，为了确保请求未被更改，除公共接口（基础信息，行情数据）外的私有接口均必须使用您的 API Key 做签名认证，以校验参数或参数值在传输途中是否发生了更改。

**签名步骤(推荐)**
    
1、请求方法（GET 或 POST），后面添加换行符 “\n”

例如： `GET\n`

2、访问域名，后面添加换行符 "\n"

例如：`openapi.wbfex.biz\n`

3、访问接口路径，后面添加换行符 "\n"

例如：`/open/api/create_order\n`

4、将接口参数以其参数名的字典序升序进行排序并使用字符 "&" 连接
例如：

`api_key=b80609d133fd8baa0999f232c7d24331&price=17&side=BUY&symbol=abcusdt&time=1596253598000&type=1&volume=10`

5、组成最终要进行签名的字符串
例如：

`GET\n`

`openapi.wbfex.biz\n`

`/open/api/create_order\n`

`api_key=b80609d133fd8baa0999f232c7d24331&price=17&side=BUY&symbol=abcusdt&time=1596253598000&type=1&volume=10`

6、用最终 “请求字符串” 和你的密钥 (Secret Key) 生成一个数字签名
1. 将上一步得到的请求字符串和 API 私钥作为两个参数，调用HmacSHA256哈希函数来获得哈希值。
2. 将此哈希值用base-64编码，得到的值作为此次接口调用的数字签名。
   
`89ADx5A3TLyMWVQj8Gqp+N6w+ivaA8I5Oi2SuYtKKYo=`

7、将数字签名加入到请求参数中
`sign=89ADx5A3TLyMWVQj8Gqp+N6w+ivaA8I5Oi2SuYtKKYo=`


**签名步骤(向后兼容)**

出于安全考虑，不建议使用该签名方法

生成待签名的字符串
    -   [open-api Demo](#open-api-java)
    
1、先将参数以其参数名的字典序升序进行排序

2、遍历排序后的字典，将所有参数按"keyvalue"格式拼接在一起（非空参数）

`api_key1234567time1596250406402type1`

3、在上述参数字符串后面拼接上用户的Secret Key
`api_key1234567time1596250406402type109d133fd8baa0999f232c7d24331`

4、对上述字符串进行MD5得出签名字符串

`sign = md5("api_key1234567time1596250406402type109d133fd8baa0999f232c7d24331")`


# API接入说明

## <span id="a3">请求格式</span>
所有的API请求都是restful，目前只有两种方法：GET和POST。
- GET请求：所有的参数都在路径参数里
- POST请求，所有参数以JSON格式发送在请求主体（body）里

一个合法的请求由以下几部分组成：
- 方法请求地址：即访问服务器地址。https://openapi.wbfex.biz/open/api/create_order
- 必须和可选参数。

  以下几个参数是所有需要验签接口必须要提供的：
  - API key： 即用户申请的API Key中的Access Key。其字段名为api_key
  - 请求时间：即发起请求的时间。其字段名字为time
  - 签名：签名计算得出的值。其字段名字为sign

所有请求都必须包含以下Header：

- ACCESS-KEY API KEY作为一个字符串。
- ACCESS-SIGN 使用base64编码签名（请参阅签名消息）。
- ACCESS-TIMESTAMP 作为您的请求的时间戳。
- Content-Type  需要统一设置为:'application/x-www-form-urlencoded’。

## <span id="a3">返回格式</span>

所有的接口返回都是JSON格式。在JSON的第一层有3个字段code，msg和data。前两个字段表示请求状态和描述，实际的业务数据在data字段里。

```json
{
    "code": "0",
    "msg": "suc",
    "data": // per API response data in nested JSON object
}
```
以下是一个返回格式的样例：

| 字段 | 数据类型  | 描述       |
| ---- | -----  | ---------- |
| code | string | 返回状态 |
| msg  | string | 状态或错误描述 |
| data | object | 业务数据  |


## 错误信息

**HTTP状态码**

HTTP常见的错误码如下：
- 400 Bad Request – Invalid request forma 请求格式无效

- 401 Unauthorized – Invalid API Key 无效的API Key

- 403 Forbidden – You do not have access to the requested resource 请求无权限

- 404 Not Found 没有找到请求

- 429 Too Many Requests 请求太频繁被系统限流

- 500 Internal Server Error – We had a problem with our server 服务器内部错误

**业务状态码**

如果失败，response body 带有错误描述信息, 对应的状态码描述如下：

| 状态码 | 说明                             | 备注                       |
| ------ | -------------------------------- | -------------------------- |
| 0      | 成功                             | code=0 成功， code >0 失败 |
| 1      | 系统错误                         |                            |
| 2      | 参数错误                         |                            |
| 3      | 用户未登录                       |                            |
| 4      | 余额不足                         |                            |
| 5      | 下单失败                         |                            |
| 6      | 数量小于最小值                   |                            |
| 7      | 数量大于最大值                   |                            |
| 8      | 订单取消失败                     |                            |
| 9      | 交易被冻结                       |                            |
| 11     | 短信验证码错误或过期             |                            |
| 12     | 未知的代币代号                   |                            |
| 14     | 验证码错误                       |                            |
| 19     | 可用余额不足                     |                            |
| 21     | 谷歌验证码错误或过期             |                            |
| 22     | 订单不存在                       |                            |
| 25     | 价格或数量精度超过最大限制       |                            |
| 26     | 用户不存在                       |                            |
| 28     | 邮箱验证码错误或过期             |                            |
| 31     | 价格或者金额小于最小值           |                            |
| 32     | 价格超出当日涨跌停范围，无法下单 |                            |
| 33     | 订单委托量超出最大限制           |                            |
| 34     | 下单超过频率限制                 |                            |
| 10069  | 涨跌停限制币对,不允许下市价单    |                            |
| 100100 | 用户名或密码错误                 |                            |
| 100101 | 用户登陆锁定两小时               |                            |
| 100102 | 用户被冻结                       |                            |
| 101115 | 当前交易量超出用户单日交易上限   |                            |
| 101117 | 当前币种不允许市价交易           |                            |
| 101999 | 该币种未开启                     |                            |


## <span id="a9">标准规范</span>

### <span id="b1">时间戳</span> 
除非另外指定，API中的所有时间戳均以微秒为单位返回。

请求签名中的ACCESS-TIMESTAMP的单位是秒，允许用小数表示更精确的时间。请求的时间戳必须在API服务时间的30秒内，否则请求将被视为过期并被拒绝。如果本地服务器时间和API服务器时间之间存在较大的偏差，那么我们建议您使用通过查询API服务器时间来更新http header。

###  <span id="b2">例子</span> 
`1524801032573`

###  <span id="b3">数字</span> 
为了保持跨平台时精度的完整性，十进制数字作为字符串返回。建议您在发起请求时也将数字转换为字符串以避免截断和精度错误。

整数（如交易编号和顺序）不加引号。

###  <span id="b4">限流</span>  
如果请求过于频繁系统将自动限制请求，并在http header中返回429 too many requests状态码。

REST API

- 公共接口：我们通过IP限制公共接口的调用：每2秒最多6个请求。

- 私人接口：我们通过用户ID限制私人接口的调用：每秒最多10个请求。

- 某些接口的特殊限制在具体的接口上注明


# 账户相关


## <span id="1">资产余额</span>

获取用户资产余额

### HTTP请求: 
- GET /open/api/user/account


> 响应 

```json
{
    "code": "0",
    "msg": "suc",
    "data": {
        "total_asset":16822,
        "coin_list": [
            {
                "coin":"btc",
                "normal":32323.233,
                "locked":32323.233,
                "btcValuatin":112.33
            },
            {
                "coin":"ltc",
                "normal":68.2,
                "locked":69.2,
                "btcValuatin":112.33
            },       
        ]
    }
}
```

### 请求参数

| 参数    | 填写类型 | 说明    |
| ------- | -------- | ------- |
| api_key | 必填     | api_key |
| time    | 必填     | 时间戳  |
| sign    | 必填     | 签名    |

### 响应数据

| 字段 | 实例  | 解释       |
| ---- | ----- | ---------- |
| code | 0     |            |
| msg  | "suc" | code>0失败 |
| data | 如右: |

# 订单相关

## <span id="2">获取全部委托</span>

获取全部委托

### HTTP请求: 

- GET /open/api/v2/all_order

- 旧接口 /open/api/all_order 仍保留,但已经不建议使用
- v2版本变化: 去掉了结果返回值中的tradeList成交记录,提升效率;如果需要单一订单的成交信息,可以使用 /open/api/order_info 接口



> 响应

```json
{
    "code": "0",
    "msg": "suc",
    "data": {
        "count":10,
        "orderList":[
            {
                "side":"BUY",
                "total_price":"0.10000000",
                "created_at":1510993841000,
                "avg_price":"0.10000000",
                "countCoin":"btc",
                "source":1,
                "type":1,
                "side_msg":"买入",
                "volume":"1.000",
                "price":"0.10000000",
                "source_msg":"WEB",
                "status_msg":"完全成交",
                "deal_volume":"1.00000000",
                "id":424,
                "remain_volume":"0.00000000",
                "baseCoin":"eth",
                "status":2
            },
            ...
        ]
    }
}
```

### 请求参数

| 参数      | 填写类型 | 说明                                            |
| --------- | -------- | ----------------------------------------------- |
| symbol    | 必填     | 交易对，btcusdt                  |
| startDate | 选填     | （新增）开始时间，精确到秒“yyyy-MM-dd mm:hh:ss” |
| endDate   | 选填     | （新增）结束时间，精确到秒“yyyy-MM-dd mm:hh:ss” |
| pageSize  | 选填     | 页面大小                                        |
| page      | 选填     | 页码                                            |
| api_key   | 必填     | api_key                                    |
| time      | 必填     | 时间戳                                          |
| sign      | 必填     | 签名                                            |

### 响应数据

| 字段 | 实例  | 解释       |
| ---- | ----- | ---------- |
| code | 0     |            |
| msg  | "suc" | code>0失败 |
| data | 如右: |


## <span id="3">获取全部成交记录</span>
获取全部成交记录

### HTTP请求: 
- GET /open/api/all_trade


> 响应

```json
{
    "code":"0",
    "msg":"suc",
    "data":{
        "count":1000,
        "resultList":[
            {
                "side":"SELL",
                "role":"taker",
                "ask_user_id":10001,
                "fee":"0.50000000",
                "returnFeeStatus":false,
                "deal_price":"5.00",
                "type":"卖出",
                "bid_id":10001,
                "ask_id":10002,
                "volume":"1",
                "feeCoin":"USDT",
                "price":"5.00000000",
                "ctime":1608345691161,
                "id":10001,
                "bid_user_id":10001
            },
            ...
        ]
    }
}
```

### 请求参数

| 参数      | 填写类型 | 说明                                            |
| --------- | -------- | ----------------------------------------------- |
| symbol    | 必填     | 交易对，btcusdt                   |
| startDate | 选填     | （新增）开始时间，精确到秒“yyyy-MM-dd HH:mm:ss” |
| endDate   | 选填     | （新增）结束时间，精确到秒“yyyy-MM-dd HH:mm:ss” |
| pageSize  | 选填     | 页面大小                                        |
| page      | 选填     | 页码                                            |
| api_key   | 必填     | api_key                                      |
| time      | 必填     | 时间戳                                          |
| sort      | 选填     | 1表示倒序                                       |
| sign      | 必填     | 签名                                            |

### 响应数据

| 字段 | 实例  | 解释       |
| ---- | ----- | ---------- |
| code | 0     |            |
| msg  | "suc" | code>0失败 |
| data | 如右: |


##  <span id="4">取消委托单</span>
取消委托单

### HTTP请求: 
- POST /open/api/cancel_order

### 请求参数

| 参数     | 填写类型 | 说明                         |
| -------- | -------- | ---------------------------- |
| order_id | 必填     | 订单ID                       |
| symbol   | 必填     | 交易对，ethbtc |
| api_key  | 必填     | api_key                      |
| time     | 必填     | 时间戳                       |
| sign     | 必填     | 签名                         |

### 响应数据

| 字段 | 实例  | 解释       |
| ---- | ----- | ---------- |
| code | 0     |
| msg  | "suc" | code>0失败 |
| data | “”    |

##  <span id="5">取消全部委托单</span>
根据币对取消全部委托单（一次请求最多取消2000个订单，多于2000个订单需要重复调用该接口）

### HTTP请求: 
- POST /open/api/cancel_order_all

### 请求参数
| 参数    | 填写类型 | 说明                         |
| ------- | -------- | ---------------------------- |
| symbol  | 必填     | 交易对，ethbtc |
| api_key | 必填     | api_key                      |
| time    | 必填     | 时间戳                       |
| sign    | 必填     | 签名                         |

### 响应数据

| 字段 | 实例  | 解释       |
| ---- | ----- | ---------- |
| code | 0     |
| msg  | "suc" | code>0失败 |
| data | “”    |


## <span id="6">创建订单</span>
创建订单

### HTTP请求: 
- POST /open/api/create_order

### 请求参数

| 参数                      | 填写类型 | 说明                                                                                                                 |
| ------------------------- | -------- | -------------------------------------------------------------------------------------------------------------------- |
| side                      | 必填     | 买卖方向BUY、SELL                                                                                                    |
| type                      | 必填     | 挂单类型，1:限价委托、2:市价委托                                                                                     |
| volume                    | 必填     | 购买数量（多义，复用字段）<br>type=1:表示买卖数量<br>type=2:买则表示总价格，卖表示总个数<br>买卖限制user/me-用户信息 |
| price                     | 选填     | 委托单价：type=2：不需要此参数                                                                                       |
| symbol                    | 必填     | 交易对，ethbtc                                                                                                     |
| fee_is_user_exchange_coin | 选填     | （冗余字段，忽略）0，当交易所有平台币时，此参数表示是否使用用平台币支付手续费，0否，1是                              |
| api_key                   | 必填     | api_key                                                                                                              |
| time                      | 必填     | 时间戳                                                                                                               |
|  sign  | 必填 | 签名 |                                                                     


> 响应

```json
{
    "code": "0",
    "msg": "suc",
    "data": {
        "order_id":34343
    }
}
```                                 

### 响应数据

| 字段 | 实例               | 解释           |
| ---- | ------------------ | -------------- |
| code | 0                  |
| msg  | "suc"              | code>0失败     |
| data | 如右： | 成功返回交易ID |

## <span id="13">批量下单/撤单</span>
批量下单，也可同时批量撤回指定订单
### HTTP请求: 
- POST /open/api/mass_replace

### 请求参数

| 参数        | 填写类型 | 说明                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| ----------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| api_key     | 必填     | api_key                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| time        | 必填     | 时间戳                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| sign        | 必填     | 签名                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| symbol      | 必填     | 币种 ，例 btcusdt                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| mass_cancel | 选填     | [1234,234....] 撤单参数，订单id                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| mass_place  | 选填     | [{side:"BUY",type:"1",volume:"0.01",price:"6400",fee_is_user_exchange_coin:"0"}, {}, …]<br>含义：<br>symbol:币种，例btcusdt<br>mass_place:下单参数。side：方向（买卖方向BUY、SELL），<br>--------------------------------type：类型（1:限价委托、2:市价委托）<br>--------------------------------volume：购买数量（多义，复用字段） type=1:表示买卖数量type=2:买则表示总价格，卖表示总个数<br>--------------------------------price：委托单价：type=2：不需要此参数<br>--------------------------------fee_is_user_exchange_coin：(冗余字段)当交易所有平台币时，此参数表示是否使用用平台币支付手续费，0否，1是 |

* mass_place是需要发送到系统的一批限价订单，每次最多100条
* mass_cancel是需要撤回的一批订单,每次最多100条

> 响应

```json
{
    "code": "0",
    "msg": "suc",
    "data": {
        "mass_place": [
            {
                "order_id":"101180",
                "code":"0", 
                "msg":"suc"
            }
            ... ...
        ],
        "mass_cancel": [
            {
                "order_id":"101180",
                "code":"0", 
                "msg":"suc"
            }
            ... ...       
        ]
    }
}

```

### 响应数据
| 字段 | 实例  | 解释       |
| ---- | ----- | ---------- |
| code | 0     |
| msg  | "suc" | code>0失败 |
| data | 如右    | <br>下单返回：订单id，状态码，成功(0)/失败(1)<br> <br>撤单返回：订单id，状态码，成功(0)/失败(1)<br>|



## <span id="27">批量下单/批量撤单-V2</span>
批量下单，同时批量撤回指定订单

### HTTP请求: 
- POST /open/api/mass_replaceV2

### 请求参数
| 参数        | 填写类型 | 说明                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| ----------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| api_key     | 必填     | api_key                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| time        | 必填     | 时间戳                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| sign        | 必填     | 签名                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| symbol      | 必填     | 币种 ，例 btcusdt                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| mass_cancel | 选填     | [1234,234....] 撤单参数，订单id                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| mass_place  | 选填     | [{side:"BUY",type:"1",volume:"0.01",price:"6400",fee_is_user_exchange_coin:"0"}, {}, …]<br>含义：<br>symbol:币种，例btcusdt<br>mass_place:下单参数-->side：方向（买卖方向BUY、SELL），<br>--------------------------------type：类型（1:限价委托、2:市价委托）<br>--------------------------------volume：购买数量（多义，复用字段） type=1:表示买卖数量type=2:买则表示总价格，卖表示总个数<br>--------------------------------price：委托单价：type=2：不需要此参数<br>--------------------------------fee_is_user_exchange_coin：(冗余字段)当交易所有平台币时，此参数表示是否使用用平台币支付手续费，0否，1是 |

* mass_place是需要发送到系统的一批限价订单，每次最多1000条

* mass_cancel是需要撤回的一批订单,每次最多1000条


> 响应

```json
{
    "code": "0",
    "msg": "suc",
    "data": {
        "mass_place": [
            {
                "msg": "Success",
                "code": "0",
                "order_id": [504,505]
            },
            {
                "msg": "Order cancellation failed",
                "code": "8",
                "order_id": [504,505]
            }
        ],
        "mass_cancel": [
            {
                "msg": "Success",
                "code": "0",
                "order_id": [572,573,574,626,629]
            }
        ]
    }
}

```

### 响应数据

| 字段 | 实例  | 解释       |
| ---- | ----- | ---------- |
| code | 0     |
| msg  | "suc" | code>0失败 |
| data | 如右    | <br>下单返回：订单id，状态码，成功(0)/失败(1)<br> <br>撤单返回：订单id，状态码，成功(0)/失败(1)<br>|


## <span id="14">获取当前委托</span>
获取当前委托，包括未成交和部分成交的委托

### HTTP请求: 
- GET /open/api/v2/new_order

* 旧接口 /open/api/new_order 仍保留,但已经不建议使用

* v2版本变化: 去掉了结果返回值中的tradeList成交记录,提升效率;如果需要单一订单的成交信息,可以使用 /open/api/order_info 接口

> 响应

```json
{
    "code": "0",
    "msg": "suc",
    "data": {
        "count":10,
        "resultList":[
            {
                "side":"BUY",
                "total_price":"0.10000000",
                "created_at":1510993841000,
                "avg_price":"0.10000000",
                "countCoin":"btc",
                "source":1,
                "type":1,
                "side_msg":"买入",
                "volume":"1.000",
                "price":"0.10000000",
                "source_msg":"WEB",
                "status_msg":"完全成交",
                "deal_volume":"1.00000000",
                "id":424,
                "remain_volume":"0.00000000",
                "baseCoin":"eth",
                "status":2
            },
            ...
        ]
    }
}
```

### 请求参数:

| 参数     | 填写类型  | 说明                          |
| -------- | --------- | ----------------------------- |
| symbol    | 必填      | 交易对，btcusdt |
| pageSize | 选填      | 页面大小                      |
| page     | 选填      | 页码                          |
| api_key  | 必填      | api_key                       |
| time     | 必填      | 时间戳                        |
| sign     | 必填      | 签名                          |

### 响应数据:

| 字段 | 实例  | 解释                                                                                                                                                                                                                                          |
| ---- | ----- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| code | 0     |
| msg  | "suc" | code>0失败                                                                                                                                                                                                                                    |
| data | 如右: | 订单状态(status)说明：<br>INIT(0,"初始订单，未成交未进入盘口"),<br>NEW_(1,"新订单，未成交进入盘口"),<br>FILLED(2,"完全成交"),<br>PART_FILLED(3,"部分成交"),<br>CANCELED(4,"已撤单"),<br>PENDING_CANCEL(5,"待撤单"),<br>EXPIRED(6,"异常订单"); |


##  <span id="15">获取订单详情</span>
获取订单详情
### HTTP请求: 
- GET /open/api/order_info


> 响应

```json
{
    "code": "0",
    "msg": "suc",
    "data": {
        "order_info":{
            "id":343,
            "type":3,
            "side":"sell",
            "side_msg":"卖出",
            "created_at":"1592461624033",
            "price":222.33,
            "volume":222.33,
            "deal_volume":222.33,
            "total_price":222.33,
            "fee":222.33,
            "source":3,
            "source_msg":"API",
            "status":0, 
            "status_msg":"未成交",
            "avg_price":222.33}
        }
        "trade_list":[
            {
                "id":343,
                "created_at":"1592461624033",
                "price":222.33,
                "volume":222.33,
                "deal_price":222.33,
                "fee":222.33
            },
            {
                "id":345,
                "created_at":"1592461624033",
                "price":222.33,
                "volume":222.33,
                "deal_price":222.33,
                "fee":222.33
            }
        ]
    }
}
```

### 请求参数

| 参数     | 填写类型 | 说明                         |
| -------- | -------- | ---------------------------- |
| order_id | 必填     | 订单ID                       |
| symbol   | 必填     | 交易对，ethbtc |
| api_key  | 必填     | api_key                      |
| time     | 必填     | 时间戳                       |
| sign     | 必填     | 签名                         |

### 响应数据:

| 字段 | 实例  | 解释                                                                                                                                                                                                                                          |
| ---- | ----- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| code | 0     |
| msg  | "suc" | code>0失败                                                                                                                                                                                                                                    |
| data | 如右: | 订单状态(status)说明：<br>INIT(0,"初始订单，未成交未进入盘口"),<br>NEW_(1,"新订单，未成交进入盘口"),<br>FILLED(2,"完全成交"),<br>PART_FILLED(3,"部分成交"),<br>CANCELED(4,"已撤单"),<br>PENDING_CANCEL(5,"待撤单"),<br>EXPIRED(6,"异常订单"); |

# 行情相关
##  <span id="7">获取所有交易对行情</span>
获取所有交易对行情

```
curl https://openapi.wbfex.biz/open/api/get_allticker
```
### HTTP请求: 
- GET /open/api/get_allticker


> 响应

```json
{
    "code": "0",
    "msg": "suc",
    "data": {
    "ticker": [
        {
            "symbol": "btcusdt",
            "high": "11156.757",
            "vol": "25335.3953627840650451",
            "last": "10963.688",
            "low": "10842.3909",
            "buy": 10958.77,
            "sell": 10968,
            "change": "-0.0158573641955148",
            "rose": "-0.0158573641955148"
        },
        {
            "symbol": "wtusdt",
            "high": "0.1531",
            "vol": "13960810.11",
            "last": "0.15207",
            "low": "0.150962",
            "buy": 0.151699,
            "sell": 0.152843,
            "change": "-0.0061368939082015",
            "rose": "-0.0061368939082015"
        },
        ...
    ]
}
 ```


### 请求参数: 
* 该接口不进行签名校验
* 参数:无

### 响应数据: 
| 字段 | 实例   | 解释                                                                                                                                                                                                                                    |
| ---- | ------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| code | 0      |
| msg  | "suc"  | code>0失败                                                                                                                                                                                                                              |
| data | 如右： | 返回值说明<br>date: 返回数据时服务器时间 <br>symbol: 交易对（交易对1(base)简称_交易对2(quote)简称） <br>buy: 买一价 <br>high: 最高价 <br>last: 最新成交价 <br>low: 最低价 <br>sell: 卖一价 <br>vol: 成交量(最近的24小时)<br>rose:涨跌幅 |

## <span id="8">获取K线数据</span>
获取K线数据

```
curl https://openapi.wbfex.biz/open/api/get_records?symbol=btcusdt&period=15
```

### HTTP请求:

GET /open/api/get_records


> 响应

```json
{
    "code": "0",
    "msg": "suc",
    "data": [
        [
            1595727000,  //时间，秒
            9674.4758,   //Open
            9675.8867,   //High
            9652.9081,   //Low
            9654.2835,   //Close
            369.67841    //成交量
        ],
        [
            1595727900,
            9654.2835,
            9658.4342,
            9652.6404,
            9658.0308,
            159.483355
        ],
        [
            1595728800,
            9658.0308,
            9658.9791,
            9641.8814,
            9649.003,
            80.145183
        ],
        ...
    ]
}
```
### 请求参数:
* 该接口不进行签名校验

| 参数   | 填写类型 | 说明                                     |
| ------ | -------- | ---------------------------------------- |
| symbol | 必填     | 交易对，bchbtc             |
| period | 必填     | 单位为分钟，比喻1分钟则为1，一天则为1440 |

### 响应数据:

| 字段 | 实例   | 解释       |
| ---- | ------ | ---------- |
| code | 0      |
| msg  | "suc"  | code>0失败 |
| data | 如右： |


##  <span id="9">获取当前行情</span>
获取当前行情

```
curl https://openapi.wbfex.biz/open/api/get_ticker?symbol=btcusdt
```

### HTTP请求:
- GET /open/api/get_ticker

> 响应

```json
{
    "code": "0",
    "msg": "suc",
    "data": {
        "high": "11156.757",
        "vol": "25397.4778447840650451",
        "last": "10958.1839",
        "low": "10842.3909",
        "buy": 10952.15,
        "sell": 10964.4335,
        "rose": "-0.0163514332972378",
        "time": 1514448473626
    }
}
```

### 请求参数:
* 该接口不进行签名校验

| 参数   | 填写类型 | 说明                          |
| ------ | -------- | ----------------------------- |
| symbol | 必填     | 交易对，btcusdt |


### 响应数据:

| 字段 | 实例   | 解释       |
| ---- | ------ | ---------- |
| code | 0      |
| msg  | "suc"  | code>0失败 |
| data | 如右： |


## <span id="10">获取行情成交记录</span>
获取交易对的实时成交记录(最大返回200条最新成交记录)

```
curl https://openapi.wbfex.biz/open/api/get_trades?symbol=btcusdt
```

### HTTP请求:
- GET /open/api/get_trades

> 响应

```json
{
    "code": "0",
    "msg": "suc",
    "data": [
        {
            "symbol": "btcusdt",
            "count_coin": "USDT",
            "amount_precision": 6,
            "base_coin": "BTC",
            "price_precision": 4
        },
        {
            "symbol": "wtusdt",
            "count_coin": "USDT",
            "amount_precision": 2,
            "base_coin": "WT",
            "price_precision": 6
        },
        {
            "symbol": "ltcusdt",
            "count_coin": "USDT",
            "amount_precision": 4,
            "base_coin": "LTC",
            "price_precision": 2
        },
        ...
    ]
}
```
### 请求参数:

* 该接口不进行签名校验

| 参数   | 填写类型 | 说明                         |
| ------ | -------- | ---------------------------- |
| symbol | 必填     | 交易对，bchbtc |

### 响应数据:

| 字段 | 实例   | 解释       |
| ---- | ------ | ---------- |
| code | 0      |
| msg  | "suc"  | code>0失败 |
| data | 如右： |


##  <span id="11">获取各个币对的最新成交价</span>
获取各个币对的最新成交价

```
curl https://openapi.wbfex.biz/open/api/market
```

### HTTP请求:

- GET /open/api/market

> 响应

```json
{
    "code": "0",
    "msg": "suc",
    "data": {
        "btcusdt": 15000,
        "ethusdt": 800，
        "eosusdt": 200
        ... ...
    }   
}
```
### 请求参数:
* 该接口不进行签名校验

### 响应数据:

| 字段 | 实例                            | 解释       |
| ---- | ------------------------------- | ---------- |
| code | 0                               |
| msg  | "suc"                           | code>0失败 |
| data | 如右: |



##  <span id="12">查询买卖盘深度</span>
查询买卖盘深度

```
curl https://openapi.wbfex.biz/open/api/market_dept?symbol=btcusdt&type=step0
```

### HTTP请求:
- GET /open/api/market_dept


> 响应

```json
{
    "code": "0",
    "msg": "suc",
    "data": { 
        "tick":{
            "asks":[//卖盘，价格由小到大
                [10968.4775,0.9332],
                [10985.9564,0.1222],
                [10993.3291,0.2778],
                [10995.8029,0.5882],
                [10998.8093,0.2982],
                ...
            ],
            "bids":[//买盘, 价格由大到小
                [10953.0857,0.1532],
                [10950,0.0226],
                [10942.8882,0.3178],
                [10941.0563,0.1862],
                [10938.2499,0.0912],
                ...
            ],
            "time": 1595995928119
        }
    }
}
```

### 请求参数:
* 该接口不进行签名校验

| 参数   | 填写类型 | 说明                                                            |
| ------ | -------- | --------------------------------------------------------------- |
| symbol | 必填     | 交易对，ethbtc                                    |
| type   | 必填     | 深度类型，step0, step1, step2（合并深度0-2）；step0时，精度最高 |

### 响应数据:

| 字段 | 实例   | 解释       |
| ---- | ------ | ---------- |
| code | 0      |
| msg  | "suc"  | code>0失败 |
| data | 如右： |


## <span id="17">查询系统支持的所有交易对及精度</span>
查询系统支持的所有交易对及精度

```
curl https://openapi.wbfex.biz/open/api/common/symbols
```

### HTTP请求:

- GET /open/api/common/symbols


> 响应

```json
{
    "code": "0",
    "msg": "suc",
    "data": [
        {
        "symbol": "btcusdt",
        "count_coin": "USDT",
        "amount_precision": 6,
        "base_coin": "BTC",
        "price_precision": 4
        },
        {
        "symbol": "wtusdt",
        "count_coin": "USDT",
        "amount_precision": 2,
        "base_coin": "WT",
        "price_precision": 6
        },
        {
        "symbol": "wptusdt",
        "count_coin": "USDT",
        "amount_precision": 4,
        "base_coin": "WPT",
        "price_precision": 6
        },
        ...
    ]
}
```

### 请求参数:
* 参数:无

### 响应数据:

| 字段 | 实例   | 解释                                                                                                                                              |
| ---- | ------ | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| code | 0      |
| msg  | "suc"  | code>0失败                                                                                                                                        |
| data | 如右： | symbol 交易对<br>base_coin 基础币种<br>count_coin 计价货币<br>price_precision 价格精度位数（0为个位）<br>amount_precision 数量精度位数（0为个位） |


# Websocket行情数据

## 简介

### 接入URL

`
wss://ws.wbfex.biz/kline-api/ws
`

### 数据压缩
WebSocket 行情接口返回的所有数据都进行了 GZIP 压缩，需要 client 在收到数据之后解压。

### 心跳消息
当用户的Websocket客户端连接到WBF的Websocket服务器后，服务器会定期（当前设为5秒）向其发送ping消息并包含一整数值如右：

`
{"ping": 1595935723334}
`

当用户的Websocket客户端接收到此心跳消息后，应返回pong消息并包含同一整数值：

`
{"pong": 1595935723334}
`

> 订阅

```json
{
    "event": "sub", 
    "params":{
        "channel":"market_$base$quote_kline_1min",
        "cb_id":"10001"
    }
}
```


当Websocket服务器连续3次发送了`ping`消息却没有收到任何一次`pong`消息返回后，服务器将主动断开与此客户端的连接。


> 订阅 响应

```json
{
    "event_rep":"subed",
    "channel":"market_$base$quote_kline_1min",
    "cb_id":"10001",
    "ts":1506584998239,
    "status":"ok"
}
```

### 订阅主题
成功建立与Websocket服务器的连接后，Websocket客户端发送如右请求以订阅特定主题。

`
{
    "event": "sub", 
    "params":{
        "channel":"channel name",
        "cb_id":"id generated by client"
    }
}
`

成功订阅后，Websocket客户端将收到确认。之后, 一旦所订阅的主题有更新，Websocket客户端将收到服务器推送的更新消息（push）

> 取消订阅

```json
{
    "event": "unsub", 
    "params":{
        "channel":"market_$base$quote_kline_1min",
        "cb_id":"10001"
    }
}
```

`
{
    "event": "subed", 
    "channel":"channel name",
    "cb_id":"id generated by client",
    "ts": "timestamp"
    "status": "subscribe status"
}
`

### 取消订阅
客户端取消订阅的格式：

`
{
    "event": "unsub", 
    "params":{
        "channel":"channel name",
        "cb_id":"id generated by client"
    }
}
`

## <span id="19">订阅-K线行情</span>

### channel
`market_$symbol_kline_$period`

> 订阅

```json
{
    "event":"sub",
    "params":{
        "channel":"market_btcusdt_kline_5min",
        "cb_id":"10001"
    }
}
```
### 订阅参数

| 参数 | 数据类型  | 是否必须  | 描述 | 取值范围 |
| ---- | ------ | ---------- | ---- | ----- |
| channel | string | 是  | 主题 |$symbol: 交易对名称<br>btcusdt,ethusdt ...</br> </br> <br>$period: K线周期</br> <br>1min,5min,15min,30min,60min,1day,1week,1month</br>|
| cb_id | string | 是  | 自定义id      | |

> 响应

```json
{
    "channel":"market_btcusdt_kline_5min",
    "ts":1506584998239,//请求时间
    "tick":{
        "id":1506602880,//时间刻度起始值
        "amount":123.1221,//交易额
        "vol":1212.12211,//交易量
        "open":2233.22,//开盘价
        "close":1221.11,//收盘价
        "high":22322.22,//最高价
        "low":2321.22//最低价
    }
}
```
### 更新数据字段
| 字段 | 数据类型 | 描述 |
|-----|-----|-----|
| id | long  | 时间刻度起始值 |
| amount | float  | 交易额 |
| vol | float  | 交易量 |
| open | float  | 开盘价 |
| close | float  | 收盘价 |
| high | float  | 最高价 |
| low | float  | 最低价 |




## <span id="20">订阅-24小时行情</span>

### channel
`maket_$symbol_ticker`

> 订阅

```json
{
    "event":"sub",
    "params":{
        "channel":"market_btcusdt_ticker",
        "cb_id":"10002"
    }
}
```

### 订阅参数

| 参数 | 数据类型  | 是否必须  | 描述 | 取值范围 |
| ---- | ------ | ---------- | ---- | ----- |
| channel | string | 是  | 主题 |$symbol: 交易对名称 <br>btcusdt,ethusdt ...</br>|
| cb_id | string | 是  | 自定义id      | |


> 响应

```json
{
    "channel":"market_btcusdt_ticker",
    "ts":1506584998239,//请求时间
    "tick":{
        "id":1506584998,//冗余，无实际意义，时间戳
        "amount":123.1221,//交易额
        "vol":1212.12211,//交易量
        "open":2233.22,//开盘价
        "close":1221.11,//收盘价
        "high":22322.22,//最高价
        "low":2321.22,//最低价
        "rose":-0.2922,//涨幅
        "ts":1506584998239,//数据产生时间
        "lower_frame":"0"
    }
}
```

### 更新数据字段
| 字段 | 数据类型 | 描述 |
|-----|-----|-----|
| id | long  | 时间刻度起始值 |
| amount | float  | 交易额 |
| vol | float  | 交易量 |
| open | float  | 开盘价 |
| close | float  | 收盘价 |
| high | float  | 最高价 |
| low | float  | 最低价 |



##  <span id="21">订阅-市场深度行情数据</span>

### channel
`maket_$symbol_depth_$step`

> 订阅

```json
{
    "event":"sub",
    "params":{
        "channel":"market_btcusdt_depth_step0",
        "cb_id":"10001",
        "asks":150,
        "bids":150
    }
}
```

### 订阅参数

| 参数 | 数据类型  | 是否必须  | 描述 | 取值范围 |
| ---- | ------ | ---------- | ---- | ----- |
| channel | string | 是  | 主题 |$symbol: 交易对名称 <br>btcusdt,ethusdt ...</br> </br> <br>$step: 聚合精度</br> <br> step0: 不聚合 </br> <br>step1: 系统精度可配置</br> <br>step2: 系统精度可配置<br>|
| cb_id | string | 是  | 自定义id      | |
| asks | integer | 否  | 卖盘档位     | |
| bids | integer | 否  | 买盘档位     | |


### 更新数据字段

* 注：第一次订阅成功会立刻返回一次全量数据， 另外server也会定期推个全量数据给前端  避免数据出问题


> 全量盘口响应

```json
{
    "channel":"market_btcusdt_depth_step0",
    "ts":1506584998239,//请求时间
    "tick":{
        "asks":[//卖盘
            [22112.22,0.9332],
            [22112.21,0.2],
        ],
        "buys":[//买盘
            [22111.22,0.9332],
            [22111.21,0.2],
        ]
    }
}
```
#### 全量盘口数据

* 可直接替换原有盘口

| 字段 | 数据类型 | 描述 |
|-----|-----|-----|
| asks | 数组对象  | 卖盘深度 |
| buys | 数组对象  | 买盘深度 |



> 增量盘口响应

```json
{
    "channel":"market_btcusdt_depth_step0",
    "ts":1506584998239,//请求时间
    "tick":{
        "side": "asks", 买卖盘方向  asks： 卖盘  buys: 买盘  
        "price" : 133.55,  盘口对应的某个价格段
        "volume" : 44.22   价格段对应的数量
    }
}
```

#### 增量盘口数据

* 只需要替换价格对应的数量即可， volume=0时删除， price与原有盘口某个价格段的price相等时更新volume, 新的price直接新增

| 字段 | 数据类型 | 描述 |
|-----|-----|-----|
| side | string  | 买卖盘方向 <br>asks: 卖盘</br> <br>buys</br>|
| price | float  | 价格 |
| volume | float  | 数量 |


## <span id="23">订阅-实时成交信息 </span>

### channel
`maket_$symbol_trade_ticker`


> 订阅

```json
{
    "event":"sub",
    "params":{
        "channel":"market_btcusdt_trade_ticker",
        "cb_id":"10001"
    }
}
```

### 订阅参数

| 参数 | 数据类型  | 是否必须  | 描述 | 取值范围 |
| ---- | ------ | ---------- | ---- | ----- |
| channel | string | 是  | 主题 |$symbol: 交易对名称<br>btcusdt,ethusdt ...</br>|
| cb_id | string | 是  | 自定义id      | |

> 响应

```json
{
    "channel":"market_btcusdt_trade_ticker",
    "ts":1506584998239,//请求时间
    "tick":{
        "id":12121,//data中最大交易ID
        "ts":1506584998239,//data中最大时间
        "data":[
            {
                "id":12121,//交易ID
                "side":"buy",//买卖方向buy,sell
                "price":32.233,//单价
                "vol":232,//数量
                "amount":323,//总额
                "ts":1506584998239,//数据产生时间
                "ds":'2017-09-10 23:12:21'
            },
            {
                "id":12120,//交易ID
                "side":"buy",//买卖方向buy,sell
                "price":32.233,//单价
                "vol":232,//数量
                "amount":323,//总额
                "ts":1506584998239,//数据产生时间
                "ds":'2017-09-10 23:12:21'
            }
        ]
    }
}
```

### 更新数据字段
| 字段 | 数据类型 | 描述 |
|-----|-----|-----|
| id  | long | 交易id |
| side | string  | 买卖盘方向 <br>asks: 卖盘</br> <br>buys</br>|
| price | float  | 成交价格 |
| vol | float  | 成交数量 |
| amount | float  | 成交总额 |
| ts | long  | 成交时间 |
| ds | string  | 日期 |




## <span id="24">请求-K线历史数据</span>


### channel
`maket_$symbol_kline_$period`

* 增加请求参数endIdx，pageSize（最多300，默认300条数据）,如果endIdx为空，则返回最近300条历史数据


> 订阅

```json
{
    "event":"req",
    "params":{
        "channel":"market_btcusdt_kline_5min",
        "cb_id":"10002",
        "since":"1506602880"
    }
}
```

### 订阅参数

| 参数 | 数据类型  | 是否必须  | 描述 | 取值范围 |
| ---- | ------ | ---------- | ---- | ----- |
| channel | string | 是  | 主题 |$symbol: 交易对名称<br>btcusdt,ethusdt ...</br> </br> <br>$period: K线周期</br> <br>1min,5min,15min,30min,60min,1day,1week,1month</br>|
| cb_id | string | 是  | 自定义id      | |
| since | long | 否 | 自定义id      | since缺省时返回最新300条，有值时返回大于since的最多1小时数据，since有强校验，不能早于当前1小时  since取到59|

> 响应

```json
{
    "event_rep":"rep","channel":"market_btcusdt_kline_5min",
    "cb_id":"10001",
    "since":"1506602880",
    "ts":1506584998239,//请求时间
    "data":[
        {
            "id":1506602880,//时间刻度起始值
            "amount":123.1221,//交易额
            "vol":1212.12211,//交易量
            "open":2233.22,//开盘价
            "close":1221.11,//收盘价
            "high":22322.22,//最高价
            "low":2321.22//最低价
        },
        {
            "id":1506602880,//时间刻度起始值
            "amount":123.1221,//交易额
            "vol":1212.12211,//交易量
            "open":2233.22,//开盘价
            "close":1221.11,//收盘价
            "high":22322.22,//最高价
            "low":2321.22//最低价
        }
    ]
}
```

### 更新数据字段
| 字段 | 数据类型 | 描述 |
|-----|-----|-----|
| id  | long | 交易id |
| vol | float  | 交易量 |
| amount | float  | 交易额 |
| open | long  | 开盘价 |
| close | long  | 收盘价 |
| high | long  | 最高价 |
| low | long  | 最低价 |



## <span id="25">请求-成交历史数据 </span>

### channel
`maket_$symbol_trade_ticker`


> 订阅

```json
{
    "event":"req","params":
    {
        "channel":"market_$base$quote_trade_ticker",
        "cb_id":"自定义",
        "top":200
     }
}
```

### 订阅参数

| 参数 | 数据类型  | 是否必须  | 描述 | 取值范围 |
| ---- | ------ | ---------- | ---- | ----- |
| channel | string | 是  | 主题 |$symbol: 交易对名称<br>btcusdt,ethusdt ...</br>|
| cb_id | string | 是  | 自定义id      | |

> 响应

```json
{
    "event_rep":"rep",
    "channel":"market_btcusdt_trade_ticker",
    "cb_id":"10001",
    "ts":1506584998239,
    "status":"ok",
    "top":200,//最大支持200
    "data":[
        {
            "id":12121,//交易ID
            "side":"buy",//买卖方向buy,sell
            "price":32.233,//单价
            "vol":232,//数量
            "amount":323,//总额
            "ts":1506584998239//数据产生时间
        },
        {
            "id":12120,//交易ID
            "side":"buy",//买卖方向buy,sell
            "price":32.233,//单价
            "vol":232,//数量
            "amount":323,//总额
            "ts":1506584998239,//数据产生时间
            "ds":'2017-09-10 23:12:21'
        }
    ]
}
```
### 更新数据字段
| 字段 | 数据类型 | 描述 |
|-----|-----|-----|
| id  | long | 交易id |
| side | string  | 买卖盘方向 <br>asks: 卖盘</br> <br>buys</br>|
| price | float  | 成交价格 |
| vol | float  | 成交数量 |
| amount | float  | 成交总额 |
| ts | long  | 成交时间 |


## <span id="26">请求-首页24行情数据</span>

### channel
`review`

> 订阅

```json
{"event":"req","params":{"channel":"review"}}
```
### 订阅参数

| 参数 | 数据类型  | 是否必须  | 描述 | 取值范围 |
| ---- | ------ | ---------- | ---- | ----- |
| channel | string | 是  | 主题 | review|

> 响应

```json
{
   "event_rep": "rep",
   "channel": "review",
   "data": {
        "bchbtc":{
            "amount": 115.968833484,
            "close": 0.07956,
            "high": 0.086323,
            "low": 0.079251,
            "open": 0.086323,
            "rose": -0.07834528,
            "vol": 1393.247
        },
        "btcusdt":{
            "amount": 115.968833484,
            "close": 11110.07956,
            "high": 11120.086323,
            "low": 11000.079251,
            "open": 10999.086323,
            "rose": 110.07834528,
            "vol": 111393.247
        }          
   }
}
```

### 更新数据字段
| 字段 | 数据类型 | 描述 |
|-----|-----|-----|
| amount  | long | 成交额 |
| close  | float  | 收盘价|
| high | float  | 最高价 |
| low | float  | 最低价 |
| open | float  | 开盘价 |
| rose | float  | 涨跌幅 |
| vol | float  | 成交量 |


## <span id="open-api-java">open-api Demo（java） </span>


```java

package com.exchange.stats.action;

import java.io.InputStream;
import java.io.UnsupportedEncodingException;
import java.security.MessageDigest;
import java.security.NoSuchAlgorithmException;
import java.util.Date;
import java.util.Map;
import java.util.Map.Entry;
import java.util.Set;
import java.util.TreeMap;

import org.apache.commons.httpclient.HttpClient;
import org.apache.commons.httpclient.methods.GetMethod;
import org.apache.commons.httpclient.methods.PostMethod;
import org.apache.commons.io.IOUtils;

public class Main {
    /**
     * @param args
     */
    public static void main(String[] args) {
/** 4个变量，api_key,secret_key */ 
 String api_key = "9750*****************65d1";
 String secret_key = "1c16******************2982";

        /** 封装需要签名的参数 */
        TreeMap<String, String> params = new TreeMap<String, String>();
        params.put("api_key", api_key);
        params.put("time", new Date().getTime() + "");
        /** 拼接签名字符串，md5签名 */
        StringBuilder result = new StringBuilder();
        Set<Entry<String, String>> entrys = params.entrySet();
        for (Entry<String, String> param : entrys) {
            /** 去掉签名字段 */
            if (param.getKey().equals("sign")) {
                continue;
            }https://openapi.wbf.info 

            /** 空参数不参与签名 */
            if (param.getValue() != null) {
                result.append(param.getKey());
                result.append(param.getValue().toString());
            }
        }
        result.append(secret_key);
        String sign = getMD5(result.toString());
        params.put("sign", sign);

        /** http请求 */
        String resultJson = get("https://openapi.wbfex.com/open/api/get_trades?symbol=btcusdt", params);
        System.out.println(resultJson);
    }

    /**
     * 通过post来提交数据，带参数的方法
     *
     * @param url    请求地址
     * @param params 参数
     * @return
     */
    public static String post(String url, Map<String, String> params) {
        String str = null;
        try {
            HttpClient client = new HttpClient();
            PostMethod method = new PostMethod(url);
            //设定请求头的样式 
            method.setRequestHeader("Content-Type", "application/x-www-form-urlencoded;charset=utf-8");
            if (params != null && params.size() > 0) {
                for (Map.Entry<String, String> entry : params.entrySet()) {
                    method.setParameter(entry.getKey(), entry.getValue());
                }
            }
            int code = client.executeMethod(method);
            if (code >= 200 && code < 300) {
                InputStream in = method.getResponseBodyAsStream();
                str = IOUtils.toString(in);
            }
        } catch (Exception e) {
// TODO Auto-generated catch block 
            e.printStackTrace();
        }
        return str;
    }

    /**
     * 通过get来提交数据，带参数的方法
     *
     * @param url    请求地址
     * @param params 参数
     * @return
     */
    public static String get(String url, Map<String, String> params) {
        String str = null;
        try {
            if (params != null && params.size() > 0) {
                int x = 1;
                for (Map.Entry<String, String> entry : params.entrySet()) {
                    if (x == 1) {
                        url = url + "?";
                    } else {
                        url = url + "&";
                    }
                    url += entry.getKey() + "=" + entry.getValue();
                    x++;
                }
            }
            HttpClient client = new HttpClient();
            System.out.println("url:::" + url);
            GetMethod method = new GetMethod(url);
            //设定请求头的样式 
            method.setRequestHeader("Content-Type", "application/x-www-form-urlencoded;charset=utf-8");
            int code = client.executeMethod(method);
            if (code >= 200 && code < 300) {
                InputStream in = method.getResponseBodyAsStream();
                str = IOUtils.toString(in);
            }
        } catch (Exception e) {
            // TODO Auto-generated catch block 
            e.printStackTrace();
        }
        return str;
    }

    /**
     * 获取String的MD5值
     *
     * @param info 字符串
     * @return 该字符串的MD5值
     */
    public static String getMD5(String info) {
        try {
            //获取 MessageDigest 对象，参数为 MD5 字符串，表示这是一个 MD5 算法（其他还有 SHA1 算法等）： 
            MessageDigest md5 = MessageDigest.getInstance("MD5");
            
            //update(byte[])方法，输入原数据 
            //类似StringBuilder对象的append()方法，追加模式，属于一个累计更改的过程 
            md5.update(info.getBytes("UTF-8"));
            
            //digest()被调用后,MessageDigest对象就被重置，即不能连续再次调用该方法计算原数据的MD5值。可以手动调用reset()方法重置输入源。 
            //digest()返回值16位长度的哈希值，由byte[]承接 
            byte[] md5Array = md5.digest();
            
            //byte[]通常我们会转化为十六进制的32位长度的字符串来使用,本文会介绍三种常用的转换方法 
            return bytesToHex(md5Array);
        } catch (NoSuchAlgorithmException e) {
            return "";
        } catch (UnsupportedEncodingException e) {
            return "";
        }
    }

    private static String bytesToHex(byte[] md5Array) {
        StringBuilder strBuilder = new StringBuilder();
        for (int i = 0; i < md5Array.length; i++) {
            int temp = 0xff & md5Array[i];
            String hexString = Integer.toHexString(temp);
            if (hexString.length() == 1) {//如果是十六进制的0f，默认只显示f，此时要补上0
                strBuilder.append("0").append(hexString);
            } else {
                strBuilder.append(hexString);
            }
        }
        return strBuilder.toString();
    }

}

```


## <span id="ws-api-java">ws-api Demo (java) </span>


```java

package test;

import java.io.ByteArrayInputStream;
import java.io.ByteArrayOutputStream;
import java.io.IOException;
import java.net.URI;
import java.nio.ByteBuffer;
import java.nio.CharBuffer;
import java.nio.charset.Charset;
import java.nio.charset.CharsetDecoder;
import java.security.cert.CertificateException;
import java.security.cert.X509Certificate;
import java.util.HashMap;
import java.util.Map;
import java.util.zip.GZIPInputStream;

import javax.net.ssl.SSLContext;
import javax.net.ssl.TrustManager;
import javax.net.ssl.X509TrustManager;

import org.java_websocket.client.DefaultSSLWebSocketClientFactory;
import org.java_websocket.client.WebSocketClient;
import org.java_websocket.drafts.Draft;
import org.java_websocket.drafts.Draft_17;
import org.java_websocket.handshake.ServerHandshake;

/**
 * @author 鲫鱼哥 DateTime:2018年11月22日 下午9:25:20 
 * 建议使用的websocket client版本 
 * <dependency> 
 * <groupId>org.java-websocket</groupId> 
 * <artifactId>Java-WebSocket</artifactId> 
 * <version>1.3.0</version> 
 * </dependency> 
 *
 */
public class WsTest {

    public static void main(String[] args) {
        try {
//wsurl 
            String url = "wss://ws.wbfex.biz/kline-api/ws";
//历史数据请求参数 
            String reqParam = "{"event":"req","params":{"channel":"market_btcusdt_trade_ticker","cb_id":"btcusdt","top":150}}";
//订阅参数 
            String subParam = "{"event":"sub","params":{"channel":"market_btcusdt_trade_ticker","cb_id":"btcusdt","top":150}}";

//初始化请求历史数据 
            WebSocketUtils wsc = WebSocketUtils.executeWebSocket(url, reqParam);

//订阅实时数据 
            wsc.send(subParam);

//线程不结束，等待新的消息，一般一分钟左右会有新的成交返回
            while (true) {
                Thread.sleep(1000);
            }

        }catch (Exception e) {
            e.printStackTrace();
        }
    }

    static class WebSocketUtils extends WebSocketClient {
        private static WebSocketUtils wsclient = null;
        private String msg = "";

        public WebSocketUtils(URI serverURI) {
            super(serverURI);
        }

        public WebSocketUtils(URI serverUri, Draft draft) {
            super(serverUri, draft);
        }

        public WebSocketUtils(URI serverUri, Map<String, String> headers, int connecttimeout) {
            super(serverUri, new Draft_17(), headers, connecttimeout);
        }

        @Override
        public void onOpen(ServerHandshake serverHandshake) {
            System.out.println("链接已建立");

        }

        @Override
        public void onMessage(String s) {
            System.out.println("收到字符串消息");
        }

        @Override
        public void onClose(int i, String s, boolean b) {
            System.out.println("链接已关闭");
        }

        @Override
        public void onError(Exception e) {
            System.out.println("报错啦");
        }

        @Override
        public void onMessage(ByteBuffer socketBuffer) {
            try {
                String marketStr = byteBufferToString(socketBuffer);
                String market = uncompress(marketStr).toLowerCase();
                if (market.contains("ping")) {
                    System.out.println("收到消息ping："+market);
                    String tmp = market.replace("ping", "pong");
                    wsclient.send(market.replace("ping", "pong"));
                } else {
                    msg = market;
                    System.out.println("收到消息："+msg);
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }

        public static Map<String, String> getWebSocketHeaders() throws IOException {
            Map<String, String> headers = new HashMap<String, String>();
            return headers;
        }

        private static void trustAllHosts(WebSocketUtils appClient) {
            TrustManager[] trustAllCerts = new TrustManager[] { new X509TrustManager() {
                public java.security.cert.X509Certificate[] getAcceptedIssuers() {
                    return new java.security.cert.X509Certificate[] {};
                }

                public void checkClientTrusted(X509Certificate[] chain, String authType) throws CertificateException {
                }

                public void checkServerTrusted(X509Certificate[] chain, String authType) throws CertificateException {
                }
            } };

            try {
                SSLContext sc = SSLContext.getInstance("TLS");
                sc.init(null, trustAllCerts, new java.security.SecureRandom());
                appClient.setWebSocketFactory(new DefaultSSLWebSocketClientFactory(sc));
            } catch (Exception e) {
                e.printStackTrace();
            }
        }

        public static WebSocketUtils executeWebSocket(String url,String sendMsg) throws Exception {
            wsclient = new WebSocketUtils(new URI(url), getWebSocketHeaders(), 1000);
            trustAllHosts(wsclient);
            wsclient.connectBlocking();
            wsclient.send(sendMsg);
            return wsclient;
        }

        // buffer 转String 
        public String byteBufferToString(ByteBuffer buffer) {
            CharBuffer charBuffer = null;
            try {
                Charset charset = Charset.forName("ISO-8859-1");
                CharsetDecoder decoder = charset.newDecoder();
                charBuffer = decoder.decode(buffer);
                buffer.flip();
                return charBuffer.toString();
            } catch (Exception ex) {
                ex.printStackTrace();
                return null;
            }
        }

        // 解压缩 
        public String uncompress(String str) throws IOException {
            if (str == null || str.length() == 0) {
                return str;
            }
            ByteArrayOutputStream out = new ByteArrayOutputStream();
            ByteArrayInputStream in = new ByteArrayInputStream(str.getBytes("ISO-8859-1"));
            GZIPInputStream gunzip = new GZIPInputStream(in);
            byte[] buffer = new byte[256];
            int n;
            while ((n = gunzip.read(buffer)) >= 0) {
                out.write(buffer, 0, n);
            }
            return out.toString();
        }

    }
}

```

