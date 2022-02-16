---
title: Coinstore官方API文档

language_tabs: # must be one of https://git.io/vQNgJ
  

toc_footers:

includes:
  
language: 简体中文

other_language: English

present_url: /cn

url: /en

active: active

other_active:

menu: 菜单

create_api: 创建 API Key

spot_goods: 现货

spot_goods_active: active

spot_goods_url: 'index.html'

contract: 永续合约

contract_url: 'futures.html'

searchText: 搜索

search: true

code_clipboard: true
---

# 介绍

欢迎使用Coinstore开发者文档，此文档是Coinstore API的唯一官方文档。

本文档提供了相关API的使用方法介绍。

RESTful API包含了资产，订单及行情等接口。

Websocket则提供了行情相关的接口及推送服务。

Coinstore API提供的服务会在此持续更新，请大家及时关注。



# 快速开始

## 接入准备

如需使用API，请先登录网页端，通过【用户中心】-【API管理】创建一个API key，再据此文档详情进行开发和交易。

您可以点击 'https://www.coinstore.com/#/user/bindAuth/ManagementAPI' 创建 API Key。

每个用户可创建5组API Key，每组API key可以绑定5个不同的IP地址。API key一旦绑定了IP地址，则只能从绑定的IP地址使用该API key调用API接口。出于安全考虑，强烈建议您为API key绑定相应的IP地址。

创建成功后请务必记住以下信息：

- `API Key`  API 访问密钥
- `Secret Key` 签名认证加密所使用的密钥

## 接口类型
Coinstore为用户提供两种接口，您可根据自己的使用场景和偏好来选择适合的方式进行查询行情、交易。

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

私有接口可用于交易管理。每个私有请求必须使用您的API Key进行签名验证。

## 接入URLs

**REST API**

`https://api.coinstore.com/api`

**WebSocket**

`wss://ws.coinstore.com/s/ws`

为保证API服务的稳定性，建议使用日本AWS云服务器进行访问。如使用中国大陆境内的客户端服务器，连接的稳定性将难以保证。


## <span id="a4">签名认证</span>

**签名说明**

API 请求在通过 internet 传输的过程中极有可能被篡改，为了确保请求未被更改，除公共接口（基础信息，行情数据）外的私有接口均必须使用您的 API Key 做签名认证，以校验参数或参数值在传输途中是否发生了更改。

**签名算法**

使用HmacSHA256哈希函数作为签名函数
```
Mac hmacSha256 = Mac.getInstance("HmacSHA256");
```

**签名步骤**
    
1、签名有效字符串为请求参数和请求体
注意：请求参数和请求体不进行任何排序，直接拼接成字符串作为payload。

 ```java
 String payload = “symbol=aaaa88&size=10”；
 ```

实例1：GET请求查询字符串

 ?symbol=aaaa88&size=10

```java
 String payload = “{"symbol":"aaaa88","side":"SELL","ordType":"LIMIT","ordPrice":2,"ordQty":1,
 "timestamp":1627384801051}”；
 ```

实例2 :Post请求体

 {"symbol":"aaaa88","side":"SELL","ordType":"LIMIT","ordPrice":2,"ordQty":1,"timestamp":1627384801051}

```java
 String payload = “symbol=aaaa88&size=10{"symbol":"aaaa88","side":"SELL","ordType":"LIMIT","ordPrice":2,"ordQty":1,
 "timestamp":1627384801051}”；
 ```

实例3：混合请求
 
 ?symbol=aaaa88&size=10
 {"symbol":"aaaa88","side":"SELL","ordType":"LIMIT","ordPrice":2,"ordQty":1,"timestamp":1627384801051}


```java
 String time = String.valueOf(X-CS-EXPIRES / 30000);
 hmacSha256.init(new SecretKeySpec(Secret_Key.getBytes(), "HmacSHA256"));
 byte[] hash = hmacSha256.doFinal(time.getBytes());
 String key = Hex.toHexString(hash);
```

2、使用签名函数对时间戳计算哈希值

>注意： X-CS-EXPIRES为13位时间戳，需要除以30000获取一个类时间戳，对其进行签名函数计算，获得函数值作为第三步的秘钥(第3步中的变量key的值）




```java
 hmacSha256.reset();
 hmacSha256.init(new SecretKeySpec(key.getBytes(), "HmacSHA256"));
 hash = hmacSha256.doFinal(payload.getBytes());
 String sign= Hex.toHexString(hash);
```

3、使用签名函数对有效字符串计算哈希值
 

>注意： key的值为第2步计算出来的哈希值。

**Python示例**

 https://coinstore-sg-encryption.s3.ap-southeast-1.amazonaws.com/filesUpload/ex1/public/coinstore.py

# API接入说明

## <span id="a3">请求格式</span>
所有的API请求都是restful，目前只有两种方法：GET和POST。
- GET请求：所有的参数都在路径参数里
- POST请求: 路径里可以设置参数，参数可以以JSON格式发送在请求主体（body）里，没有参数的需要传{}

一个合法的请求由以下几部分组成：
- 方法请求地址：即访问服务器地址api.coinstore.com，比如https://api.coinstore.com/api/trade/order/place
- 必须和可选参数。
- X-CS-APIKEY： 即用户申请的API Key。
- X-CS-EXPIRES：您发出请求的时间戳。如：1629291143107。
- X-CS-SIGN：签名计算得出的字符串，用于确保签名有效和未被篡改。

**注意：X-CS-APIKEY ，X-CS-EXPIRES ，X-CS-SIGN 三个参数都在请求头中，另外需要设置'Content-Type':'application/json'。**

## <span id="a3">返回格式</span>

所有的接口返回都是JSON格式。在JSON的第一层有3个字段code，msg和data。前两个字段表示请求状态和描述，实际的业务数据在data字段里。

```json
{
    "code": "0",
    "message": "suc",
    "data": // per API response data in nested JSON object
}
```
以下是一个返回格式的样例：

| 字段 | 数据类型  | 描述       |
| ---- | -----  | ---------- |
| code | int | 返回状态 |
| message  | string | 状态或错误描述 |
| data | object | 业务数据  |


## 错误信息

**HTTP状态码**

HTTP常见的错误码如下：
- 400 Bad Request – Invalid request forma 请求格式无效

- 401 signature failed – Invalid API Key 无效的API Key

- 404 service not found 没有找到请求

- 429 too many visits 请求太频繁被系统限流

- 500 internal server error – 服务器内部错误

**业务状态码**

如果失败，response message 带有错误描述信息, 对应的状态码描述如下：

| 状态码 | 说明                             | 备注                       |
| ------ | -------------------------------- | -------------------------- |
| 0      | 成功                             | code=0 成功， code >0 失败 |


# 账户相关


## <span id="1">资产余额</span>

获取用户资产余额

### HTTP请求: 
- POST /spot/accountList


> 响应 

```json
{
    "data": [
        {
            "uid": 315,
            "accountId": 1134971,
            "currency": "USDT",
            "balance": "0.05001",
            "type": 4,
            "typeName": "FROZEN"
        },
        {
            "uid": 315,
            "accountId": 1134971,
            "currency": "USDT",
            "balance": "13297.94999",
            "type": 1,
            "typeName": "AVAILABLE"
        }
    ],
    "code": 0
}
```

### 请求参数

|    code    |  type   | required |       comment        |
| ---------- | ------- | -------- | -------------------- |
|  |  |  |  |

### 响应数据

|       code        |  type  |                  comment                       |
| ---- | ----- |---------- |
| code | int      |   0：成功，其他失败         |
| message | String  | 错误信息 |
| data | Object []  |  业务数据 |
|- userId | Long  | 用户id |
|- accountId | Long  | 账户id |
|- currencyId | int  | 币种id |
|- balance | Decimal  | 金额 |
|- type | Decimal |账户状态 1:可用 4:冻结 |

# 订单相关

## <span id="2">获取当前订单</span>
获取当前订单

### HTTP请求: 
- GET /trade/order/active

> 响应

```json
{
    "data": [
        {
            "symbol": "LUFFYUSDT",
            "baseCurrency": "LUFFY",
            "quoteCurrency": "USDT",
            "timestamp": 1642402062196,
            "side": "BUY",
            "timeInForce": "GTC",
            "accountId": 1138204,
            "ordPrice": 9.1E-10,
            "cumAmt": "0",
            "cumQty": "0",
            "leavesQty": "0",
            "clOrdId": "b1b2ea5e00a84b888d419cb73f8eb203",
            "ordAmt": "0",
            "ordQty": "1",
            "ordId": "1722183748419690",
            "ordStatus": "SUBMITTED",
            "ordType": "LIMIT"
        }
    ],
    "code": 0
}
```

### 请求参数

|    code    |  type   | required |       comment        |
| ---------- | ------- | -------- | -------------------- |
|  |  |  |  |


### 响应数据

|       code        |  type  |                         comment                       |
| ----------------- | ------ |------------------------------- |
| code            | int    |     0：成功，其他失败                                               |
| message         | string    |         错误信息                                    |
| data            |  list  |    |
| ├─ ordId            | long      订单id            |
| ├─ clOrdId            | string     |   客户端订单id            |
| ├─symbol | string |   币对 |
| ├─baseCurrency| string |   base币|
| ├─quoteCurrency| string |   quot币|
| ├─ordPrice | string |    订单价格|
| ├─ordQty| string |   订单数量|
| ├─ordAmt| string |   订单金额|
| ├─side | string |    BUY,SELL|
| ├─cumAmt| string | 成交金额  |
| ├─cumQty| string |  成交数量 |
| ├─leavesQty| string |  剩余数量  |
| ├─ordStatus | string |  订单状态 NOT_FOUND,SUBMITTING,SUBMITTED,PARTIAL_FILLED,CANCELED,FILLED |
| ├─ordType | string |   MARKET,LIMIT,POST_ONLY|
| ├─timeInForce| string | GTC,IOC,FOK |
| ├─timestamp| long | 时间戳 |


## <span id="3">获取用户最新成交</span>
获取全部成交记录

### HTTP请求: 
- GET /trade/match/accountMatches


> 响应

```json
{
    "data": [
        {
            "id": 3984987,
            "remainingQty": 0E-18,
            "matchRole": 1,
            "feeCurrencyId": 44,
            "acturalFeeRate": 0.002000000000000000,
            "role": 1,
            "accountId": 1138204,
            "instrumentId": 15,
            "baseCurrencyId": 44,
            "quoteCurrencyId": 30,
            "execQty": 40.900000000000000000,
            "orderState": 50,
            "matchId": 258338866,
            "orderId": 1717384395096065,
            "side": 1,
            "execAmt": 8.887161000000000000,
            "selfDealingQty": 0E-18,
            "tradeId": 11523732,
            "fee": 0.081800000000000000,
            "matchTime": 1637825389,
            "seq": null
        }
    ],
    "code": 0
}
```

### 请求参数

|    code    |  type   | required |       comment        |
| ---------- | ------- | -------- | -------------------- |
|symbol | string | Y | 交易对 |
|size| int | N | 20 |

### 响应数据

|       code        |  type  |                      comment                       |
| ----------------- | ------ |---------------------------------- |
| code            | int    |  0：成功，其他失败                                               |
| message         | string    |   错误信息                                    |
| data | object|   |
| ├─accountId| long| 账户id |
| ├─instrumentId| int | 币对id |
| ├─baseCurrencyId| int | 基础货币 BTC |
| ├─quoteCurrencyId| int | 计价货币USDT |
| ├─matchRole| int | 撮合角色 TAKER(1),MAKER(-1) |
| ├─feeCurrencyId| int | 手续费币种id |
| ├─role| String | 角色 TAKER,MAKER |
| ├─execQty| String | 成交数量 |
| ├─orderState| int | 订单状态 |
| ├─matchId| long | 撮合id |
| ├─orderId| long | 订单id |
| ├─side| int | 订单方向 | BUY(1), SELL(-1) |
| ├─execAmt| String | 成交额 |
| ├─selfDealingQty| String | 自成交额 |
| ├─tradeId| long | 成交id |
| ├─fee| String | 手续费 |
| ├─matchTime| long  | 撮合时间 |
| ├─remainingQty| String | 剩余数量 |


##  <span id="4">取消委托单</span>
取消委托单

### HTTP请求: 
- POST /trade/order/cancel

> 请求体

```json
{
  "ordId": 1722183748419690,
  "symbol":"LUFFYUSDT"
}
```

> 响应

```json
{
    "code": 0,
    "data": {
        "clientOrderId": "b1b2ea5e00a84b888d419cb73f8eb203",
        "state": "CANCELED",
        "ordId": 1722183748419690
    }
}
```

### 请求参数

|    code    |  type   | required |       comment        |
| ---------- | ------- | -------- | -------------------- |
| symbol     | string | true | |
| ordId     | long     | true  |  |

### 响应数据

|       code        |  type  |                      comment                       |
| ----------------- | ------ |--------------------------------------- |
| code            | int    |     0：成功，其他失败                                               |
| message         | string    |   错误信息                                    |
| data            |    |   返回数据                    |
| ├─state| string|   |
| ├─ordId| long |   |

##  <span id="5">一键撤单</span>


### HTTP请求: 
- POST /trade/order/cancelAll

> 请求体

```json
{
  "symbol":"LUFFYUSDT"
}
```

> 响应

```json
{
    "code": 0
}
```

### 请求参数

|    code    |  type   | required |       comment        |
| ---------- | ------- | -------- | -------------------- |
| symbol     | string | false| 不传撤销所有币对订单 |

### 响应数据

|       code        |  type  |                      comment                       |
| ----------------- | ------ |--------------------------------------- |
| code            | int    |     0：成功，其他失败                                               |
| message         | string    |    错误信息                                    |


## <span id="6">创建订单</span>
创建订单

> 请求体

```json
{
	"symbol": "LUFFYUSDT",
	"side": "BUY",
	"ordType": "LIMIT",
	"ordPrice": 9.2e-10,
	"ordQty": "12323231243",
	"timestamp": 1642407805168
}
```

> 响应

```json
{
    "code": "0",
    "data": {
        "order_id":11594964764657880
    }
}
``` 

### HTTP请求: 
- POST /trade/order/place

### 请求参数

|    code    |  type   | required |       comment        |
| ---------- | ------- | -------- | -------------------- |
| clOrdId | string | false    |   客户端订单ID。客户自定义的唯一订单ID。 如果未发送，则自动生成  |
| symbol        | string | true    |  |
| side   | string    | true    |   BUY,SELL    |
| ordType     | string    | true    |  MARKET,LIMIT,POST_ONLY   |
| timeInForce    | string     | false    | defaultValue = "GTC"   |
| ordPrice    | long     | false    | 限价单必选 |
| ordQty    | long     | false    | 限价单必选，市价卖单必选 |
| ordAmt    | long     | false    | 市价买单必选 |
| timestamp    | long     | true    |时间戳  |                                                                    


                                

### 响应数据

|       code        |  type  |                    comment                       |
| ----------------- | ------ |------------------------------- |
| code            | int     |     0：成功，其他失败                                               |
| message         | string    |    错误信息                                    |
| data            |     |   返回数据                                                 |
| ├─ ordId            | long   |  订单id                                                 |

## <span id="13">批量下单</span>
批量下单

> 请求体

```json
{
	"symbol": "LUFFYUSDT",
	"orders": [
		{
			"side": "BUY",
			"ordType": "LIMIT",
			"ordPrice": 9.2e-10,
			"ordQty": "1"
		},
		{
			"side": "BUY",
			"ordType": "LIMIT",
			"ordPrice": 9.2e-10,
			"ordQty": "1"
		}
	],
	"timestamp": 1642574848089
}
```

> 响应

```json
{
    "code": 0,
    "data": [
        {
            "ordId": 1722364585836585,
            "clOrdId": "c09e68b49a724c8a95ef76526bf6bc05"
        },
        {
            "ordId": 1722364585836586,
            "clOrdId": "d0344830639d4f6d94be4110bf98c759"
        }
    ]
}
```

### HTTP请求: 
- POST /trade/order/placeBatch

### 请求参数

|    code    |  type   | required |       comment        |
| ---------- | ------- | -------- | -------------------- |
| symbol        | string | true    |  |
| timestamp    | long     | true    |  |
| orders        | list | true    |  |
| ├─ clOrdId | string | false    |   客户端订单ID。客户自定义的唯一订单ID。 如果未发送，则自动生成  |
| ├─ side   | string    | true    |   BUY,SELL    |
| ├─ ordType     | string    | true    |  MARKET,LIMIT,POST_ONLY   |
| ├─ timeInForce    | string     | false    | defaultValue = "GTC"   |
| ├─ ordPrice    | long     | false    | 限价单必选 |
| ├─ ordQty    | long     | false    | 限价单必选，市价卖单必选 |
| ├─ ordAmt    | long     | false    | 市价买单必选 |


### 响应数据

|       code        |  type  |                     comment                       |
| ----------------- | ------ | ---------------------------------------- |
| code            | int    |    0：成功，其他失败                                               |
| message         | string        |    错误信息                                    |
| data            |  list    |   返回数据                                                 |
| ├─ ordId            | long     |   订单id            |
| ├─ clOrdId            | string     |   客户端订单id            |
| ├─errno| int|   |  |
| ├─errMsg| string| | |

## <span id="27">根据订单id批量进行撤单</span>
根据订单id批量进行撤单

### HTTP请求: 
- POST trade/order/cancelBatch

> 请求体

```json
{
  "orderIds": [1722364585836586,1722364585836585],
  "symbol":"LUFFYUSDT"
}
```

> 响应

```json
{
    "data": {
        "success": [
            1722364585836586,
            1722364585836585
        ],
        "reject": []
    },
    "code": 0
}
```

### 请求参数

|    code    |  type   | required |       comment        |
| ---------- | ------- | -------- | -------------------- |
|symbol | string | Y |  "BTCUSDT"|
|orderIds| LIst| Y |[1,2,3]  |



### 响应数据

|       code        |  type  |          comment                       |
| ----------------- | ------ |---------------------------------------- |
| code         | string    |   0：成功，其他失败                               |
| message         | string    |    错误信息                                   |
| data            | Object   |                                      |
| --success|   List   |     成功的订单id集合                                         |
| --reject  |    List     |  失败的订单id集合                      |


## <span id="14">获取订单信息</span>
获取订单信息

> 响应

```json
{
    "data": {
        "baseCurrency": "LUFFY",
        "quoteCurrency": "USDT",
        "symbol": "LUFFYUSDT",
        "timestamp": 1642574848089,
        "side": "BUY",
        "accountId": 1138204,
        "ordId": 1722364585836586,
        "clOrdId": "d0344830639d4f6d94be4110bf98c759",
        "ordType": "LIMIT",
        "ordState": "CANCELED",
        "ordPrice": "0.00000000092",
        "ordQty": "1",
        "ordAmt": "0.00000000092",
        "cumAmt": "0",
        "cumQty": "0",
        "leavesQty": "0",
        "avgPrice": "0",
        "feeCurrency": "LUFFY",
        "timeInForce": "GTC"
    },
    "code": 0
}
```

### HTTP请求: 
- GET trade/order/orderInfo

### 请求参数:

|    code    |  type   | required |       comment        |
| ---------- | ------- | -------- | -------------------- |
|ordId | long | true  | 订单id |

### 响应数据:

|       code        |  type  |               comment                       |
| ----------------- | ------ | -------------------------------------- |
| code            | int     |     0：成功，其他失败                                               |
| message         | string    |    错误信息                                    |
| data | object|  | 
| ├─ordId| long | 订单ID |
| ├─clOrdId| String | 客户端订单ID |
| ├─accountId| long | 账户ID |
| ├─symbol | String |   | 交易对名称 |
| ├─baseCurrency| String | 基础货币 |
| ├─quoteCurrency| String | 计价货币 |
| ├─ordPrice | String | 价格 |
| ├─ordQty| String | 数量 |
| ├─ordAmt| String | 金额 |
| ├─side | Stirng | 方向 BUY,SELL|
| ├─cumAmt| String | 累计成交额 |
| ├─cumQty| String | 累计成交量 |
| ├─leavesQty| String | 剩余数量 |
| ├─ordState | Stirng | 状态 NOT_FOUND, REJECTED, SUBMITTING, SUBMITTED, PARTIAL_FILLED, REPLACING, REPLACED, CANCELING, CANCELED, EXPIRED, STOPPED, FILLED |
| ├─ordType | Stirng | 订单类型 MARKET,LIMIT,POST_ONLY  |
| ├─flags| Stirng | 订单类型 POST_ONLY,REDUCE_ONLY,HIDDEN |
| ├─timeInForce| Stirng | 成交限制类型 GTC,IOC,FOK |
| ├─timestamp| long | 订单时间 毫秒 |
| ├─avgPrice| long | 成交均价  |


# 行情相关

##  <span id="7">Ticker</span>
 市场所有交易对的Ticker
 
### HTTP请求: 
- GET /v1/market/tickers

> 响应

```json
{
  "data": [
    {
      "channel": "ticker",
      "bidSize": "454.2",
      "askSize": "542.6",
      "count": 41723,
      "volume": "24121351.85",
      "amount": "1693799.10798965",
      "close": "0.067998",
      "open": "0.071842",
      "high": "0.072453",
      "low": "0.067985",
      "bid": "0.0679",
      "ask": "0.0681",
      "symbol": "TRXUSDT",
      "instrumentId": 2
    }
  ],
  "code": 0
}
```

### 请求参数: 

|    code    |  type   | required |       comment        |
| ---------- | ------- | -------- | -------------------- |

### 响应数据: 

|       code        |  type   |                      comment                       |
| ----------------- | ------ | ---------------------------------------- |
| code            | int    |     0：成功，其他失败                                               |
| message         | string       |    错误信息                                    |
| data | Object|   |
| ├─channel| String |标识  |
| ├─instrumentId| Integer  |交易对ID |
| ├─symbol| String  |交易对 |
| ├─count | Integer  |24h滚动交易笔数 |
| ├─volume|String  |24h以报价币种计量的交易量 |    
| ├─amount|  String  |24h以基础币种计量的交易量 |           
| ├─close|  String  | 末一笔成交价| 
| ├─open|  String  | 第一笔成交价| 
| ├─high|  String  | 最高一笔成交价| 
| ├─low|  String  | 最低一笔成交价| 
| ├─bid|  String  | 买一价| 
| ├─bidSize|  String  | 买一量| 
| ├─ask|  String  | 卖一价| 
| ├─askSize|  String  | 卖一量| 
                     



 
##  <span id="7">获取深度</span>
获取深度数据

> 响应

```json
{
	"data": {
		"channel": "4@depth@5",
		"level": 5,
		"a": [
			[
				"41995",
				"0.018144",
				-1
			],
			[
				"41995.5",
				"0.008279",
				-1
			]
		],
		"b": [
			[
				"41991.5",
				"0.548567",
				1
			],
			[
				"41991",
				"0.013168",
				1
			]
		],
		"symbol": "BTCUSDT",
		"instrumentId": 4
	},
	"code": 0
}
 ```
 
### HTTP请求: 
- GET /v1/market/depth/{symbol}

### 请求参数: 

|    code    |  type   | required |       comment        |
| ---------- | ------- | -------- | -------------------- |
|symbol | string | true | 交易对，如“BTCUSDT” |
|depth | int | false | 深度的数量，如“5, 10, 20, 50, 100”,默认20 |

### 响应数据: 

|       code        |  type   |                      comment                       |
| ----------------- | ------ | ---------------------------------------- |
| code            | int    |     0：成功，其他失败                                               |
| message         | string       |    错误信息                                    |
| data | Object|   |
| ├─channel| String |标识  |
| ├─level| Integer  |数量 |
| ├─instrumentId| Integer  |交易对ID |
| ├─symbol| string  |交易对 |
| ├─a | List  |卖盘，[${价格}, ${数量}, ${方向}] ] |
| ├─b | List  |买盘，[${价格}, ${数量}, ${方向}] ] |


##  <span id="7">获取K线</span>
获取交易K线

> 响应

```json
{
	"data": {
		"channel": "4@kline@week_1",
		"item": [
			{
				"endTime": 1641744059,
				"amount": "98630624.103511453",
				"interval": "week_1",
				"startTime": 1641744000,
				"firstTradeId": 14547884,
				"lastTradeId": 14799534,
				"volume": "2307.742209",
				"close": "43196.0002",
				"open": "41628.2582",
				"high": "52250.3884",
				"low": "39654.039"
			}
		],
		"symbol": "BTCUSDT",
		"instrumentId": 4
	},
	"code": 0
}
 ```
 
### HTTP请求: 
- GET /v1/market/kline/{symbol}

### 请求参数: 

|    code    |  type   | required |       comment        |
| ---------- | ------- | -------- | -------------------- |
|symbol | string | true | 交易对，如“BTCUSDT” |
|period | String | false | 数据粒度，如“1min, 5min, 15min, 30min, 60min, 4hour,12hour, 1day, 1week”,默认20 |
|size | Integer | false | 数据条数，[1,2000] |

### 响应数据: 

|       code        |  type   |                      comment                       |
| ----------------- | ------ | ---------------------------------------- |
| code            | int    |     0：成功，其他失败                                               |
| message         | string       |    错误信息                                    |
| data | Object|   |
| ├─channel| String |标识  |
| ├─instrumentId| Integer  |交易对ID |
| ├─symbol| String  |交易对 |
| ├─item | List  |K 线集合 |
| ├─├─interval | String  |K线间隔 |
| ├─├─endTime| Long |结束时间，单位 s |         
| ├─├─amount|String  |成交额 |               
| ├─├─startTime|Long  |开始时间，单位 s|     
| ├─├─firstTradeId|Long |第一笔成交ID |    
| ├─├─lastTradeId|Long  |末一笔成交ID |    
| ├─├─volume|  String  |成交量 |           
| ├─├─close|  String  | 末一笔成交价|            
| ├─├─open|   String  | 第一笔成交价|            
| ├─├─high|  String  |最高成交价 |             
| ├─├─low|   String  |最低成交价 |             



##  <span id="7">最新成交</span>
获取最新成交记录

### HTTP请求: 
- GET /v1/market/trade/{symbol}

> 响应

```json
 {
 	"data": [
 		{
 			"channel": "4@trade",
 			"time": 1642495112,
 			"volume": "0.011102",
 			"price": "41732.3305",
 			"tradeId": 14867136,
 			"takerSide": "BUY",
 			"seq": 14867136,
 			"ts": 1642495112000,
 			"symbol": "BTCUSDT",
 			"instrumentId": 4
 		}
 	],
 	"code": 0
 }
```

### 请求参数: 

|    code    |  type   | required |       comment        |
| ---------- | ------- | -------- | -------------------- |
|symbol | string | true | 交易对，如“BTCUSDT” |
|size | Integer | false | 数据条数，[1,100] |

### 响应数据: 

|       code        |  type   |                      comment                       |
| ----------------- | ------ | ---------------------------------------- |
| code            | int    |     0：成功，其他失败                                               |
| message         | string       |    错误信息                                    |
| data | Object|   |
| ├─channel| String |标识  |
| ├─instrumentId| Integer  |交易对ID |
| ├─symbol| String  |交易对 |
| ├─interval | String  |K线间隔 |
| ├─time| Long |时间，单位 s |         
| ├─ts|Long  |时间，单位 ms |                  
| ├─tradeId|Long |第一笔成交ID |    
| ├─takerSide|String  |方向 "BUY"，"SELL" |    
| ├─volume|  String  |成交量 |           
| ├─price|  String  | 末一笔成交价|                       



##  <span id="7">获取所有交易对最新价格</span>
获取所有交易对最新价格

> 响应

```json
{
  "code": 0,
  "message": "",
  "data": [
    {
      "id": 1,
      "symbol": "BTCUSDT",
      "price": "400"
    },
    {
      "id": 4,
      "symbol": "AUTUSDT",
      "price": "1"
    }
  ]
}
 ```
 
### HTTP请求: 
- GET /v1/ticker/price

### 请求参数: 

|    code    |  type   | required |       comment        |
| ---------- | ------- | -------- | -------------------- |
|symbol | string | true | 交易对，区分大小写，逗号分割 |

**默认：查询所有交易对**
**查询指定交易对: /v1/ticker/price;symbol=BTCUSDT,EOSUSDT,AUTUSDT**

### 响应数据: 

|       code        |  type   |                      comment                       |
| ----------------- | ------ | ---------------------------------------- |
| code            | int    |     0：成功，其他失败                                               |
| message         | string       |    错误信息                                    |
| data | List[]|   |
| ├─id| long |  |
| ├─symbol| string  |交易对 |
| ├─price| string  |价格 |

  
  
# Websocket行情数据

## **简介**

### 接入URL
wss://ws.coinstore.com/s/ws

1. 所有wss接口的 baseurl为: wss://<host:port>/s/ws

2. stream名称中所有交易对均为 小写

3. 每个链接有效期不超过24小时，请妥善处理断线重连。

4. 每3分钟，服务端会发送ping帧，客户端应当在10分钟内回复pong帧，否则服务端会主动断开链接。允许客户端发送不成对的pong帧(即客户端可以以高于10分钟每次的频率发送pong帧保持链接)。


### 服务端推送数据类型说明

> Format Schema

```lang=json
{
    "S": 1, // session 级别的 response 消息序号，session 重连后重置，可用来判断是否漏消息
    "T": "echo|resp", // 响应类型，详见 `Types` 部分
    "C": 200,  // code
    "M": "sub.channel.success",  // meaning of this code
    "sid": "3ae985ff-cadd-9c96-7a42-4ed29e77f83b",
    "echo": {},  // client request command
    "payload": "external msg"
}
```

#### Types

##### request-response 类型的消息

* `echo`: 服务器接受到到每一个消息都会以该种形式返回一个message，确认消息已经被接收
* `resp`: 服务器接受到到每一个消息都会以该种形式返回一个处理结果的message，标记消息处理，返回信息中包含处理结果信息


##### subscribe 消息类型
以下类型的消息，从执行对应 channel 的 `SUB` 命令开始，到执行 `UNSUB` 结束，如果服务器端数据发生变化则推送，最小推送间隔 100ms
```lang=json
{
    "S": 1,
    "T": "kline|ticker|depth|trade|account",
    ...:  // 详细见下面到每种数据订阅格式 
}
```
* `kline`

* `ticker`

* `depth`

* `trade`

* `account`

* `order`



### Pong
服务器端支持 websocket pong frame 和 pong message 两种形式端 pong 响应

> Pong message format

```lang=json
{
  "op": "pong",
  "epochMillis": 1604040975
}
```

#### Websocket Pong frame

1. https://tools.ietf.org/html/rfc6455#section-5.5.3 

2. https://developer.mozilla.org/zh-CN/docs/Web/API/WebSockets_API/Writing_WebSocket_servers#Pings%E5%92%8CPongs%EF%BC%9AWebSockets%E7%9A%84%E5%BF%83%E8%B7%B3 




> Example

```lang=shell
$>wscat -c 'ws://127.0.0.1:8080/s/ws'
< {"S":1,"T":"resp","sid":"3ae985ff-cadd-9c96-7a42-4ed29e77f83b","C":200,"M":"established"}
> {"op":"SUB","channel":["80004@trade"],"id":1}
< {"S":2,"T":"echo","sid":"3ae985ff-cadd-9c96-7a42-4ed29e77f83b","C":200,"M":"command.received","echo":{"op":"SUB","channel":[{"name":"80004@trade","type":{"name":"trade","auth":"PUB"},"instrumentId":"80004","lifeStartTime":1604040964,"msgCount":0,"pub":true}],"id":1}}
< {"S":3,"T":"resp","sid":"3ae985ff-cadd-9c96-7a42-4ed29e77f83b","C":200,"M":"sub.channel.success"}
< {"S":4,"T":"trade","channel":"80004@trade","time":1604040975,"price":"9811.7494086","takerSide":"SELL","tradeId":26461,"volume":"7.505","symbol":"EOSUSD","instrumentId":80004}
```

## **实时订阅/取消数据流**

> 订阅一个信息流

```lang=json
{
    "op": "SUB",
    "channel": [
        "btcusdt@ticker",
        "btcusdt@depth"
    ],
    "id": 1
}
```

> 取消订阅一个信息流

```lang=json
{
    "op": "UNSUB",
    "channel": [
        "btcusdt@depth"
    ],
    "id": 312
}
```

> 已订阅信息流

```lang=json
{
    "op": "LIST",
    "channel":[],
    "id": 3
}
```

* 以下数据可以通过websocket发送以实现订阅或取消订阅数据流。示例如下。
* 响应内容中的id是无符号整数，作为往来信息的唯一标识。
* 如果相应内容中的 result 为 null，表示请求发送成功。

### Client `op` Types

1. `SUB`
2. `UNSUB`
3. `LOGIN`
4. `REQ`
5. `LIST`



> Respon

```lang=json
{
    "S": 12,
    "T": "resp",
    "subs": [
        {
            "name": "80004@trade",
            "type": {
                "name": "trade",
                "auth": "PUB"
            },
            "instrumentId": "80004",
            "lifeStartTime": 1604043594,
            "msgCount": 212
        }
    ],
    "sid": "b1beeb73-7c84-862f-3d03-619de1e1a5dc"
}
```

> NOTE: `<symbol>` 参数暂时请使用 交易对的id，后续支持使用交易对的名称

> IMPORTANT:  请优先使用  `symbol` 字段，` instrumentId` 标记为 `Deprecated`

> NOTE:  所有返回数据的时间，单位都是 `秒`


## **逐笔交易**

> Send

```lang=json
 {
 	"op": "SUB",
 	"channel": [
 		"28@trade"
 	],
 	"param": {
 		"size": 2
 	},
 	"id": 1
 }
```
> Receive全量数据：

```lang=json
 {
   "S": 15,
   "T": "trade",
   "data": [
     {
       "channel": "28@trade",
       "price": "384.32",
       "tradeId": 21704,
       "seq": 10157,
       "ts": 1612685697087,
       "takerSide": "BULL",
       "time": 1612685697,
       "volume": "2",
       "symbol": "BTCJJ",
       "instrumentId": 28
     },
     {
       "channel": "28@trade",
       "price": "644.96",
       "tradeId": 21705,
       "seq": 10158,
       "ts": 1612685698157,
       "takerSide": "SELL",
       "time": 1612685698,
       "volume": "1.1932988",
       "symbol": "BTCJJ",
       "instrumentId": 28
     }
   ]
 }
```
> Receive增量数据：

```lang=json
{
  "instrumentId": 88066,
  "symbol": "USDTBTC",    // 交易对
  "tradeId": 12345,       // 交易ID
  "takerSide": "BUY",    // taker side
  "price": "0.001",     // 成交价格
  "volume": "100",       // 成交数量
  "time": 123456785,   // 成交时间 单位 s
  "ts": 1612685313400, // 单位 ms
  "seq": 9935         // 唯一自增序号
}
```

### Stream Name
`<symbol>@trade`

eg: `88066@trade`
 
### param
 `param":{"size":2}`
 




## **K线 Streams**
K线stream逐秒推送所请求的K线种类(最新一根K线)的更新。

> Send

```lang=json
 {"op":"SUB","channel":["4@kline@min_1"],"id":1}
```
> Receive

```lang=json
{
   "instrumentId": 88066, 
   "startTime": 1603732500,  // 这根 k线的开始时间，单位 s
    "endTime": 1603732559,  // 这根 k线的开始时间，单位 s
    "symbol": "USDTBTC",  // 交易对
    "interval": "1m",      // K线间隔
    "firstTradeId": 100,       // 这根K线期间第一笔成交ID
    "lastTradeId": 200,       // 这根K线期间末一笔成交ID
    "open": "0.0010",  // 这根K线期间第一笔成交价
    "close": "0.0020",  // 这根K线期间末一笔成交价
    "high": "0.0025",  // 这根K线期间最高成交价
    "low": "0.0015",  // 这根K线期间最低成交价
    "volume": "1000",    // 这根K线期间成交量
    "amount": "1.0000",  // 这根K线期间成交额
}
```

### Stream Name
`<symbol>@kline@<interval>`
  
eg: `88066@kline@min_1`

### interval 可选值
* min_1
* min_5
* min_15
* min_30
* hour_1
* hour_4
* hour_12
* day_1
* week_1



## **K线 Request**
请求历史k线

> Send

```lang=json
 {
 	"op": "REQ",
 	"param": {
 		"channel": "4@kline@min_1",
 		"endTime": null,
 		"limit": 200
 	},
 	"id": 1
 }
```
> Receive

```lang=json
{
    "channel": "88066@kline@min_1",
    "symbol": "USDTBTC",
    "instrumentId": 88066,
    "item": [
        {
            "interval": "min_1",
            "startTime": 1603766280,
            "endTime": 1603766339,
            "lastTradeId": 4739656,
            "firstTradeId": 4739656,
            "close": "4.91",
            "open": "4.91",
            "high": "4.91",
            "low": "4.91",
            "volume": "8.488",
            "amount": "41.67608"
        },
        ...
    ]
}
```

###  param

* `channel`: `<symbol>@kline@<interval>` ,eg:88066@kline@min_1
* `limit`: 最大不超过 200
* `endTime`: 可选，exclusive





## **按 Symbol 的 Ticker信息**
按Symbol刷新的最近24小时精简 ticker 信息

> Send

```lang=json
 {"op":"SUB","channel":["4@ticker"],"id":1}
```
> Receive

```lang=json
{
  "instrumentId": 88066,
  "symbol": "USDTBTC",      // 交易对
  "open": "0.0010",      // 整整24小时前，向后数的第一次成交价格
  "high": "0.0025",      // 24小时内最高成交价
  "low": "0.0010",      // 24小时内最低成交加
  "close": "0.0025",      // 最新成交价格
  "volume": "10000",       // 24小时内成交量
  "amount": "18"          // 24小时内成交额
}
```

### Stream 名称
`<symbol>@ticker`, eg:  `88066@ticker`



## **全市场所有Symbol 的 Ticker信息**
按Symbol刷新的最近24小时精简ticker信息

###  Stream 名称: 
`!@ticker`

> Send

```lang=json
 {"op":"SUB","channel":["!@ticker"],"id":1}
```

> Receive

```lang=json
{
    "data": [
        {
            "channel": "80002@ticker",
            "open": "9996.24",
            "high": "10048.6601",
            "low": "9768.90178",
            "close": "9833.12538",
            "volume": "3362.405",
            "amount": "33212664.57170620732",
            "symbol": "BTCUSD", 
            "instrumentId": 80002
        },
        {
            "channel": "80001@ticker",
            "open": "10001.57",
            "high": "10053.9441",
            "low": "9816.23078",
            "close": "9832.91038",
            "volume": "3138.93361771",
            "amount": "31040843.0757471861414",
            "symbol": "ETHUSD",
            "instrumentId": 80001
        },
        ...
    ]
}
```

## **深度信息**
每秒或每100毫秒推送有限档深度信息。

> Send

```lang=json
 {"op":"SUB","channel":["4@depth@20"],"id":1}
```

> Receive

```lang=json
{
  "level": 5,
  "instrumentId": 88066,
  "symbol": "BTCUSDT"
  "b": [             // Bids to be updated
    [
      "0.0024",         // Price level to be updated
      "10"              // Quantity
    ]
  ],
  "a": [             // Asks to be updated
    [
      "0.0026",         // Price level to be updated
      "100"            // Quantity
    ]
  ]
}
```

### Stream Names
`<symbol>@depth@<levels>` 

 eg:  `88066@depth@50`
 
### levels 可选值
levels表示几档买卖单信息, 可选 5/10/20/50/100档




## **登陆**
登陆时可以同时指定订阅数据

```lang=json
{
    "op": "LOGIN",
    "channel": [
        "7@account",
        "80002@order"
    ],
    "auth": {
        "user-id":4930,     // 用户登陆账号的 user id 或者子账号的 user id
        "token": "eb737c84862f3d"  // 登陆之后的 token； 端上登陆用户使用登陆后的token， api 用户填写  api-key
        "type": "token"      // 可选值范围：token | api , 默认为 token， api 用户填写 api
    }
    "id": 2
}
```

## **账户**

 Stream Name: `<currency>@account`, or `!@account` all currency

```lang=json
{
    "accountId": 12,
    "currency": "BTC",
    "frozen": "21.03",
    "available": "3128.29",
    "timestamp": 1602493840  // 单位 s
}

```

## **成交订单**

 Stream Name: `<symbol>@order`, or `!@order` all symbol's

```lang=json
{
    // increment
    
    "version": 12,

    // order base info, never change

    "accountId": 1,
    "ordId": 12,
    "symbol": "BTCUSDT",
    "side": "BUY",
    "ordType": "LIMIT",
    "timeInForce": "GTC",
    "ordPrice": "128.21",
    "ordQty": "11.21",
    "ordAmt": "231.2",

    // order state
    "ordState": "PARTIAL_FILLED",

    // sum every trade time

    "execQty": "12.2",
    "execAmt": "312.12",
    "remainingQty": "1.2",

    // current trade info,  present meaning values when `matchQty` is bigger than 0

    "matchId": 12,
    "tradeId": 123,
    "matchRole": "TAKER",
    "matchQty": "1.9",
    "matchAmt": "390.29",
    "selfDealingQty": "1.9",
    "actualFeeRate": "0.002",
    "feeCurrencyId": 12,
    "fee": 0.21,

    "timestamp": 1602493840 // 单位 s
}
```

## **字典**

### OrderState

* REJECTED
* SUBMITTING
* SUBMITTED
* PARTIAL_FILLED
* CANCELING
* CANCELED
* EXPIRED
* STOPPED
* FILLED

### OrderType

* MARKET 
* LIMIT 
* POST_ONLY

### TimeInForce

* IOC
* GTC

### MatchRole

* TAKER
* MAKER

### Side

* BUY
* SELL

# Other API

## Introduction
This document covers the API endpoint details including spot exchange API and it was full public access. The access address host is https://api.coinstore.com/


## <span id="114">Asset list</span>  
The assets endpoint is to provide a detailed summary for each currency available on the exchange.

> 响应

```lang=json
{
 "code": "0",
 "message": null,
 "data": {
     "JJ": {
         "name": "jj",
         "unified_cryptoasset_id": 95,
         "can_withdraw": "true",
         "can_deposit": "true",
         "min_withdraw": "10",
         "max_withdraw": "100",
         "maker_fee": "0.002",
         "taker_fee": "0.002"
     }
 }
}
```

### Request 
- GET  /v2/public/assets

### Response

|    code    |  type   | example |  meaning  | remarks |
| -----------| ------  | -----   | -----     | -----   |
|name|String|Y|Full name of cryptocurrency.|Trading pair name|
|unified_cryptoasset_id|Long|Y|Unique ID of cryptocurrency assigned by Unified Cryptoasset ID.|Trading pair ID|
|can_withdraw|String|Y|Identifies whether withdrawals are enabled or disabled.|Withdraw enable or not|
|can_deposit|String|Y|Identifies whether deposits are enabled or disabled.|Deposit enable or not|
|min_withdraw|String|Y|Identifies the single minimum withdrawal amount of a cryptocurrency.|Minimum withdraw|
|max_withdraw|String|Y|Identifies the single maximum withdrawal amount of a cryptocurrency.|Maximum withdraw|
|maker_fee|String|Y|Fees applied when liquidity is added to the order book.|Maker trading fee|
|taker_fee|String|Y|Fees applied when liquidity is removed from the order book.|Taker trading fee|



## <span id="115">Trading pair information</span>  
The summary endpoint is to provide an overview of market data for all tickers and all market pairs on the exchange.
> 响应

```lang=json
{
 "code": "0",
 "message": null,
 "data": [
     {
         "trading_pairs": "btcusdt",
         "last_price": "60000",
         "lowest_ask": "60000",
         "highest_bid": "555",
         "base_volume": "0",
         "quote_volume": "0",
         "price_change_percent_24h": "0",
         "highest_price_24h": "60000",
         "lowest_price_24h": "60000",
         "base_currency": "BTC",
         "quote_currency": "USDT"
     },
     {
         "trading_pairs": "ethusdt",
         "last_price": "60000",
         "lowest_ask": "60000",
         "highest_bid": "555",
         "base_volume": "0",
         "quote_volume": "0",
         "price_change_percent_24h": "0",
         "highest_price_24h": "60000",
         "lowest_price_24h": "60000",
         "base_currency": "ETH",
         "quote_currency": "USDT"
     }
 ]
}
```

### Request 
- GET /v2/public/summary

### Response

|    code    |  type   | example |  meaning  | remarks |
| -----------| ------  | -----   | -----     | -----   |
|trading_pairs|String|Y|Identifier of a ticker with delimiter to separate base/quote, eg. BTC-USD (Price of BTC is quoted in USD)|Trading pair name|
|last_price|String|Y|Last transacted price of base currency based on given quote currency|Last price|
|lowest_ask|String|Y|Lowest Ask price of base currency based on given quote currency|lowest ask price|
|highest_bid|String|Y|Highest bid price of base currency based on given quote currency|Highest bid price|
|base_volume|String|Y|24-hr volume of market pair denoted in BASE currency|24H volume|
|quote_volume|String|Y|24-hr volume of market pair denoted in QUOTE currency|24H amount|
|price_change_percent_24h|String|Y|24-hr % price change of market pair|24h change%|
|highest_price_24h|String|Y|Highest price of base currency based on given quote currency in the last 24-hrs|24H high|
|lowest_price_24h|String|Y|Lowest price of base currency based on given quote currency in the last 24-hrs|24H low|
|base_currency|String|Y|Symbol/currency code of base currency, eg. BTC|base currency|
|quote_currency|String|Y|Symbol/currency code of quote currency, eg. USD|quote currency|





## <span id="116">Ticker</span>
The ticker endpoint is to provide a 24-hour pricing and volume summary for each market pair available on the exchange.

> 响应

```lang=json
{
 "code": "0",
 "message": null,
 "data": {
     "BTC_USDT": {
         "base_id": "BTC",
         "quote_id": "USDT",
         "last_price": "60000",
         "quote_volume": "0",
         "base_volume": "0",
         "isFrozen": 1
     },
     "ETH_USDT": {
         "base_id": "ETH",
         "quote_id": "USDT",
         "last_price": "60000",
         "quote_volume": "0",
         "base_volume": "0",
         "isFrozen": 1
     }
 }
}
```

### Request 
- GET /v2/public/ticker

### Response

|    code    |  type   | example |  meaning  | remarks |
| -----------| ------  | -----   | -----     | -----   |
|base_id|String|Y|The quote pair Unified Cryptoasset ID.|Quote currency ID|
|quote_id|String|Y|The base pair Unified Cryptoasset ID.|Base currency ID|
|last_price|String|Y|Last transacted price of base currency based on given quote currency|Last price|
|quote_volume|String|Y|24 hour trading volume denoted in QUOTE currency|24H amount|
|base_volume|String|Y|24-hour trading volume denoted in BASE currency|24H volume|
|isFrozen|Integer|Y|Indicates if the market is currently enabled (0) or disabled (1).|Market enable or not|




## <span id="117">Depth</span>
The order book endpoint is to provide a complete level 2 order book (arranged by best asks/bids) with full depth returned for a given market pair.

> 响应

```lang=json
{
 "code": "0",
 "message": null,
 "data": {
     "timestamp": "1622526449",
     "bids": [
         [
             "10",
             "0.1102"
         ],
         [
             "20",
             "0.1"
         ]
     ],
     "asks": [
         [
             "60000",
             "91.185787"
         ],
         [
             "66000",
             "0.30322"
         ]
     ]
 }
}
```

### Request 
- GET /v2/public/orderbook/market_pair?market_pair=BTC_USDT&level=3&depth=100

|    code    |  type   | example |  meaning  | remarks |
| -----------| ------  | -----   | -----     | -----   |
|market_pair|String|Y|A pair such as “LTC_BTC”|Trading pair name|
|depth|Integer|Y|Orders depth quantity: [0,5,10,20,50,100,500] Not defined or 0 = full order book Depth = 100 means 50 for each bid/ask side.|Depth|
|level|Integer|Y|Level 1 – Only the best bid and ask. Level 2 – Arranged by best bids and asks. Level 3 – Complete order book, no aggregation.| Level |

### Response

|    code    |  type   | example |  meaning  | remarks |
| -----------| ------  | -----   | -----     | -----   |
|timestamp|String|Y|Unix timestamp in milliseconds for when the last updated time occurred.|Server time|
|bids|String|Y|An array containing 2 elements. The offer price and quantity for each bid order.|Bid|
|asks|String|Y|An array containing 2 elements. The ask price and quantity for each ask order.|Ask|




## <span id="118">Market Pair</span>
The trades endpoint is to return data of 100 recently completed trades for a given market pair.

> 响应

```lang=json
{
 "code": "0",
 "message": null,
 "data": [
     {
         "trade_id": 2101,
         "price": "60000",
         "base_volume": "0.003333",
         "quote_volume": "0",
         "timestamp": "1622526904",
         "type": "buy"
     },
     {
         "trade_id": 2100,
         "price": "60000",
         "base_volume": "0.008333",
         "quote_volume": "0",
         "timestamp": "1622526904",
         "type": "buy"
     }
 ]
}
```

### Request 
- GET /v2/public/trades/market_pair?market_pair=BTC_USDT

|    code    |  type   | example |  meaning  | remarks |
| -----------| ------  | -----   | -----     | -----   |
|market_pair	|String	|Y	|A pair such as LTC_BTC.	|Trading Pair Name|

### Response

|    code    |  type   | example |  meaning  | remarks |
| -----------| ------  | -----   | -----     | -----   |
|trade_id|Integer|Y|A unique ID associated with the trade for the currency pair transaction Note: Unix timestamp does not qualify as trade_id.|Order ID|
|price|String|Y|Last transacted price of base currency based on given quote currency|Last price|
|base_volume|String|Y|Transaction amount in BASE currency.|Volume|
|quote_volume|String|Y|Transaction amount in QUOTE currency.|Amount|
|timestamp|String|Y|Unix timestamp in milliseconds for when the transaction occurred.|Timestamp|
|type|String|Y|Used to determine whether or not the transaction originated as a buy or sell. Buy – Identifies an ask was removed from the order book. Sell – Identifies a bid was removed from the order book.|Side|



## <span id="119">Historical Market Pair</span>
The trades endpoint is to return data on historical completed trades for a given market pair.

> 响应

```lang=json
{
 "code": "0",
 "message": null,
 "data": {
    "buy":[
     {
         "trade_id": 2101,
         "price": "60000",
         "base_volume": "0.003333",
         "quote_volume": "0",
         "timestamp": "1622526904",
         "type": "buy"
     },
     {
         "trade_id": 2100,
         "price": "60000",
         "base_volume": "0.008333",
         "quote_volume": "0",
         "timestamp": "1622526904",
         "type": "buy"
     }],
    "sell":[
        {
            "trade_id": 2101,
            "price": "60000",
            "base_volume": "0.003333",
            "quote_volume": "0",
            "timestamp": "1622526904",
            "type": "sell"
        },
        {
            "trade_id": 2100,
            "price": "60000",
            "base_volume": "0.008333",
            "quote_volume": "0",
            "timestamp": "1622526904",
            "type": "sell"
        }]
 }
}
```

### Request 
- GET /v2/public/trades/historical_trades?market_pair=BTC_USDT

|    code    |  type   | example |  meaning  | remarks |
| -----------| ------  | -----   | -----     | -----   |
|market_pair	|String	|Y	|A pair such as LTC_BTC.	|Trading Pair Name|
|type	|String	|N	|To indicate nature of trade - buy/sell	|type|
|limit	|Integer	|N	|Number of historical trades to retrieve from time of query. [100, 200, 500...]. default returns 100 history.	|limit|
|start_time	|Long	|N	|Start time from which to query historical trades from	|start time|
|end_time	|Long	|N	|End time for historical trades query	|end time|

### Response

|    code    |  type   | example |  meaning  | remarks |
| -----------| ------  | -----   | -----     | -----   |
|trade_id|Integer|Y|A unique ID associated with the trade for the currency pair transaction Note: Unix timestamp does not qualify as trade_id.|Order ID|
|price|String|Y|Last transacted price of base currency based on given quote currency|Last price|
|base_volume|String|Y|Transaction amount in BASE currency.|Volume|
|quote_volume|String|Y|Transaction amount in QUOTE currency.|Amount|
|timestamp|String|Y|Unix timestamp in milliseconds for when the transaction occurred.|Timestamp|
|type|String|Y|Used to determine whether or not the transaction originated as a buy or sell. Buy – Identifies an ask was removed from the order book. Sell – Identifies a bid was removed from the order book.|Side|










