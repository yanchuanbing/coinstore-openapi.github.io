---
title: Exchange-official-API-docs

language_tabs: # must be one of https://git.io/vQNgJ
  

toc_footers:

includes:
  
language: English

other_language: 简体中文

url: /wbfapi/cn

present_url: /wbfapi/en

active: active

other_active:

menu: Menu

create_api: Create API Key

spot_goods: Exchange 

spot_goods_active: active

contract: Contract

contract_active: active

contract_url: 'https://doc.wbfutures.pro/index.html'

searchText: Search

search: true

code_clipboard: true
---

# Introduction

Welcome to WBF Developer Documentation. It is the only official documentation of WBF API.

This documentation provides an introduction to the use of related APIs. RESTful API includes interfaces such as assets, orders and tickers. Websocket provides ticker-related interface and push service. The capabilities provided by WBF API will be continuously updated here, so stay tuned for updates.

You can switch among the APIs of different transactions by clicking the menu at the top, and switch the documentation language by clicking the language button at the top right.

On the right side of the documentation are examples of request parameters and response results.


# Quick Start

## Access Preparation
To use API, please log into the webpage first, create an API key through [User Center] - [API], and then develop and trade according to the details of this documentation.

You can click [here](https://www.wbfex.biz/personal/apiManagement) to create an API Key.

Each user can create 5 groups of API Keys, and each group of API Keys can bind 5 different IP addresses. Once an API key binds an address, the API interface can only be called by using the API key from the bound IP address. For security reasons, it is strongly recommended that you bind the corresponding IP address for API key.

Please remember the following information upon successful creation:

- `API Key`  API access key
- `Secret Key` Key for encryption of signature authentication

## Interface Type
WBF provides users with two interfaces, and you can choose the appropriate way to query the tickers and trade according to your own usage scenarios and preferences.

**REST API**

REST, the abbreviation of Representational State Transfer, is a popular Internet transmission architecture. It has the characteristics of clear structure, standards compliance, easy understanding and expansion, and is being adopted by more and more websites with the benefits as follows:

- In RESTful architecture, each URL represents a resource;
- Some representation layer of this resource is transferred between the client and the server;
- The client operates the server-side resources through four HTTP instructions to realize "presentation layer state transformation. 

Developers are advised to use REST API for one-time operations such as trades or assets.

**WebSocket API**

WebSocket is a new HTML5 Protocol. It realizes full-duplex communication between client and server, and the connection between client and server can be established by a simple handshake. The server can actively push information to the client according to business rules.

Developers are advised to use WebSocket API to obtain market tickers, asks/bids depth and other information.

### Interface Authentication

The above two interfaces include public interface and private interface.

Public interface can be used to obtain basic information and ticker data. Public interfaces can be called without authentication.

Private interface can be used for trading management and account management. Every private request must use your API Key for signature verification.

## Access URLs

**REST API**

`https://openapi.wbfex.biz`

**WebSocket**

`wss://ws.wbfex.biz/kline-api/ws`

To ensure the stability of API service, it is recommended to access using Japanese AWS cloud server. If the client server in Chinese mainland is used, it would be difficult to guarantee the stability of the connection.


## <span id="a4">Signature Authentication</span>

**Signature Description**

The API request is very likely to be tampered in the process of transmission through the Internet. In order to ensure that the request has not been changed, all private interfaces other than public interfaces (basic information, ticker data) must use your API Key for signature authentication to verify whether the parameters or parameter values have changed during transmission.

**Signature Steps (recommended)**
    
1、Request a method (GET or POST), followed by a line break  “\n”

Example: `GET\n`

2、Visit the domain name, followed by a line break "\n"

Example：`openapi.wbfex.biz\n`

3、Access interface path, followed by a line break "\n"

Example：`/open/api/create_order\n`

4、Sort the interface parameters in ascending dictionary order of their parameter names and connect them with the character "&"
Example：

`api_key=b80609d133fd8baa0999f232c7d24331&price=17&side=BUY&symbol=abcusdt&time=1596253598000&type=1&volume=10`

5、Compose the string to be signed finally
Example：

`GET\n`

`openapi.wbfex.biz\n`

`/open/api/create_order\n`

`api_key=b80609d133fd8baa0999f232c7d24331&price=17&side=BUY&symbol=abcusdt&time=1596253598000&type=1&volume=10`

6、Use the final "request string" and your Secret Key to generate a digital signature
1. With the request string and API private key obtained in the previous step as two parameters, call HmacSHA256 hash function to obtain hash value.
2. Encode this hash value with base-64 to get the value as the digital signature for this interface call.
   
`89ADx5A3TLyMWVQj8Gqp+N6w+ivaA8I5Oi2SuYtKKYo=`

7、Add the digital signature to the request parameter 
`sign=89ADx5A3TLyMWVQj8Gqp+N6w+ivaA8I5Oi2SuYtKKYo=`


**Signature Steps (backward compatible)**

For security reasons, this signature method is not recommended

Generate a string to be signed
    -   [open-api Demo](#open-api-java)
    
1、First sort the parameters in ascending dictionary order of their parameter names

2、Traverse the sorted dictionary, and concatenate all parameters together in the format of "keyvalue" (non-nullable parameters)

Example：
`api_key1234567time1596250406402type1`

3、Concatenate the user's Secret Key API
`api_key1234567time1596250406402type109d133fd8baa0999f232c7d24331`

4、Use MD5 against the above string to obtain a signature string

`sign = md5("api_key1234567time1596250406402type109d133fd8baa0999f232c7d24331")`


# API Access Instructions

## <span id="a3">Request Format</span>
All API requests are restful, and there are only two methods at present: GET and POST. 
- GET request: all parameters are in path parameters;
- POST request: all parameters are sent in JSON format to request body.

A licit request consists of the following parts：
- method request address: i.e. the access server address.https://openapi.wbfex.biz/open/api/create_order
- required and optional parameters.

  The following parameters must be provided for all interfaces requiring signature authentication:
  - API key: access Key in API Key applied by user, with its field name of "api_key"
  - request time: time when the request was initiated, with its field name of "time"
  - signature: value obtained through signature calculation, with its field name of "sign"

All requests must contain the following Header:

- The ACCESS-KEY API KEY is used as a string.
- ACCESS-SIGN uses base64 encoding for signing (see signing messages).
- ACCESS-TIMESTAMP is used as the timestamp of your request.
- Content-Type needs to be uniformly set to: 'application/x-www-form-urlencoded'.

## <span id="a3">Return Format</span>

All interface returns are in JSON format. There are three fields in the first layer of JSON: code, msg and data. The first two fields indicate the request status and description, and the actual business data is in the data field.

```json
{
    "code": "0",
    "msg": "suc",
    "data": // per API response data in nested JSON object
}
```
The following is an example of a return format：

| Field | Data Type  | Description       |
| ---- | -----  | ---------- |
| code | string | Return status |
| msg  | string | Status or error description |
| data | object | Transaction data  |


## Error Message

**HTTP Status Code**

Common error codes for HTTP are as follows: 
- 400 Bad Request - Invalid request format  

- 401 Unauthorized – Invalid API Key 

- 403 Forbidden – You do not have access to the requested resource 

- 404 Not Found 

- 429 Too Many Requests 

- 500 Internal Server Error – We had a problem with our server 

**Transaction Status Code**

In case of failure, the response body carries error description information, and the corresponding status code is described as follows：

| Status Code | Description                             | Remarks                       |
| ------ | -------------------------------- | -------------------------- |
| 0      | Success                             | code=0 success， code >0 failure |
| 1      | System error                         |                            |
| 2      | Parameter error                         |                            |
| 3      | User is not logged in                       |                            |
| 4      | Insufficient balance                         |                            |
| 5      | Ordering failed                         |                            |
| 6      | Quantity is less than min. value                   |                            |
| 7      | Quantity is greater than max. value                   |                            |
| 8      | Order cancellation failed                     |                            |
| 9      | Trading is frozen                       |                            |
| 11     | Incorrect or expired SMS verification code             |                            |
| 12     | Unknown token code                   |                            |
| 14     | Incorrect verification code                       |                            |
| 19     | Insufficient available balance                     |                            |
| 21     | Incorrect or expired Google verification code             |                            |
| 22     | Order doesn't exist                       |                            |
| 25     | Price or quantity precision exceeds the max. limit       |                            |
| 26     | User doesn't exist                      |                            |
| 28     | Incorrect or expired Email verification code             |                            |
| 31     | Price or amount is less than the min. value           |                            |
| 32     | Price exceeds the limit move range of the day, and order cannot be placed |                            |
| 33     | Orders exceed the max. limit           |                            |
| 34     | Order exceeds frequency limit                 |                            |
| 10069  | Limit move limits the token pair, and market orders are not allowed    |                            |
| 100100 | Incorrect username or password                 |                            |
| 100101 | User login locked for two hours               |                            |
| 100102 | User is frozen                       |                            |
| 101115 | Current trading volume exceeds the user's daily trading limit   |                            |
| 101117 | Market price dealing is not allowed in current token           |                            |
| 101999 | This token is not activated                     |                            |


## <span id="a9">Standard Specifications</span>

### <span id="b1">Time Stamp</span> 
Unless otherwise specified, all timestamps in the API are returned in ms.

The ACCESS-TIMESTAMP in the request signature is in seconds, allowing a more accurate time to be expressed in decimals. The timestamp of the request must be within 30 seconds of the API service time, otherwise the request will be deemed expired and rejected. In case of a significant deviation between the local server time and the API server time, we recommend that you update the http header by querying the API server time.

###  <span id="b2">Example</span> 
`1524801032573`

###  <span id="b3">Digits</span> 
In order to maintain the integrity of cross-platform precision, decimal digits are returned as strings. It is recommended that you convert digits into strings when making requests to avoid truncation and precision errors.

Integers, such as trading numbers and sequences, are not quoted.

###  <span id="b4">Current-limiting</span>  
If there are too many requests, the system will automatically limit the requests and return the status code of "429 too many requests" in the http header.

REST API

- Public interface: We restrict the call of public interface through IP: up to 6 requests every 2 seconds.

- Private interface: We restrict the call of private interface by user ID: up to 10 requests per second.

- Special restrictions of some interfaces are indicated on specific interfaces


# Account Related


## <span id="1">Assets Balance</span>

Get user assets balance

### HTTP Request: 
- GET /open/api/user/account


> Response 

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

### Request Parameters

| Parameter    | Fill-in Type | Description    |
| ------- | -------- | ------- |
| api_key | Mandatory     | api_key |
| time    | Mandatory     | Time stamp  |
| sign    | Mandatory     | Signature    |

### Response Data

| Field | Example  | Description       |
| ---- | ----- | ---------- |
| code | 0     |            |
| msg  | "suc" | code>0 failure |
| data | As shown on the right: |

# Order Related

## <span id="2">Get all orders</span>

Get all orders

### HTTP Request: 

- GET /open/api/v2/all_order

- The old interface /open/api/all_order  is retained, but it is no longer recommended
- v2 version change: The tradeList record in the result return value is removed to improve the efficiency. If the trading information of a single order is required, the  /open/api/order_info interface may be used.



> Response

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
                "side_msg":"buy",
                "volume":"1.000",
                "price":"0.10000000",
                "source_msg":"WEB",
                "status_msg":"fully filled",
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

### Request Parameters

| Parameter      | Fill-in Type | Description                                           |
| --------- | -------- | ----------------------------------------------- |
| symbol    | Mandatory     | Symbol，btcusdt                  |
| startDate | Optional    | (added) start time, accurate to seconds “yyyy-MM-dd mm:hh:ss” |
| endDate   | Optional     | (added) end time, accurate to seconds“yyyy-MM-dd mm:hh:ss” |
| pageSize  | Optional     | Page size                                       |
| page      | Optional     | Page                                            |
| api_key   | Mandatory     | api_key                                    |
| time      | Mandatory     | Time stamp                                          |
| sign      | Mandatory     | Signature                                            |

### Response Data

| Field | Example  | Description       |
| ---- | ----- | ---------- |
| code | 0     |            |
| msg  | "suc" | code>0 failure |
| data | As shown on the right: |


## <span id="3">Get all trading records</span>
Get all trading records

### HTTP Request: 
- GET /open/api/all_trade


> Response

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

### Request Parameters

| Parameter      | Fill-in Type | Description                                            |
| --------- | -------- | ----------------------------------------------- |
| symbol    | Mandatory     | Symbol，btcusdt                   |
| startDate | Optional     | (added) start time, accurate to seconds“yyyy-MM-dd HH:mm:ss” |
| endDate   | Optional     | (added) end time, accurate to seconds“yyyy-MM-dd HH:mm:ss” |
| pageSize  | Optional     | Page size                                        |
| page      | Optional    | Page                                            |
| api_key   | Mandatory     | api_key                                      |
| time      | Mandatory     | Time stamp                                          |
| sort      | Optional      | 1 indicates reverse order                                       |
| sign      | Mandatory     | Signature                                            |

### Response Data

| Field | Example  | Description       |
| ---- | ----- | ---------- |
| code | 0     |            |
| msg  | "suc" | code>0 failure |
| data | As shown on the right: |


##  <span id="4">Cancel orders</span>
Cancel orders

### HTTP Request: 
- POST /open/api/cancel_order

### Request Parameters

| Parameter     | Fill-in Type | Description                         |
| -------- | -------- | ---------------------------- |
| order_id | Mandatory     | Order ID                       |
| symbol   | Mandatory     | Symbol, ethbtc |
| api_key  | Mandatory     | api_key                      |
| time     | Mandatory     | Time stamp                       |
| sign     | Mandatory     | Signature                         |

### Response Data

| Field | Example  | Description       |
| ---- | ----- | ---------- |
| code | 0     |
| msg  | "suc" | code>0 failure |
| data | “”    |

##  <span id="5">Cancel all orders</span>
Cancel all orders according to token pair (up to 2000 orders can be canceled upon a single request, and the interface needs to be called repeatedly for a request of more than 2000 orders)

### HTTP Request: 
- POST /open/api/cancel_order_all

### Request Parameters
| Parameter   | Fill-in Type | Description                         |
| ------- | -------- | ---------------------------- |
| symbol  | Mandatory     | Symbol, ethbtc |
| api_key | Mandatory     | api_key                      |
| time    | Mandatory     | Time stamp                       |
| sign    | Mandatory     | Signature                         |

### Response Data

| Field | Example  | Description       |
| ---- | ----- | ---------- |
| code | 0     |
| msg  | "suc" | code>0 failure |
| data | “”    |


## <span id="6">Create Order</span>
Create Order

### HTTP Request: 
- POST /open/api/create_order

### Request Parameters

| Parameter                      | Fill-in Type | Description                                                                                                                 |
| ------------------------- | -------- | -------------------------------------------------------------------------------------------------------------------- |
| side                      | Mandatory     | BUY/SELL                                                                                                    |
| type                      | Mandatory     | Order type: 1: limit order, 2: market order                                                                                     |
| volume                    | Mandatory     | Volume (polysemy, reused fields)<br>Type=1: indicates the buying and selling quantity<br>Type=2: indicates the total price (BUY) and the total quantity (SELL)<br>Trading restriction user/me- user information |
| price                     | Optional     | Order price: type=2: this parameter is not required                                                                                       |
| symbol                    | Mandatory     | Symbol，ethbtc                                                                                                     |
| fee_is_user_exchange_coin | Optional     | (redundant field, ignored) 0, when all platform tokens are traded, this parameter indicates whether to use platform tokens to pay the commission charges, 0 - No, 1 - Yes                              |
| api_key                   | Mandatory     | api_key                                                                                                              |
| time                      | Mandatory     | Time stamp                                                                                                              |
|  sign  | Mandatory | Signature |                                                                     


> Response

```json
{
    "code": "0",
    "msg": "suc",
    "data": {
        "order_id":34343
    }
}
```                                 

### Response Data

| Field | Example               | Description           |
| ---- | ------------------ | -------------- |
| code | 0                  |
| msg  | "suc"              | code>0 failure     |
| data | As shown on the right： | Trade ID returned successfully |

## <span id="13">Batch ordering/cancellation</span>
Place orders in batches, or cancel designated orders in batches
### HTTP Request: 
- POST /open/api/mass_replace

### Request Parameters

| Parameter        | Fill-in Type | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| ----------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| api_key     | Mandatory     | api_key                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| time        | Mandatory     | Time stamp                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| sign        | Mandatory     | Signature                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| symbol      | Mandatory     | Coin, for example btcusdt                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| mass_cancel | Optional     | [1234,234....] cancellation parameter, order id                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| mass_place  | Optional     | [{side:" BUY",type:" 1",volume:" 0.01",price:" 6400",fee_is_user_exchange_coin:" 0"}, {}, …]<br>Meaning：<br>symbol: Coin, for example, btcusdt<br>mass_place: Ordering parameter. side: direction (BUY and SELL),<br>--------------------------------type: type (1: limit order, 2: market order)<br>--------------------------------volume: volume (polysemy, reused fields) type=1: indicates the buying and selling quantity type=2: indicates the total price (BUY) and the total quantity (SELL)<br>--------------------------------price: order price: type=2: this parameter is not required<br>--------------------------------fee_is_user_exchange_coin: (redundant field) when all platform coins are traded, this parameter indicates whether to use platform coins to pay the commission charges, 0 - No, 1 - Yes |

* Mass_place indicates a batch of limit orders that need to be sent to the system, with a maximum of 100 orders at a time
* Mass_cancel indicates a batch of orders that need to be canceled, with a maximum of 100 orders at a time

> Response

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

### Response Data
| Field | Example  | Description       |
| ---- | ----- | ---------- |
| code | 0     |
| msg  | "suc" | code>0 failure |
| data | As shown on the right    | <br>Ordering return: order id, status code, success (0)/ failure (1)<br> <br>Cancellation return: order id, status code, success (0)/ failure (1)<br>|



## <span id="27">Batch ordering/cancellation - V2</span>
Place orders in batches, or cancel designated orders in batches

### HTTP Request: 
- POST /open/api/mass_replaceV2

### Request Parameters
| Parameter        | Fill-in Type | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| ----------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| api_key     | Mandatory     | api_key                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| time        | Mandatory     | Time stamp                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| sign        | Mandatory     | Signature                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| symbol      | Mandatory     | Coin, for example,  btcusdt                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| mass_cancel | Optional     | [1234,234....] \ [1234,234 ...] cancellation parameter, order id                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| mass_place  | Optional     | [{side:"BUY",type:"1",volume:"0.01",price:"6400",fee_is_user_exchange_coin:"0"}, {}, …]<br>Meaning：<br>symbol: Coin, for example, btcusdt<br>mass_place: ordering parameter -->side: direction (BUY and SELL),<br>--------------------------------type: type (1: limit order, 2: market order)<br>--------------------------------volume: volume (polysemy, reused fields) type=1: indicates the buying and selling quantity type=2: indicates the total price (BUY) and the total quantity (SELL)<br>--------------------------------price: order price: type=2: this parameter is not required<br>--------------------------------fee_is_user_exchange_coin: (redundant field) when all platform coins are traded, this parameter indicates whether to use platform coins to pay the commission charges, 0 - No, 1 - Yes |

* Mass_place indicates a batch of limit orders that need to be sent to the system, with a maximum of 1000 orders at a time

* Mass_cancel indicates a batch of orders that need to be canceled, with a maximum of 1000 orders at a time


> Response

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

### Response Data

| Field | Example  | Description       |
| ---- | ----- | ---------- |
| code | 0     |
| msg  | "suc" | code>0 failure |
| data | As shown on the right    | <br>Ordering return: order id, status code, success (0)/ failure (1)<br> <br>Cancellation return: order id, status code, success (0)/ failure (1)<br>|


## <span id="14">Get current orders</span>
Get current orders, including unfilled orders and partially filled orders

### HTTP Request: 
- GET /open/api/v2/new_order

* The old interface /open/api/new_order is retained, but it is no longer recommended

* V2 version change: The tradeList record in the result return value is removed to improve the efficiency. If the trading information of a single order is required, the /open/api/order_info interface may be used.

> Response

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
                "side_msg":"buy",
                "volume":"1.000",
                "price":"0.10000000",
                "source_msg":"WEB",
                "status_msg":"fully filled",
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

### Request Parameter:

| Parameter     | Fill-in Type  | Description                          |
| -------- | --------- | ----------------------------- |
| symbol    | Mandatory      | Symbol, btcusdt |
| pageSize | Optional       | Page size                      |
| page     | Optional       | Page                          |
| api_key  | Mandatory      | api_key                       |
| time     | Mandatory      | Time stamp                        |
| sign     | Mandatory      | Signature                          |

### Response Data:

| Field | Example  | Description                                                                                                                                                                                                                                          |
| ---- | ----- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| code | 0     |
| msg  | "suc" | code>0 failure                                                                                                                                                                                                                                    |
| data | As shown on the right: | Order status description：<br>INIT (0, "initial order, unfilled and not in the handicap"),<br>NEW_ (1, "new order, unfilled but in the handicap"),<br>FILLED (2, "fully filled"),<br>PART_FILLED (3, "partially filled"),<br>CANCELED (4, "canceled order"),<br>PENDING_CANCEL (5, "pending cancellation"),<br>EXPIRED (6, "abnormal order"); |


##  <span id="15">Get order details</span>
Get order details
### HTTP Request: 
- GET /open/api/order_info


> Response

```json
{
    "code": "0",
    "msg": "suc",
    "data": {
        "order_info":{
            "id":343,
            "type":3,
            "side":"sell",
            "side_msg":"sell",
            "created_at":"1592461624033",
            "price":222.33,
            "volume":222.33,
            "deal_volume":222.33,
            "total_price":222.33,
            "fee":222.33,
            "source":3,
            "source_msg":"API",
            "status":0, 
            "status_msg":"unfilled",
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

### Request Parameters

| Parameter     | Fill-in Type | Description                         |
| -------- | -------- | ---------------------------- |
| order_id | Mandatory     | Order ID                       |
| symbol   | Mandatory     | Symbol, ethbtc |
| api_key  | Mandatory     | api_key                      |
| time     | Mandatory     | Time stamp                       |
| sign     | Mandatory     | Signature                         |

### Response Data:

| Field | Example  | Description                                                                                                                                                                                                                                          |
| ---- | ----- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| code | 0     |
| msg  | "suc" | code>0 failure                                                                                                                                                                                                                                    |
| data | As shown on the right: | Order status description：<br>INIT (0, "initial order, unfilled and not in the handicap"),<br>NEW_ (1, "new order, unfilled but in the handicap"),<br>FILLED (2, "fully filled"),<br>PART_FILLED (3, "partially filled"),<br>CANCELED (4, "canceled order"),<br>PENDING_CANCEL (5, "pending cancellation"),<br>EXPIRED (6, "abnormal order"); |

# Tickers Related
##  <span id="7">Get all symbol tickers</span>
Get all symbol tickers

```
curl https://openapi.wbfex.biz/open/api/get_allticker
```
### HTTP Request: 
- GET /open/api/get_allticker


> Response

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


### Request Parameter: 
* This interface doesn't require signature verification
* Parameter: None

### Response Data: 
| Field | Example   | Description                                                                                                                                                                                                                                    |
| ---- | ------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| code | 0      |
| msg  | "suc"  | code>0 failure                                                                                                                                                                                                                              |
| data | As shown on the right： | Return value description<br>date: server time when data is returned  <br>symbol: Symbol (symbol 1(base) _ symbol 2(quote))  <br>buy: buy price <br>high: highest price  <br>last: last strike price <br>low: lowest price  <br>sell: sell price <br>vol: volume (last 24 hours)<br>rose:rose |

## <span id="8">Get K-line data</span>
Get K-line data

```
curl https://openapi.wbfex.biz/open/api/get_records?symbol=btcusdt&period=15
```

### HTTP Request:

GET /open/api/get_records


> Response

```json
{
    "code": "0",
    "msg": "suc",
    "data": [
        [
            1595727000,  //Time，second
            9674.4758,   //Open
            9675.8867,   //High
            9652.9081,   //Low
            9654.2835,   //Close
            369.67841    //Amount
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
### Request Parameter:
* This interface doesn't require signature verification

| Parameter   | Fill-in Type | Description                                     |
| ------ | -------- | ---------------------------------------- |
| symbol | Mandatory     | Symbol, bchbtc             |
| period | Mandatory     | Unit: minute, 1 minute = 1; 1 day = 1440 |

### Response Data:

| Field | Example   | Description       |
| ---- | ------ | ---------- |
| code | 0      |
| msg  | "suc"  | code>0 failure |
| data | As shown on the right： |


##  <span id="9">Get current tickers</span>
Get current tickers

```
curl https://openapi.wbfex.biz/open/api/get_ticker?symbol=btcusdt
```

### HTTP Request:
- GET /open/api/get_ticker

> Response

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

### Request Parameter:
* This interface doesn't require signature verification

| Parameter   | Fill-in Type | Description                          |
| ------ | -------- | ----------------------------- |
| symbol | Mandatory     | Symbol, btcusdt |


### Response Data:

| Field | Example   | Description       |
| ---- | ------ | ---------- |
| code | 0      |
| msg  | "suc"  | code>0 failure |
| data | As shown on the right： |


## <span id="10">Get trading records</span>
Get the real-time trading records of symbols (up to 200 latest trading records are returned)

```
curl https://openapi.wbfex.biz/open/api/get_trades?symbol=btcusdt
```

### HTTP Request:
- GET /open/api/get_trades

> Response

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
### Request Parameter:

* This interface doesn't require signature verification

| Parameter   | Fill-in Type | Description                         |
| ------ | -------- | ---------------------------- |
| symbol | Mandatory     | Symbol, bchbtc |

### Response Data:

| Field | Example   | Description       |
| ---- | ------ | ---------- |
| code | 0      |
| msg  | "suc"  | code>0 failure |
| data | As shown on the right： |


##  <span id="11">Get the latest strike price of each token pair</span>
Get the latest strike price of each token pair

```
curl https://openapi.wbfex.biz/open/api/market
```

### HTTP Request:

- GET /open/api/market

> Response

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
### Request Parameter:
* This interface doesn't require signature verification

### Response Data:

| Field | Example                            | Description       |
| ---- | ------------------------------- | ---------- |
| code | 0                               |
| msg  | "suc"                           | code>0 failure |
| data | As shown on the right: |



##  <span id="12">Query asks depth</span>
Query asks depth

```
curl https://openapi.wbfex.biz/open/api/market_dept?symbol=btcusdt&type=step0
```

### HTTP Request:
- GET /open/api/market_dept


> Response

```json
{
    "code": "0",
    "msg": "suc",
    "data": { 
        "tick":{
            "asks":[//asks，Price from low to high
                [10968.4775,0.9332],
                [10985.9564,0.1222],
                [10993.3291,0.2778],
                [10995.8029,0.5882],
                [10998.8093,0.2982],
                ...
            ],
            "bids":[//bids, Price from high to low
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

### Request Parameter:
* This interface doesn't require signature verification

| Parameter   | Fill-in Type | Description                                                            |
| ------ | -------- | --------------------------------------------------------------- |
| symbol | Mandatory     | Symbol, ethbtc                                    |
| type   | Mandatory    | Depth type, step0, step1, step2 (combined depth 0-2); highest precision at step0 |

### Response Data:

| Field | Example   | Description       |
| ---- | ------ | ---------- |
| code | 0      |
| msg  | "suc"  | code>0 failure |
| data | As shown on the right： |


## <span id="17">Query all symbols supported by the system</span>
Query all symbols supported by the system

```
curl https://openapi.wbfex.biz/open/api/common/symbols
```

### HTTP Request:

- GET /open/api/common/symbols


> Response

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

### Request Parameter:
* Parameter: None

### Response Data:

| Field | Example   | Description                                                                                                                                              |
| ---- | ------ | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| code | 0      |
| msg  | "suc"  | code>0 failure                                                                                                                                        |
| data | As shown on the right： | symbol <br>base_coin <br>count_coin <br>price_precision (0 is one digit)<br>amount_precision  (0 is one digit) |


# Websocket Ticker Data

## Introduction

### Access URL

`
wss://ws.wbfex.biz/kline-api/ws
`

### Data Compression
All the data returned by WebSocket ticker interface are compressed by GZIP, which requires the client to decompress the data after receiving it.

### Heartbeat Message
When the Websocket client of the user connects to the Websocket server of WBF, the server will send ping messages to it periodically (currently at 5 seconds) and contain an integer value as shown on the right:

`
{"ping": 1595935723334}
`

When the Websocket client of the user receives this heartbeat message, it should return a pong message containing the same integer value:

`
{"pong": 1595935723334}
`

> Subscribe

```json
{
    "event": "sub", 
    "params":{
        "channel":"market_$base$quote_kline_1min",
        "cb_id":"10001"
    }
}
```


When the Websocket server sends ping messages for three consecutive times but does not receive any pong messages, the server will actively disconnect from this client.


> Subscribe Response

```json
{
    "event_rep":"subed",
    "channel":"market_$base$quote_kline_1min",
    "cb_id":"10001",
    "ts":1506584998239,
    "status":"ok"
}
```

### Subscribe to Topics
After the connection with Websocket server is successfully established, Websocket client sends a request as shown on the right to subscribe to a specific topic.

`
{
    "event": "sub", 
    "params":{
        "channel":"channel name",
        "cb_id":"id generated by client"
    }
}
`

Upon successful subscription, Websocket client will receive the confirmation. After that, once the subscribed topic is updated, the Websocket client will receive an update message (push) pushed by the server.

> Unsubscribe

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

### Unsubscribe
The client may unsubscribe the topic in the following format:

`
{
    "event": "unsub", 
    "params":{
        "channel":"channel name",
        "cb_id":"id generated by client"
    }
}
`

## <span id="19">Subscribe to K-line ticker</span>

### channel
`market_$symbol_kline_$period`

> Subscribe

```json
{
    "event":"sub",
    "params":{
        "channel":"market_btcusdt_kline_5min",
        "cb_id":"10001"
    }
}
```
### Subscription Parameters

| Parameter | Data Type | Mandatory or Not  | Description | Value Range |
| ---- | ------ | ---------- | ---- | ----- |
| channel | string | Yes  | Topic |$symbol: Symbol name <br>btcusdt,ethusdt<br> $period: K-line period<br>1min,5min,15min,30min,60min,1day,1week,1month</br>|
| cb_id | string | Yes  | Customized id      | |

> Response

```json
{
    "channel":"market_btcusdt_kline_5min",
    "ts":1506584998239,
    "tick":{
        "id":1506602880,//Time scale start value
        "amount":123.1221,//amount
        "vol":1212.12211,//volume
        "open":2233.22,//opening price
        "close":1221.11,//closing price
        "high":22322.22,//highest price
        "low":2321.22//lowest price
    }
}
```
### Update Data Fields
| Field | Data Type | Description |
|-----|-----|-----|
| id | long  | Time scale start value |
| amount | float  |  amount |
| vol | float  | volume |
| open | float  | opening price|
| close | float  | closing price|
| high | float  | highest price |
| low | float  | lowest price|




## <span id="20">Subscribe to 24-hour Ticker</span>

### channel
`maket_$symbol_ticker`

> Subscribe

```json
{
    "event":"sub",
    "params":{
        "channel":"market_btcusdt_ticker",
        "cb_id":"10002"
    }
}
```

### Subscription Parameters

| Parameter | Data Type  | Mandatory or Not  | Description | Value Range |
| ---- | ------ | ---------- | ---- | ----- |
| channel | string | Yes  | Topic |$symbol: Symbol name <br>btcusdt,ethusdt ...</br>|
| cb_id | string | Yes  | Customized id     | |


> Response

```json
{
    "channel":"market_btcusdt_ticker",
    "ts":1506584998239,//
    "tick":{
        "id":1506584998,//Time scale start value
        "amount":123.1221,//amount
        "vol":1212.12211,//volume
        "open":2233.22,//opening price
        "close":1221.11,//closing price
        "high":22322.22,//highest price
        "low":2321.22,//lowest price
        "rose":-0.2922,//rose
        "ts":1506584998239,
        "lower_frame":"0"
    }
}
```

### Update Data Fields
| Field | Data Type | Description |
|-----|-----|-----|
| id | long  | Time scale start value |
| amount | float  | amount |
| vol | float  | volume |
| open | float  | opening price |
| close | float  | closing price|
| high | float  | highest price |
| low | float  | lowest price |



##  <span id="21">Subscribe to Market Depth Ticker Data</span>

### channel
`maket_$symbol_depth_$step`

> Subscribe

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

### Subscription Parameters

| Parameter | Data Type  | Mandatory or Not  | Description | Value Range|
| ---- | ------ | ---------- | ---- | ----- |
| channel | string | Yes  | Topic |$symbol: symbol name  <br>  btcusdt,ethusdt ...</br> <br> $step: aggregation precision <br> step0: disaggregation  <br>step1: system precision is configurable<br>step2: system precision is configurable<br>|
| cb_id | string | Yes  | Customized id      | |
| asks | integer | No  | asks gear     | |
| bids | integer | No  | bids gear     | |


### Update Data Fields

* Note: If the first subscription is successful, the full-volume data will be returned immediately. In addition, the server will regularly push the full-volume data to the front end to avoid data problems.


> Full-volume Handicap Response

```json
{
    "channel":"market_btcusdt_depth_step0",
    "ts":1506584998239,//Request time
    "tick":{
        "asks":[//sell
            [22112.22,0.9332],
            [22112.21,0.2],
        ],
        "buys":[//buy
            [22111.22,0.9332],
            [22111.21,0.2],
        ]
    }
}
```
#### Full-volume Handicap Data

* It can directly replace the original handicap

| Field | Data Type| Description |
|-----|-----|-----|
| asks | array object  | asks depth |
| buys | array object  | buys  depth |



> Incremental Handicap Response

```json
{
    "channel":"market_btcusdt_depth_step0",
    "ts":1506584998239,//Request time
    "tick":{
        "side": "asks", //asks/buys direction   asks: asks  buys: buys
        "price" : 133.55,  
        "volume" : 44.22   
    }
}
```

#### Incremental Handicap Data

* Replace the quantity corresponding to the price. Delete it when volume=0. Update the volume when price is equal to the price of a certain price segment of the original handicap, and add the new price directly.

| Field | Data Type| Description |
|-----|-----|-----|
| side | string  | asks/buys direction <br>asks: asks</br> <br>buys</br>|
| price | float  | price |
| volume | float  | volume |


## <span id="23">Subscribe to Real-time Trading Information </span>

### channel
`maket_$symbol_trade_ticker`


> Subscribe

```json
{
    "event":"sub",
    "params":{
        "channel":"market_btcusdt_trade_ticker",
        "cb_id":"10001"
    }
}
```

### Subscription Parameters

| Parameter | Data Type  | Mandatory or Not  | Description | Value Range |
| ---- | ------ | ---------- | ---- | ----- |
| channel | string | Yes  | Topic |$symbol: symbol name<br>btcusdt,ethusdt ...</br>|
| cb_id | string | Yes  | Customized id     | |

> Response

```json
{
    "channel":"market_btcusdt_trade_ticker",
    "ts":1506584998239,//Request time
    "tick":{
        "id":12121,//ID
        "ts":1506584998239,//
        "data":[
            {
                "id":12121,//trading id
                "side":"buy",//asks/bids direction
                "price":32.233,
                "vol":232,
                "amount":323,
                "ts":1506584998239,
                "ds":'2017-09-10 23:12:21'
            },
            {
                "id":12120,//trading id
                "side":"buy",//asks/bids direction
                "price":32.233,
                "vol":232,//volume
                "amount":323,//amount
                "ts":1506584998239,
                "ds":'2017-09-10 23:12:21'
            }
        ]
    }
}
```

### Update Data Fields
| Field | Data Type| Description |
|-----|-----|-----|
| id  | long | trading id |
| side | string  | asks/buys direction <br>asks: asks</br> <br>buys</br>|
| price | float  | strike price|
| vol | float  | volume |
| amount | float  | amount |
| ts | long  | trading time |
| ds | string  | date |




## <span id="24">Request K-line Historical Data</span>


### channel
`maket_$symbol_kline_$period`

* Add request parameters endIdx and pageSize (maximum 300, default 300 entries). If endIdx is null, the latest 300 entries of historical data will be returned.


> Subscribe

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

### Subscription Parameters

| Parameter | Data Type  | Mandatory or Not  | Description | Value Range |
| ---- | ------ | ---------- | ---- | ----- |
| channel | string | Yes  | Topic |$symbol: symbol name<br>btcusdt,ethusdt ... </br>   <br> $period: K-line period</br> <br>1min,5min,15min,30min,60min,1day,1week,1month</br>|
| cb_id | string | Yes  | Customized id     | |
| since | long | No | Customized id      | When since defaults, the latest 300 entries will be returned, and when since is valued, it returns up to one-hour data that is larger than since. As since has strong verification, it cannot be valued at 59 one hour earlier.|

> Response

```json
{
    "event_rep":"rep","channel":"market_btcusdt_kline_5min",
    "cb_id":"10001",
    "since":"1506602880",
    "ts":1506584998239,//
    "data":[
        {
            "id":1506602880,
            "amount":123.1221,//amount
            "vol":1212.12211,//volume
            "open":2233.22,//opening price
            "close":1221.11,//closing price
            "high":22322.22,//highest price
            "low":2321.22//lowest price
        },
        {
            "id":1506602880,
            "amount":123.1221,//amount
            "vol":1212.12211,//volume
            "open":2233.22,//opening price
            "close":1221.11,//closing price
            "high":22322.22,//highest price
            "low":2321.22//lowest price
        }
    ]
}
```

### Update Data Fields
| Field | Data Type | Description |
|-----|-----|-----|
| id  | long | trading id |
| vol | float  | volume |
| amount | float  | amount |
| open | long  | opening price |
| close | long  | closing price |
| high | long  | highest price |
| low | long  | lowest price|



## <span id="25">Request Trading Historical Data </span>

### channel
`maket_$symbol_trade_ticker`


> Subscribe

```json
{
    "event":"req","params":
    {
        "channel":"market_$base$quote_trade_ticker",
        "cb_id":"Customized",
        "top":200
     }
}
```

### Subscription Parameters

| Parameter | Data Type  | Mandatory or Not  | Description | Value Range |
| ---- | ------ | ---------- | ---- | ----- |
| channel | string | Yes  | Topic |$symbol: symbol name<br>btcusdt,ethusdt ...</br>|
| cb_id | string | Yes  | Customized id     | |

> Response

```json
{
    "event_rep":"rep",
    "channel":"market_btcusdt_trade_ticker",
    "cb_id":"10001",
    "ts":1506584998239,
    "status":"ok",
    "top":200,
    "data":[
        {
            "id":12121,//trading id
            "side":"buy",//asks/bids direction
            "price":32.233,//strike price
            "vol":232,//volume
            "amount":323,//amount
            "ts":1506584998239//
        },
        {
            "id":12120,//trading id
            "side":"buy",//asks/bids direction
            "price":32.233,//strike price
            "vol":232,//volume
            "amount":323,//amount
            "ts":1506584998239,//
            "ds":'2017-09-10 23:12:21'
        }
    ]
}
```
### Update Data Fields
| Field | Data Type | Description |
|-----|-----|-----|
| id  | long | trading id |
| side | string  | asks/buys direction  <br>asks: asks</br> <br>buys</br>|
| price | float  | strike price |
| vol | float  | volume |
| amount | float  | amount |
| ts | long  | trading time |


## <span id="26">Request Home 24 Ticker Data</span>

### channel
`review`

> Subscribe

```json
{"event":"req","params":{"channel":"review"}}
```
### Subscription Parameters

| Parameter | Data Type  | Mandatory or Not  | Description | Value Range |
| ---- | ------ | ---------- | ---- | ----- |
| channel | string | Yes  | Topic | review|

> Response

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

### Update Data Fields
| Field | Data Type| Description|
|-----|-----|-----|
| amount  | long | amount |
| close  | float  | closing price|
| high | float  | highest price|
| low | float  | lowest price |
| open | float  | opening price |
| rose | float  | rose |
| vol | float  | volume|


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

