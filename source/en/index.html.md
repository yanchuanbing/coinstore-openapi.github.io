---
title: Official API Document Of Coinstore

language_tabs: # must be one of https://git.io/vQNgJ
  

toc_footers:

includes:
  
language: English

other_language: 简体中文

present_url: /en

url: /cn

active: active

other_active: 

menu: 菜单

create_api: Create API KEY

spot_goods: Spot

spot_goods_active: active

spot_goods_url: 'index.html'

contract: Perpetual Swap

contract_url: 'futures.html'

searchText: Search

search: true

code_clipboard: true
---

# Introduction

Welcome to Coinstore Developer Documentation. It is the only official documentation of Coinstore API.

This documentation provides an introduction to the use of related APIs.

RESTful API includes interfaces such as assets, orders and tickers.

Websocket provides ticker-related interface and push service.

The services provided by Coinstore API will be continuously updated here, so stay tuned for updates.



# Quick Start

## Access Preparation

To use API, please log into the webpage first, create an API key through [User Center] - [API Managment], and then develop and trade according to the details of this documentation.

You can click 'https://www.coinstore.com/#/user/bindAuth/ManagementAPI' to create an API Key.

Each user can create 5 groups of API Keys, and each group of API Keys can bind 5 different IP addresses. Once an API key binds an address, the API interface can only be called by using the API key from the bound IP address. For security reasons, it is strongly recommended that you bind the corresponding IP address for API key.

Please remember the following information upon successful creation:

- `API Key`  API access key
- `Secret Key` Key for encryption of signature authentication

## Interface Type
Coinstore provides users with two interfaces, and you can choose the appropriate way to query the ticker and trade according to your own usage scenarios and preferences.

**REST API**

REST, the abbreviation of Representational State Transfer, is a popular Internet transmission architecture. It has the characteristics of clear structure, standards compliance, easy understanding and expansion, and is being adopted by more and more websites with the benefits as follows:

- In RESTful architecture, each URL represents a resource;
- Some representation layer of this resource is transferred between the client and the server;
- The client operates the server-side resources through four HTTP instructions to realize "presentation layer state transformation". 

Developers are advised to use REST API for one-time operations such as trades or assets.

**WebSocket API**

WebSocket is a new HTML5 Protocol. It realizes full-duplex communication between client and server, and the connection between client and server can be established by a simple handshake. The server can actively push information to the client according to business rules.

Developers are advised to use WebSocket API to obtain market tickers, asks/bids depth and other information.

### Interface Authentication

The above two interfaces include public interface and private interface.

Public interface can be used to obtain basic information and ticker data. Public interfaces can be called without authentication.

Private interface can be used for trading management. Every private request must use your API Key for signature verification.

## Access URLs

**REST API**

`https://api.coinstore.com/api`

**WebSocket**

`wss://ws.coinstore.com/s/ws`

To ensure the stability of API service, it is recommended to access using Japanese AWS cloud server. If the client server in Chinese mainland is used, it would be difficult to guarantee the stability of the connection.


## <span id="a4"> Signature Authentication </span>

**Signature Description**

The API request is very likely to be tampered in the process of transmission through the Internet. In order to ensure that the request has not been changed, all private interfaces other than public interfaces (basic information, ticker data) must use your API Key for signature authentication to verify whether the parameters or parameter values have changed during transmission.

**Signature Algorithm**

HmacSHA256 hash function is used as signature function.
```
Mac hmacSha256 = Mac.getInstance("HmacSHA256");
```

**Signature Steps**
    
1. The signature valid string consists of request parameters and request body.
Note: The request parameters and request body are not sorted but directly spliced into a string as payload.

 ```java
  Example 1: GET Request Query String
 ?symbol=aaaa88&size=10
 
 String payload = "symbol=aaaa88&size=10"；
 ```






```java
Example 2: POST Request Body
{"symbol":"aaaa88","side":"SELL","ordType":"LIMIT","ordPrice":2,"ordQty":1,"timestamp":1627384801051}


 String payload = "{"symbol":"aaaa88","side":"SELL","ordType":"LIMIT","ordPrice":2,"ordQty":1,
 "timestamp":1627384801051}"；
```


```java
Example 3: Mixed Request
 ?symbol=aaaa88&size=10
{"symbol":"aaaa88","side":"SELL","ordType":"LIMIT","ordPrice":2,"ordQty":1,"timestamp":1627384801051}

 String payload = "symbol=aaaa88&size=10{"symbol":" aaaa88","side":" SELL","ordType":" LIMIT","ordPrice":2,"ordQty":1,
 "timestamp":1627384801051}"；
```




2. Use signature function to calculate hash value for timestamp

> Note: X-CS-EXPIRES is a 13-bit timestamp, which needs to be divided by 30000 to obtain a class timestamp. It is calculated by signature function to obtain the function value as the key of step 3 (the value of variable key in step 3).


```java
 String time = String.valueOf(X-CS-EXPIRES / 30000);
 hmacSha256.init(new SecretKeySpec(Secret_Key.getBytes(), "HmacSHA256"));
 byte[] hash = hmacSha256.doFinal(time.getBytes());
 String key = Hex.toHexString(hash);
```





3. Use signature function to calculate hash value for valid string

> Note: The value of key is the hash value calculated in step 2.

```java
 hmacSha256.reset();
 hmacSha256.init(new SecretKeySpec(key.getBytes(), "HmacSHA256"));
 hash = hmacSha256.doFinal(payload.getBytes());
 String sign= Hex.toHexString(hash);
```

**Python Example**

 https://coinstore-sg-encryption.s3.ap-southeast-1.amazonaws.com/filesUpload/ex1/public/coinstore.py


# API Access Instructions

## <span id="a3"> Request Format </span>
All API requests are restful, and there are only two methods at present: GET and POST.
- GET request: All parameters are in path parameters
- POST request: Parameters can be set in the path, and they can be sent in JSON format to the request body. If there are no parameters,{} needs to be sent

A licit request consists of the following parts:
- method request address: Access server address api.coinstore.com，e.g. https://api.coinstore.com/api/trade/order/place
- Required and optional parameters.
- X-CS-APIKEY: API Key applied by the user.
- X-CS-EXPIRES: Timestamp when you issued the request. For example:1629291143107.
- X-CS-SIGN: A string calculated from the signature, which is used to ensure that the signature is valid and not tampered with.

**Note: X-CS-APIKEY, X-CS-EXPIRES and X-CS-SIGN are all in the request header, and 'Content-Type':'application/json' 
needs to be set.**

## <span id="a3">Return Format</span>

All interface returns are in JSON format. There are three fields in the first layer of JSON: code, msg and data. The first two fields indicate the request status and description, and the actual business data is in the data field.

```json
{
    "code": "0",
    "msg": "suc",
    "data": // per API response data in nested JSON object
}
```
The following is an example of a return format:

| field| data type  | description       |
| ---- | -----  | ---------- |
| code | int | 0：success, other: failure     |
| message  | string | status or error description |
| data | object | transaction data  |


## Error Message

**HTTP Status Code**

Common error codes for HTTP are as follows:
- 400 Bad Request – Invalid request format

- 401 signature failed – Invalid API Key

- 404 service not found

- 429 too many visits

- 500 internal server error

**Transaction Status Code**

In case of failure, the response message carries error description information, and the corresponding status code is described as follows:

| status code | description                             | remarks                       |
| ------ | -------------------------------- | -------------------------- |
| 0      | success                             | code=0 success, code >0 failure |


# Account Related


## <span id="1"> Assets Balance </span>

Get user assets balance

### HTTP Request: 
- POST /spot/accountList


> Response 

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

### Request Parameters

|    code    |  type   | required |       comment        |
| ---------- | ------- | -------- | -------------------- |
|  |  |  |  |

### Response Data

|       code        |  type  |        example        |                      comment                       |
| ---- | ----- | ---------- |---------- |
| code | String      | |            |
| message | String  | | Error Message |
| data | Object []  | |  Transaction Data |
|- userId | Long  |  |User id |
|- accountId | Long  | | Account id |
|- currencyId | Integer  | | Currency id |
|- balance | Decimal  | | Amoun |
|- type | Decimal | |Account status 1: available 4: deactivated |

# Order Related

## <span id="2"> Get current orders </span>

Get current order

### HTTP Request: 

- GET /trade/order/active


> Response

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

### Request Parameters

|    code    |  type   | required |       comment        |
| ---------- | ------- | -------- | -------------------- |
|  |  |  |  |


### Response Data

|       code        |  type  |        example        |                      comment                       |
| ----------------- | ------ | --------------------- | -------------------------------------------------- |
| code            | int    | 0                     |     0: success, other: failure                                               |
| message         | string    |                 |    error message                                    |
| data            |  list  |    | |
| ├─ ordId            | long   | 11594964764657880     |   order id            |
| ├─ clOrdId            | string   | 7980b2ebf32042ba9dbfbddc555a3985     |   client order id            |
| ├─symbol | string |  |  token pair |
| ├─baseCurrency| string |  | base currency|
| ├─quoteCurrency| string |  | quot currency|
| ├─ordPrice | string |  |  order price|
| ├─ordQty| string |  | order quantity|
| ├─ordAmt| string |  | order amount|
| ├─side | string |  |  BUY,SELL|
| ├─cumAmt| string |  | |
| ├─cumQty| string |  | |
| ├─leavesQty| string |  | |
| ├─ordStatus | string |  | NOT_FOUND,SUBMITTING,SUBMITTED,PARTIAL_FILLED,CANCELED,FILLED |
| ├─ordType | string |  |  MARKET,LIMIT,POST_ONLY|
| ├─timeInForce| string | GTC | GTC,IOC,FOK |
| ├─timestamp| long |  | |


## <span id="3"> Get user's latest trade </span>
Get all trading records

### HTTP Request: 
- GET /trade/match/accountMatches


> Response

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

### Request Parameters

|    code    |  type   | required |       comment        |
| ---------- | ------- | -------- | -------------------- |
|symbol | string | Y | symbol |
|size| int | N | 20 |

### Response Data

|       code        |  type  |        example        |                      comment                       |
| ----------------- | ------ | --------------------- | -------------------------------------------------- |
| code            | int    | 0                     |     0: success, other: failure                                               |
| message         | string    |                 |    error message                                    |
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


##  <span id="4"> Cancel orders </span>
Cancel orders

### HTTP Request: 
- POST /trade/order/cancel

> Request Body

```json
{
  "ordId": 1722183748419690,
  "symbol":"LUFFYUSDT"
}
```

> Response

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

### Request Parameters

|    code    |  type   | required |       comment        |
| ---------- | ------- | -------- | -------------------- |
| symbol     | string | true | |
| ordId     | long     | true  |  |

### Response Data

|       code        |  type  |        example        |                      comment                       |
| ----------------- | ------ | --------------------- | -------------------------------------------------- |
| code            | int    | 0                     |     0: success, other: failure                                               |
| message         | string    |                 |    error message                                    |
| data            |    |      |   return data                                                 |
| ├─state| string|  | |
| ├─ordId| long |  | |

##  <span id="5"> One-click cancellation </span>


### HTTP Request: 
- POST /trade/order/cancelAll

> Request Body

```json
{
  "symbol":"LUFFYUSDT"
}
```

> Response

```json
{
    "code": 0
}
```

### Request Parameters

|    code    |  type   | required |       comment        |
| ---------- | ------- | -------- | -------------------- |
| symbol     | string | false| cancel all token pair orders without transfer |

### Response Data

|       code        |  type  |        example        |                      comment                       |
| ----------------- | ------ | --------------------- | -------------------------------------------------- |
| code            | int    | 0                     |     0: success, other: failure                                               |
| message         | string    |                 |    error message                                    |


## <span id="6"> Create order </span>
Create order

> Request Body

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

> Response

```json
{
    "code": "0",
    "data": {
        "order_id":11594964764657880
    }
}
```

### HTTP Request: 
- POST /trade/order/place

### Request Parameters

|    code    |  type   | required |       comment        |
| ---------- | ------- | -------- | -------------------- |
| clOrdId | string | false    |   client order ID. Unique customer-defined order ID. If not sent, it is automatically generated  |
| symbol        | string | true    |  |
| side   | string    | true    |   BUY,SELL    |
| ordType     | string    | true    |  MARKET,LIMIT,POST_ONLY   |
| timeInForce    | string     | false    | defaultValue = "GTC"   |
| ordPrice    | long     | false    | limit order required |
| ordQty    | long     | false    | limit order required, market price sell order required |
| ordAmt    | long     | false    | market price buy order required |
| timestamp    | long     | true    |  |



### Response Data

|       code        |  type  |        example        |                      comment                       |
| ----------------- | ------ | --------------------- | -------------------------------------------------- |
| code            | int    | 0                     |     0: success, other: failure                                               |
| message         | string    |                 |    error message                                    |
| data            |     |      |   return data                                                 |
| ├─ ordId            | long   | 11594964764657880     |   order id                                                 |

## <span id="13"> Batch ordering </span>
Batch ordering

> Request Body

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

> Response

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

### HTTP Request: 
- POST /trade/order/placeBatch

### Request Parameters

|    code    |  type   | required |       comment        |
| ---------- | ------- | -------- | -------------------- |
| symbol        | string | true    |  |
| timestamp    | long     | true    |  |
| orders        | list | true    |  |
| ├─ clOrdId | string | false    |   client order ID. Unique customer-defined order ID. If not sent, it is automatically generated  |
| ├─ side   | string    | true    |   BUY,SELL    |
| ├─ ordType     | string    | true    |  MARKET,LIMIT,POST_ONLY   |
| ├─ timeInForce    | string     | false    | defaultValue = "GTC"   |
| ├─ ordPrice    | long     | false    | limit order required |
| ├─ ordQty    | long     | false    | limit order required, market price sell order required |
| ├─ ordAmt    | long     | false    | market price buy order required |



### Response Data

|       code        |  type  |        example        |                      comment                       |
| ----------------- | ------ | --------------------- | -------------------------------------------------- |
| code            | int    | 0                     |     0: success, other: failure                                               |
| message         | string    |                 |    error message                                    |
| data            |  list  |      |   return data                                                 |
| ├─ ordId            | long   | 11594964764657880     |   order id            |
| ├─ clOrdId            | string   | 7980b2ebf32042ba9dbfbddc555a3985     |   client order id            |
| ├─errno| int|   |  |
| ├─errMsg| string| | |

## <span id="27"> Batch cancellation according to order id </span>
Batch cancellation according to order id.

### HTTP Request: 
- POST trade/order/cancelBatch

> Request Body

```json
{
  "orderIds": [1722364585836586,1722364585836585],
  "symbol":"LUFFYUSDT"
}
```

> Response

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

### Request Parameters

|    code    |  type   | required |       comment        |
| ---------- | ------- | -------- | -------------------- |
|symbol | string | Y |  "BTCUSDT"|
|orderIds| LIst| Y |[1,2,3]  |




### Response Data

|       code        |  type  |        example        |                      comment                       |
| ----------------- | ------ | --------------------- | -------------------------------------------------- |
| code         | string    |0                |    normal return information                                |
| message         | string    |order.id-required                 |    error message                                   |
| data            | Object   |                    |                                             |
| --success|   List   | [1,2,3]                    |     set of successful order ids                                         |
| --reject  |    List   | [1,2,3]                                  |  set of failed order ids.                      |


## <span id="14">Get order information </span>
Get order information

> Response

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

### HTTP Request: 
- GET trade/order/orderInfo

### Request Parameters:

|    code    |  type   | required |       comment        |
| ---------- | ------- | -------- | -------------------- |
|ordId | long | false  | order id |

### Response Data:

|       code        |  type  |        example        |                      comment                       |
| ----------------- | ------ | --------------------- | -------------------------------------------------- |
| code            | int    | 0                     |     0: success, other: failure                                               |
| message         | string    |                 |    error message                                    |
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
| ├─ordState | string |  | NOT_FOUND,SUBMITTING,SUBMITTED,PARTIAL_FILLED,CANCELED,FILLED |
| ├─ordType | string |   | MARKET,LIMIT,POST_ONLY |
| ├─timeInForce| string | GTC |   GTC,IOC,FOK |
| ├─timestamp| long |  | |
| ├─avgPrice| long |  | |


# Ticker Related

## <span id="7">Ticker</span>
 Ticker for all trading pairs in the market

> Response
 
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

### HTTP request:
- GET /v1/market/tickers

### Request parameters:

| code | type | required | comment |
| ---------- | ------- | -------- | -------------------- |

### Response data:

| code | type | comment |
| ----------------- | ------ | ---------------------------------------- |
| code | int | 0: success, other failure |
| message | string | Error message |
| data | Object| |
| ├─channel| String |Identifier |
| ├─instrumentId| Integer |Pair ID |
| ├─symbol| String |Trading pair |
| ├─count | Integer |24h rolling transaction count |
| ├─volume|String |24h transaction volume measured in quote currency |
| ├─amount| String |24h transaction volume measured in base currency |
| ├─close| String | Last transaction price|
| ├─open| String | First transaction price|
| ├─high| String | The highest transaction price|
| ├─low| String | The lowest transaction price|
| ├─bid| String | Buy one price|
| ├─bidSize| String | Buy a quantity|
| ├─ask| String | Sell one price|
| ├─askSize| String | Amount to sell|



 
## <span id="7">Get depth</span>
Get depth data

> Response

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
 
### HTTP request:
- GET /v1/market/depth/{symbol}

### Request parameters:

| code | type | required | comment |
| ---------- | ------- | -------- | -------------------- |
|symbol | string | true | trading pair, such as "BTCUSDT" |
|depth | int | false | The number of depths, such as "5, 10, 20, 50, 100", default 20 |

### Response data:

| code | type | comment |
| ----------------- | ------ | ---------------------------------------- |
| code | int | 0: success, other failure |
| message | string | Error message |
| data | Object| |
| ├─channel| String |Identifier |
| ├─level| Integer |Quantity |
| ├─instrumentId| Integer |Pair ID |
| ├─symbol| string |Trading pair |
| ├─a | List | Sell order, [${price}, ${quantity}, ${direction}] ] |
| ├─b | List | Buy order, [${price}, ${quantity}, ${direction}] ] |


## <span id="7">Get K line</span>
Get trading K line

> Response

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
 
### HTTP request:
- GET /v1/market/kline/{symbol}

### Request parameters:

| code | type | required | comment |
| ---------- | ------- | -------- | -------------------- |
|symbol | string | true | trading pair, such as "BTCUSDT" |
|period | String | false | Data granularity, such as "1min, 5min, 15min, 30min, 60min, 4hour, 12hour, 1day, 1week", the default is 20 |
|size | Integer | false | Number of data bars, [1,2000] |

### Response data:

| code | type | comment |
| ----------------- | ------ | ---------------------------------------- |
| code | int | 0: success, other failure |
| message | string | Error message |
| data | Object| |
| ├─channel| String |Identifier |
| ├─instrumentId| Integer |Pair ID |
| ├─symbol| String |Trading pair |
| ├─item | List |K-line collection |
| ├─├─interval | String |K line interval |
| ├─├─endTime| Long |End time, unit s |
| ├─├─amount|String |Amount |
| ├─├─startTime|Long |Start time, unit s|
| ├─├─firstTradeId|Long |First Trade ID |
| ├─├─lastTradeId|Long |Last Trade ID |
| ├─├─volume| String |Volume|
| ├─├─close| String | Last transaction price|
| ├─├─open| String | First transaction price|
| ├─├─high| String |Highest transaction price |
| ├─├─low| String |Lowest transaction price |



## <span id="7">Latest trades</span>
Get the latest trades record

> Response
 
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
  
### HTTP request:
- GET /v1/market/trade/{symbol}

### Request parameters:

| code | type | required | comment |
| ---------- | ------- | -------- | -------------------- |
|symbol | string | true | trading pair, such as "BTCUSDT" |
|size | Integer | false | Number of data bars, [1,100] |

### Response data:

| code | type | comment |
| ----------------- | ------ | ---------------------------------------- |
| code | int | 0: success, other failure |
| message | string | Error message |
| data | Object| |
| ├─channel| String |Identifier |
| ├─instrumentId| Integer |Pair ID |
| ├─symbol| String |Trading pair |
| ├─interval | String |K line interval |
| ├─time| Long |Time, unit s |
| ├─ts|Long |Time, unit ms |
| ├─tradeId|Long |The ID of the first transaction |
| ├─takerSide|String |Direction "BUY", "SELL" |
| ├─volume| String |Volume |
| ├─price| String | Last transaction price|                      
 
 

  
##  <span id="7"> Get the latest price of all symbols </span>
Get the latest price of all symbols

### HTTP Request: 
- GET /v1/ticker/price


> Response

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


### Request Parameters: 

|    code    |  type   | required |       comment        |
| ---------- | ------- | -------- | -------------------- |
|symbol | string | true | Trading pairs, case-sensitive, comma-separated |

**default: Query all symbols**

**query the specified symbol: /v1/ticker/price;symbol=btcusdt,eosusdt,autusdt**

### Response Data: 

|       code        |  type  |        example        |                      comment                       |
| ----------------- | ------ | --------------------- | -------------------------------------------------- |
| code            | int    | 0                     |     0: success, other: failure                                               |
| message         | string    |                 |    error message                                    |
| data | List[]|  | |
| ├─id| long |  | |
| ├─symbol| string |  |symbol|
| ├─price| string |  |price |



#Websocket Ticker Data

##  **Introduction**

### Access URL
wss://ws.coinstore.com/s/ws

1. The baseurl of all wss interfaces is: wss://<host:port>/s/ws

2. All symbols in stream name are lowercase

3. Each link is valid for no more than 24 hours. Please handle the disconnection and reconnection properly.

4. The server will send a ping frame every 3 minutes, and the clients should reply to the pong frame within 10 minutes, otherwise, the server will actively disconnect the link. Clients are allowed to send unpaired pong frames (that is, clients can send pong frames at a frequency higher than 10 minutes each time to maintain the link).



### Description of Server Push Data Type

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

##### Request-response message type

* `echo`: The server will return a message for each message received in the same form to confirm that the message has been received.
* `resp`: The server will return a message of processing result for each message received in the same form, marking the message processing. The return message includes the processing result.

##### subscribe message type
the following types of messages start from executing the `SUB` command of the corresponding channel and end with executing the `UNSUB` command, and the messages are pushed if the data on the server side changes, with a minimum push interval of 100ms.
```lang=json
{
    "S": 1,
    "T": "kline|ticker|depth|trade|account",
    ...:  // see below for details on each data subscription format 
}
```
* `kline`

* `ticker`

* `depth`

* `trade`

* `account`

* `order`


### Pong
the server supports two forms of end pong response, namely websocket pong frame and pong message.

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

## **Subscribe/unsubscribe data stream in real time**

> Subscribe an information stream

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

> Unsubscribe an information stream

```lang=json
{
    "op": "UNSUB",
    "channel": [
        "btcusdt@depth"
    ],
    "id": 312
}
```

> Subscribed information stream

```lang=json
{
    "op": "LIST",
    "channel": [],
    "id": 3
}
```

* The following data can be sent via websocket to subscribe or unsubscribe the data stream. Examples are as follows.
* The id in the response content is an unsigned integer, which is the unique identifier of the current information.
* If the result in the corresponding content is null, it means that the request was sent successfully.

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

> NOTE: `<symbol>` Please use the id of the symbol for the parameter temporarily, and the name of the symbol can be used later.

> IMPORTANT:  Please use the symbol' field first, and the instrumentId' is marked as Deprecated'.

> NOTE:  All the time to return data is in `seconds'.


## **Trade by Trade**

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
> full-volume data:

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
> incremental data:

```lang=json
{
  "instrumentId": 88066,
  "symbol": "USDTBTC",    // symbol
  "tradeId": 12345,       // trading ID
  "takerSide": "BUY",    // taker side
  "price": "0.001",     // strike price
  "volume": "100",       // trading volume
  "time": 123456785,   // trading time unit: s
  "ts": 1612685313400, // unit: ms
  "seq": 9935         // unique auto-increment serial number
}
```

### Stream Name
 `<symbol>@trade`
 
 eg: `88066@trade`
### param
`param": {"size":2}`





## **K-line Streams**
K-line stream pushes the requested k-line type (the latest k-line) every second.

> Send

```lang=json
 {"op":"SUB","channel":["4@kline@min_1"],"id":1}
```

> Receive

```lang=json
{
   "instrumentId": 88066, 
   "startTime": 1603732500,  // start time of this k-line, unit: s
    "endTime": 1603732559,  // start time of this k-line, unit: s
    "symbol": "USDTBTC",  // symbol
    "interval": "1m",      // K-line interval
    "firstTradeId": 100,       // first trade ID during this k-line period
    "lastTradeId": 200,       // last trade ID during this k-line period
    "open": "0.0010",  // first strike price during this k-line period
    "close": "0.0020",  // last strike price during this K-line period
    "high": "0.0025",  // highest strike price during this K line period
    "low": "0.0015",  // lowest strike price during this K line period
    "volume": "1000",    // trading volume during this K line period
    "amount": "1.0000",  // trading amount during this k-line period
}
```

### Stream Name
`<symbol>@kline@<interval>`

eg: `88066@kline@min_1`


### interval optional values
* min_1
* min_5
* min_15
* min_30
* hour_1
* hour_4
* hour_12
* day_1
* week_1
* mon_1



## **K-line Request**
request historical k-line

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

### param

* `channel`: `<symbol>@kline@<interval>` ,eg:88066@kline@min_1
* `limit`: No more than 200
* `endTime`: Optional, exclusive




## **Ticker Information by Symbol**
Streamlined ticker information in the last 24 hours refreshed by Symbol

> Send

```lang=json
 {"op":"SUB","channel":["4@ticker"],"id":1}
```

> Receive

```lang=json
{
  "instrumentId": 88066,
  "symbol": "USDTBTC",      // symbol
  "open": "0.0010",      // first strike price counted backwards 24 hours ago
  "high": "0.0025",      // highest strike price within 24 hours
  "low": "0.0010",      // lowest strike price within 24 hours
  "close": "0.0025",      // latest strike price
  "volume": "10000",       // trading volume within 24 hours
  "amount": "18"          // trading amount within 24 hours
}
```

### Stream Name
`<symbol>@ticker`, eg:  `88066@ticker`


## **Ticker information of all symbols in the whole market**
Streamlined ticker information in the last 24 hours refreshed by Symbol

### Stream Name
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

## **Depth information**
Push limited level depth information every second or every 100ms. 

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

### levels optional values
Levels indicates the levels of the buy or sell orders, and 5/10/20/50/100 level can be selected.




## **Login**

Specify subscription data when logging in

```lang=json
{
    "op": "LOGIN",
    "channel": [
        "7@account",
        "80002@order"
    ],
    "auth": {
        "user-id":4930,     // user id of user login account or user id of sub-account
        "token": "eb737c84862f3d"  // token after login; the client user uses the token after login, and the api user fills in the api-key
        "type": "token"      // optional values: token | api; default value is token; api users fill in api
    }
    "id": 2
}
```

## **Account**

Stream Name: `<currency>@account`, or `!@account` all currency

```lang=json
{
    "accountId": 12,
    "currency": "BTC",
    "frozen": "21.03",
    "available": "3128.29",
    "timestamp": 1602493840  // unit: s
}

```

## **Closing order**

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

    "timestamp": 1602493840 // unit: s
}
```

## **Dictionary**

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

