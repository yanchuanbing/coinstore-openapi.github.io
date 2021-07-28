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

欢迎使用Coinstore开发者文档，此文档是Coinstore API的唯一官方文档. 

本文档提供了相关API的使用方法介绍。RESTful API包含了资产，订单及行情等接口。Websocket则提供了行情相关的接口及推送服务。Coinstore API提供的能力会在此持续更新，请大家及时关注。



# 快速开始

## 接入准备
如需使用API，请先联系商务或运营创建。

需要提供调用方的IP，进行白名单绑定。

创建成功后请务必记住以下信息：

- `API Key`  API 访问密钥

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

**签名步骤(推荐)**
    
注：目前是通过IP白名单做的安全验证，未开启签名。


# API接入说明

## <span id="a3">请求格式</span>
所有的API请求都是restful，目前只有两种方法：GET和POST。
- GET请求：所有的参数都在路径参数里
- POST请求，所有参数以JSON格式发送在请求主体（body）里，没有参数的需要传{}

一个合法的请求由以下几部分组成：
- 方法请求地址：即访问服务器地址。https://api.coinstore.com/api/trade/order/place
- 必须和可选参数。

  以下几个参数是所有需要验签接口必须要提供的：
  - API key： 即用户申请的API Key中的Access Key。其字段名为api-key

所有请求都必须包含以下Header：

- api-key API KEY作为一个字符串。
- Content-Type  需要统一设置为:'application/json'。

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
| code | string | 返回状态 |
| message  | string | 状态或错误描述 |
| data | object | 业务数据  |


## 错误信息

**HTTP状态码**

HTTP常见的错误码如下：
- 400 Bad Request – Invalid request forma 请求格式无效

- 401 signature failed – Invalid API Key 无效的API Key

- 404 service not found 没有找到请求

- 429 too many visits 请求太频繁被系统限流

- 500 unkown exception – 服务器内部错误

**业务状态码**

如果失败，response body 带有错误描述信息, 对应的状态码描述如下：

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

|       code        |  type  |        example        |                      comment                       |
| ---- | ----- | ---------- |---------- |
| code | String      | |            |
| message | String  | | 错误信息 |
| data | Object []  | |  业务数据 |
|- userId | Long  |  |用户id |
|- accountId | Long  | | 账户id |
|- currencyId | Integer  | | 币种id |
|- balance | Decimal  | | 金额 |
|- type | Decimal | |账户状态 1:可用 4:冻结 |

# 订单相关

## <span id="2">获取当前订单</span>

获取当前订单

### HTTP请求: 

- GET /trade/order/active


> 响应

```json

```

### 请求参数

|    code    |  type   | required |       comment        |
| ---------- | ------- | -------- | -------------------- |
|  |  |  |  |


### 响应数据

|       code        |  type  |        example        |                      comment                       |
| ----------------- | ------ | --------------------- | -------------------------------------------------- |
| code            | int    | 0                     |     0：成功，其他失败                                               |
| message         | string    |                 |    错误信息                                    |
| data            |  list  |    | |
| ├─ ordId            | long   | 11594964764657880     |   订单id            |
| ├─ clOrdId            | string   | 7980b2ebf32042ba9dbfbddc555a3985     |   客户端订单id            |
| ├─symbol | string |  |  币对 |
| ├─baseCurrency| string |  | base币|
| ├─quoteCurrency| string |  | quot币|
| ├─ordPrice | string |  |  订单价格|
| ├─ordQty| string |  | 订单数量|
| ├─ordAmt| string |  | 订单金额|
| ├─side | string |  |  BUY,SELL|
| ├─cumAmt| string |  | |
| ├─cumQty| string |  | |
| ├─leavesQty| string |  | |
| ├─ordStatus | string |  | NOT_FOUND,SUBMITTING,SUBMITTED,PARTIAL_FILLED,CANCELED,FILLED |
| ├─ordType | string |  |  MARKET,LIMIT,POST_ONLY|
| ├─timeInForce| string | GTC | GTC,IOC,FOK |
| ├─timestamp| long |  | |


## <span id="3">获取用户最新成交</span>
获取全部成交记录

### HTTP请求: 
- GET /trade/match/accountMatches


> 响应

```json

```

### 请求参数

|    code    |  type   | required |       comment        |
| ---------- | ------- | -------- | -------------------- |
|symbol | string | Y | 交易对 |
|size| int | N | 20 |

### 响应数据

|       code        |  type  |        example        |                      comment                       |
| ----------------- | ------ | --------------------- | -------------------------------------------------- |
| code            | int    | 0                     |     0：成功，其他失败                                               |
| message         | string    |                 |    错误信息                                    |
| data | object|  | |
| ├─accountId| long| | |
| ├─instrumentId| int | | |
| ├─baseCurrencyId| int | | |
| ├─quoteCurrencyId| int | | |
| ├─matchRole| int | | TAKER(1),MAKER(-1) |
| ├─feeCurrencyId| int | | |
| ├─role| string | | TAKER,MAKER |
| ├─execQty| string | | |
| ├─orderState| int | | |
| ├─matchId| long | | |
| ├─orderId| long | | |
| ├─side| int | | BUY(1), SELL(-1) |
| ├─execAmt| string | | |
| ├─selfDealingQty| string | | |
| ├─tradeId| long | | |
| ├─fee| string | | |
| ├─matchTime| long  | | |
| ├─remainingQty| string | | |


##  <span id="4">取消委托单</span>
取消委托单

### HTTP请求: 
- POST /trade/order/cancel

### 请求参数

|    code    |  type   | required |       comment        |
| ---------- | ------- | -------- | -------------------- |
| symbol     | string | true | |
| ordId     | long     | true  |  |

### 响应数据

|       code        |  type  |        example        |                      comment                       |
| ----------------- | ------ | --------------------- | -------------------------------------------------- |
| code            | int    | 0                     |     0：成功，其他失败                                               |
| message         | string    |                 |    错误信息                                    |
| data            |    |      |   返回数据                                                 |
| ├─state| string|  | |
| ├─ordId| long |  | |

##  <span id="5">一键撤单</span>


### HTTP请求: 
- POST /trade/order/cancelAll

### 请求参数

|    code    |  type   | required |       comment        |
| ---------- | ------- | -------- | -------------------- |
| symbol     | string | false| 不传撤销所有币对订单 |

### 响应数据

|       code        |  type  |        example        |                      comment                       |
| ----------------- | ------ | --------------------- | -------------------------------------------------- |
| code            | int    | 0                     |     0：成功，其他失败                                               |
| message         | string    |                 |    错误信息                                    |


## <span id="6">创建订单</span>
创建订单

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
| timestamp    | long     | true    |  |                                                                    


> 响应

```json
{
    "code": "0",
    "data": {
        "order_id":11594964764657880
    }
}
```                                 

### 响应数据

|       code        |  type  |        example        |                      comment                       |
| ----------------- | ------ | --------------------- | -------------------------------------------------- |
| code            | int    | 0                     |     0：成功，其他失败                                               |
| message         | string    |                 |    错误信息                                    |
| data            |     |      |   返回数据                                                 |
| ├─ ordId            | long   | 11594964764657880     |   订单id                                                 |

## <span id="13">批量下单</span>
批量下单
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

> 响应

```json


```

### 响应数据

|       code        |  type  |        example        |                      comment                       |
| ----------------- | ------ | --------------------- | -------------------------------------------------- |
| code            | int    | 0                     |     0：成功，其他失败                                               |
| message         | string    |                 |    错误信息                                    |
| data            |  list  |      |   返回数据                                                 |
| ├─ ordId            | long   | 11594964764657880     |   订单id            |
| ├─ clOrdId            | string   | 7980b2ebf32042ba9dbfbddc555a3985     |   客户端订单id            |
| ├─errno| int|   |  |
| ├─errMsg| string| | |

## <span id="27">根据订单id批量进行撤单</span>
根据订单id批量进行撤单

### HTTP请求: 
- POST trade/order/cancelBatch

### 请求参数

|    code    |  type   | required |       comment        |
| ---------- | ------- | -------- | -------------------- |
|symbol | string | Y |  "BTCUSDT"|
|orderIds| LIst| Y |[1,2,3]  |


> 响应

```json

```

### 响应数据

|       code        |  type  |        example        |                      comment                       |
| ----------------- | ------ | --------------------- | -------------------------------------------------- |
| code         | string    |0                |    正常返回信息                                |
| message         | string    |order.id-required                 |    错误信息                                   |
| data            | Object   |                    |                                             |
| --success|   List   | [1,2,3]                    |     成功的订单id集合                                         |
| --reject  |    List   | [1,2,3]                                  |  失败的订单id集合                      |


## <span id="14">.获取订单信息</span>
.获取订单信息

### HTTP请求: 
- GET trade/order/orderInfo


> 响应

```json

```

### 请求参数:

|    code    |  type   | required |       comment        |
| ---------- | ------- | -------- | -------------------- |
|clOrdId | string | false | ordId 二选一 |
|ordId | long | false  | clOrdId 二选一 |

### 响应数据:

|       code        |  type  |        example        |                      comment                       |
| ----------------- | ------ | --------------------- | -------------------------------------------------- |
| code            | int    | 0                     |     0：成功，其他失败                                               |
| message         | string    |                 |    错误信息                                    |
| data | object|  | |
| ├─ordId| long |  | |
| ├─clOrdId| string |  | |
| ├─accountId| long |  | |
| ├─symbol | string |  |   |
| ├─baseCurrency| string |  | |
| ├─quoteCurrency| string |  | |
| ├─ordPrice | string |  | |
| ├─ordQty| string |  | |
| ├─ordAmt| string |  | |
| ├─side | string |  | BUY,SELL|
| ├─cumAmt| string |  | |
| ├─cumQty| string |  | |
| ├─leavesQty| string |  | |
| ├─ordStatus | string |  | NOT_FOUND,SUBMITTING,SUBMITTED,PARTIAL_FILLED,CANCELED,FILLED |
| ├─ordType | string |   | MARKET,LIMIT,POST_ONLY |
| ├─timeInForce| string | GTC |   GTC,IOC,FOK |
| ├─timestamp| long |  | |
| ├─avgPrice| long |  | |


# 行情相关
##  <span id="7">获取所有交易对最新价格</span>
获取所有交易对最新价格

### HTTP请求: 
- GET /v1/ticker/price


> 响应

```json
{
  "code": 0,
  "message": "",
  "data": [
    {
      "id": 1,
      "symbol": "btcusdt",
      "price": "400"
    },
    {
      "id": 4,
      "symbol": "autusdt",
      "price": "1"
    }
  ]
}
 ```


### 请求参数: 

> 默认：查询所有交易对

> 查询指定交易对: /v1/ticker/price;symbol=btcusdt,eosusdt,autusdt `

### 响应数据: 

|       code        |  type  |        example        |                      comment                       |
| ----------------- | ------ | --------------------- | -------------------------------------------------- |
| code            | int    | 0                     |     0：成功，其他失败                                               |
| message         | string    |                 |    错误信息                                    |
| data | List[]|  | |
| ├─id| long |  | |
| ├─symbol| string |  |交易对 |
| ├─price| string |  |价格 |



# Websocket行情数据

## 简介

### 接入URL

`
wss://ws.coinstore.com/s/ws
`
1. 所有wss接口的 baseurl为: wss://<host:port>/s/ws
1. stream名称中所有交易对均为 小写
1. 每个链接有效期不超过24小时，请妥善处理断线重连。
1. `每3分钟，服务端会发送ping帧，客户端应当在10分钟内回复pong帧，否则服务端会主动断开链接。允许客户端发送不成对的pong帧(即客户端可以以高于10分钟每次的频率发送pong帧保持链接)。`

### 服务端推送数据类型说明

#### Format Schema

```lang=json
{
    "S": 1,  // session 级别的 response 消息序号，session 重连后重置，可用来判断是否漏消息
    "T": "resp", // 响应类型，详见 `Types` 部分
    ...
}
```

#### Types

 1. request-response 类型的消息

** `echo`: 服务器接受到到每一个消息都会以该种形式返回一个message，确认消息已经呗接收
** `resp`: 服务器接受到到每一个消息都会以该种形式返回一个处理结果的message，标记消息处理，返回信息中包含处理结果信息

```lang=json
{
    "S": 1,
    "T": "echo|resp",
    "C": 200,  // code
    "M": "sub.channel.success",  // meaning of this code
    "sid": "3ae985ff-cadd-9c96-7a42-4ed29e77f83b",
    "echo": {},  // client request command
    "payload": "external msg"
}
```

 2. subscribe 消息类型

> 以下类型的消息，从执行对应 channel 的 `SUB` 命令开始，到执行 `UNSUB` 结束，如果服务器端数据发生变化则推送，最小推送间隔 100ms

** `kline`
** `ticker`
** `depth`
** `trade`
** `account`
** `order`

```lang=json
{
    "S": 1,
    "T": "kline|ticker|depth|trade|account",
    ...:  // 详细见下面到每种数据订阅格式 
}
```

#### Pong

> 服务器端支持 websocket pong frame 和 pong message 两种形式端 pong 响应

#### Websocket Pong frame

1. [[ https://tools.ietf.org/html/rfc6455#section-5.5.3 | here ]]  
2.  AND [[ https://developer.mozilla.org/zh-CN/docs/Web/API/WebSockets_API/Writing_WebSocket_servers#Pings%E5%92%8CPongs%EF%BC%9AWebSockets%E7%9A%84%E5%BF%83%E8%B7%B3 | here ]] 

#### Pong message format

```lang=json
{
  "op": "pong",
  "epochMillis": 1604040975
}
```


#### Example

```lang=shell
$>wscat -c 'ws://127.0.0.1:8080/s/ws'
< {"S":1,"T":"resp","sid":"3ae985ff-cadd-9c96-7a42-4ed29e77f83b","C":200,"M":"established"}
> {"op":"SUB","channel":["80004@trade"],"id":1}
< {"S":2,"T":"echo","sid":"3ae985ff-cadd-9c96-7a42-4ed29e77f83b","C":200,"M":"command.received","echo":{"op":"SUB","channel":[{"name":"80004@trade","type":{"name":"trade","auth":"PUB"},"instrumentId":"80004","lifeStartTime":1604040964,"msgCount":0,"pub":true}],"id":1}}
< {"S":3,"T":"resp","sid":"3ae985ff-cadd-9c96-7a42-4ed29e77f83b","C":200,"M":"sub.channel.success"}
< {"S":4,"T":"trade","channel":"80004@trade","time":1604040975,"price":"9811.7494086","takerSide":"SELL","tradeId":26461,"volume":"7.505","symbol":"EOSUSD","instrumentId":80004}
```

### **实时订阅/取消数据流**

* 以下数据可以通过websocket发送以实现订阅或取消订阅数据流。示例如下。
* 响应内容中的id是无符号整数，作为往来信息的唯一标识。
* 如果相应内容中的 result 为 null，表示请求发送成功。

#### Client `op` Types

1. `SUB`
2. `UNSUB`
3. `LOGIN`
4. `REQ`
5. `LIST`
6. ...(未来新功能支持)

#### 订阅一个信息流

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

#### 取消订阅一个信息流

```lang=json
{
    "op": "UNSUB",
    "channel": [
        "btcusdt@depth"
    ],
    "id": 312
}
```

#### 已订阅信息流

```lang=json
{
    "op": "LIST",
    "channel":[],
    "id": 3
}
```

** Respon

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

NOTE: `<symbol>` 参数暂时请使用 交易对的id，后续支持使用交易对的名称

IMPORTANT:  请优先使用  `symbol` 字段，` instrumentId` 标记为 `Deprecated`

NOTE:  所有返回数据的时间，单位都是 `秒`

### **公共行情频道**

#### **逐笔交易**

> Stream Name: `<symbol>@trade`, eg: `88066@trade`
> param: `param":{"size":2}`
> e.g: ``{"op":"SUB","channel":["28@trade"],"param":{"size":2},"id":1}``

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

##### Chagnelog: 20210207

> 1. 增加字段 ts & seq, 降序
> 2. 支持 param.size 参数: sub 命令会默认返回 param.size 条历史数据
> 3. 订阅时返回批量数据等结构： 
> ```lang=json
> {
>   "S": 15,
>   "T": "trade",
>   "data": [
>     {
>       "channel": "28@trade",
>       "price": "384.32",
>       "tradeId": 21704,
>       "seq": 10157,
>       "ts": 1612685697087,
>       "takerSide": "BULL",
>       "time": 1612685697,
>       "volume": "2",
>       "symbol": "BTCJJ",
>       "instrumentId": 28
>     },
>     {
>       "channel": "28@trade",
>       "price": "644.96",
>       "tradeId": 21705,
>       "seq": 10158,
>       "ts": 1612685698157,
>       "takerSide": "SELL",
>       "time": 1612685698,
>       "volume": "1.1932988",
>       "symbol": "BTCJJ",
>       "instrumentId": 28
>     }
>   ]
> }
> ```




#### **K线 Streams**

> K线stream逐秒推送所请求的K线种类(最新一根K线)的更新。
> Stream Name: `<symbol>@kline@<interval>`, eg: `88066@kline@min_1`
> interval 可选值:
> ** min_1
> ** min_5
> ** min_15
> ** min_30
> ** hour_1
> ** hour_4
> ** hour_12
> ** day_1
> ** week_1
> ** mon_1

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

#### **K线 Request**

> 请求历史k线：
> param: `"{channel":"88066@kline@min_1","endTime":1603766280,"limit":10}`,
> eg:  `{"op":"REQ","param":{"channel":"88066@kline@min_1","limit":10},"id":1}`
>`limit`: 最大不超过 200
> `endTime`: 可选，exclusive

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


#### **按 Symbol 的 Ticker信息**

> 按Symbol刷新的最近24小时精简 ticker 信息
> Stream 名称: `<symbol>@ticker`, eg:  `88066@ticker`

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

#### **全市场所有Symbol 的 Ticker信息**

> 按Symbol刷新的最近24小时精简ticker信息
> Stream 名称: `!@ticker`

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

#### **有限档深度信息**

> 每秒或每100毫秒推送有限档深度信息。levels表示几档买卖单信息, 可选 5/10/20/50/100档
> Stream Names: `<symbol>@depth@<levels>` ~~或 `<symbol>@depth@<levels>@100ms`.~~, eg:  `88066@depth@50`

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

### **用户私有数据频道**

#### **登陆**

> 登陆时可以同时指定订阅数据

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

#### **账户**

> Stream Name: `<currency>@account`, or `!@account` all currency

```lang=json
{
    "accountId": 12,
    "currency": "BTC",
    "frozen": "21.03",
    "available": "3128.29",
    "timestamp": 1602493840  // 单位 s
}

```

#### **成交**

> Stream Name: `<symbol>@order`, or `!@order` all symbol's

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

### **字典**

#### OrderState

* REJECTED
* SUBMITTING
* SUBMITTED
* PARTIAL_FILLED
* CANCELING
* CANCELED
* EXPIRED
* STOPPED
* FILLED

#### OrderType

* MARKET 
* LIMIT 
* POST_ONLY

#### TimeInForce

* IOC
* GTC

#### MatchRole

* TAKER
* MAKER

#### Side

* BUY
* SELL




