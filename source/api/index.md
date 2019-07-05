title: INTRODUCTION
---
Welcome to the RightBTC API documentation.
The RightBTC API's are designed to allow access to all of the features of the RightBTC platform, subject to you complying with the API Terms of Service.

## API access

In order to access the parts of the API which require authentication, you must generate an API key and an API secret key using the web page of your account info.

You can generate as many API keys as you would like (**Entity Account Required**), and each of those keys can be customized in a few ways.

|  PERMISSIONS | Read | Write |
|  ------- | -------| ------- |
|  ORDERS   |  √  | √ | 
|  ORDER_HISTORY   |  √  | √ | 
|  FEE |  √  | × | 
|  DEPOSITS |  ×  | × | 
|  WITHDRAWALS |  ×  | × | 

## Notes

* Do not parse text descriptions, use only codes
* All times are UTC timestamps expressed as micro-seconds (eg. 1571234567000), but candlestick API calls use seconds (eg. 1571234567).

{% youtube ARted4RniaU %}

## Precision

{% note info Lots/Ticks %}
Due to the precision of the floating-point issue in computer not being 100% real,
we therefore use lots/ticks to replace price/amount.
The precision level of all trading is calculated based on significant integers (lots/ticks).
{% endnote %}

Let's see the case of:

Example: Bitcoin's price is $9000.00, then I want to BUY 0.1 BTC on RightBTC.

|  PAIR | In float price/amount | In integer lots/ticks |
|  ------- | -------| ------- |
|  BTCUSD |  9000.00/0.1  | 900000/100000 |

`price 9000.00` => `ticks 900000` ? because RightBTC defines `1 tick` = `0.01 USD`
`amount 0.1` => `lots 100000` ? because RightBTC defines `1 lot` = `0.000001 BTC`

Now we also know what is the minimum amount and price tick of any pair.

See API [Contracts](rest_pub.html#Contracts) to get each definition of the trading pairs.

## REST APIs

### General
```
https://api.rightbtc.com/v1
```
Requests parameters for POST requests (authenticated) in the "Authenticated Endpoints" section, not GET parameters.

Requests parameters for GET requests (non-authenticated) are based on URI.

In order to offer the best service possible, we have added a rate limit to the number of REST requests.

|  METHOD | per second | per minute | per day |
|  ------- | -------| ------- | ------- |
|  REQUESTS | - | 1200 | - |
|  ORDERS |  10 | - | 100000 |


### Public Endpoints

All Public Endpoints use GET requests.

Let's try `GET /v1/markets`:
``` bash
$ curl --location --request GET "http://api.rightbtc.com/v1/markets"
```
```json
[
    {
        "mnem": "BTCUSD",
        "display": "display",
        "contr": "BTCUSD",
        "settlDate": null,
        "expiryDate": null,
        "state": 1,
        "makerFee": 0,
        "takerFee": 0
    },
    {
        "mnem": "ETPBTC",
        "display": "display",
        "contr": "ETPBTC",
        "settlDate": null,
        "expiryDate": null,
        "state": 1,
        "makerFee": 0,
        "takerFee": 0
    }
]
```

* See [GET Markets](rest_pub.html#Markets)
* See [GET Contracts](rest_pub.html#Contracts)
* See [GET Assets](rest_pub.html#Assets)
* See [GET Depth](rest_pub.html#Depth)
* See [GET FullDepth](rest_pub.html#FullDepth)
* See [GET Ticker](rest_pub.html#Ticker)
* See [GET Trades](rest_pub.html#Trades)
* See [GET View](rest_pub.html#View)
* See [GET CandleStick](rest_pub.html#Candlestick)

### Authenticated Endpoints

All Authenticated Endpoints use POST requests.
Authentication is done using an API key and a secret key.
To generate this pair, please go to the API Access web page.

The authentication procedure is as follows:

1. Get your `apiKey` and `secretKey` pair from the web page.
IMAGE1

2. Prepare HTTP Header with
```
* APIKEY: apiKey
* NONCE: nonce - a strictly increasing number (eg:timestamp)
* SIGNATURE: MD5(body + secretKey + nonce)
```
The `body` is the content of POST request which is **JSON encoded**.

```cpp
string apiKey = config.getApiKey();
string secretKey = config.getSecretKey();
string nonce = getTimeOfDay();

httpHeader.insert("APIKEY", apiKey);
httpHeader.insert("NONCE", nonce);

string jsonEncodedBody = "{\"a\":1,\"b\":2}";
string payload = jsonEncodedBody + secretKey + nonce;
string signature = crypto::md5(payload);

httpHeader.insert("SIGNATURE", signature);
```

Specifically, Content Type supports `application/json` and `application/x-www-form-urlencoded`
Add below in Header,
```
* content-type:application/json
```
*Parsing JSON on RightBTC is faster than x-www-form-urlencoded*

* See [Trader Info](rest_auth.html#Trader-Info)
* See [Trader Balance](rest_auth.html#Trader-Balance)
* See [Order New](rest_auth.html#Create-New-Order)
* See [Order Cancel](rest_auth.html#Cancel-Active-Order)
* See [Order Status](rest_auth.html#Query-Active-Orders)

