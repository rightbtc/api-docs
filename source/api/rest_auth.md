title: REST Authenticated Endpoints
comments: false
---
You can access the following endpoints:
``` yaml
product-endpoint:
- https://api.rightbtc.com
test-api-endpoint:
- https://test-api.rightbtc.com
```
*`product-endpoint`* provides real access of the RightBTC platform. Otherwise, *`test-api-endpoint`* is a dummy environment of RightBTC, you can apply for [testing api/secret pairs](https://test-api.rightbtc.com/dummyuser/) in order to test with some BTC/ETP/USD/DNA balance.
```json 
{
  "display":"KrAZLSCA", // your DUMMY SECRET Key(it won't be shown on product env, NEVER)
  "email":"wKkSBnXu", // your DUMMY trader api key
  "mnem":"wKkSBnXu"
} 
```

**All Authenticated Endpoints use POST requests.**
*All examples assume the `test-api-endpoint`*.

See [How to setup HTTP Headers](index.html#Authenticated-Endpoints)

### Trader Info
{% note info Trader Info %}
A Trader belongs to a account. Basic info of this trader.
{% endnote %}

#### /v1/trader/info

Field | Type | Description
--- | --- | ---
`null` | [null] | null |
No fields required.

```bash
$ curl --location --request POST "http://test-api.rightbtc.com/v1/trader/info" \
  --header "APIKEY: apiKey" \
  --header "SIGNATURE: signature" \
  --header "NONCE: 1561346769" \
  --header "Content-Type: application/json"
```
```json
{
    "mnem": "trader_id",
    "display": "Hao Chen Display",
    "email": "trader_id or user_id",
    "makerFee": 0.002,
    "takerFee": 0.002,
    "orderLimitSec": 10,
    "requestLimitSec": 20,
    "canLending": 0,
    "marginable": 1,
    "maxLeverage": 3,
    "marginState": 1,
    "specializedFee": []
}
```

Key | Type | Description 
--- | --- | ---
`mnem` | [string] | global unique trader id |
`display` | [string] | describtion of this trader |
`email` | [string] | api key id |
`takerFee` | [decimal] | taking order fee when your order is matched as role TAKER, priority over global market settings. |
`makerFee` | [decimal] | fee when your order is matched as role MAKER, priority over global market settings. |
`orderLimitSec` | [integer] | ORDERS(create/cancel/cancel all/multi cancel) rate limits, defaults to 10 commits per second |
`requestLimitSec` | [integer] | FUNDING(transfer/loan/repay/margin info) rate limits, defaults to 20 commits per second |
`canLending` | [integer] | peer to peer lending permission flag (debit), 0 to false, 1 to true |
`marginable` | [integer] | margin trading permission flag, defaults to 0 means false, 1 to true when you signed the risk agreement from webpages. |
`maxLeverage` | [double] | max leverage for your postion for margin trading, defualts to 3 |
`marginState` | [integer] | forced liquidation to 0, defualts to 1 |
`specializedFee` | [array] | specialized fee for markets, defaults to null array, top priority over all of fee settings. |

### Trader Balances
{% note info Trader Balances %}
An array of asset balances.
**There are 3 wallet types "EXCHANGE" "MARGIN" "FUNDING" for your balances.**
{% endnote %}

#### /v1/trader/balances

Field | Type | Required | Description
--- | --- | ---
`asset` | [string] | No | specify an [asset](rest_pub.html#Assets) `mnem`, if not sent then returns a list of trader's balances of "EXCHANGE" "MARGIN" "FUNDING". |

```bash
$ curl --location --request POST "http://test-api.rightbtc.com/v1/trader/balances" \
  --header "APIKEY: apiKey" \
  --header "SIGNATURE: signature" \
  --header "NONCE: 1561346769" \
  --header "Content-Type: application/json"
```
```json
{
    "EXCHANGE": [
        {
            "asset": "BTC",
            "balance": 500000000,
            "available": 500000000,
            "denom": 100000000
        },
        {
            "asset": "ETP",
            "balance": 7000000000000000,
            "available": 7000000000000000,
            "denom": 100000000
        },
        {
            "asset": "USDT",
            "balance": 600000000000000,
            "available": 600000000000000,
            "denom": 100000000
        }
    ],
    "MARGIN": [],
    "FUNDING": []
}
```
`balance:500000000` and `denom = 100000000` is equal to `5 BTC = 500000000/100000000`

{% blockquote %}
if sent field `asset` then returns JSON as following.
{% endblockquote %}
```
{
    "EXCHANGE": {
        "asset": "BTC",
        "balance": 50000000000,
        "available": 49999965200,
        "denom": 100000000
    },
    "MARGIN": {
        "asset": "BTC",
        "balance": 150010000000,
        "available": 150010000000,
        "denom": 100000000
    },
    "FUNDING": {
        "asset": "BTC",
        "loan": 10000000,
        "contributes": 12500,
        "lend": 0,
        "offer": 0,
        "commissions": 0,
        "balance": -10000000,
        "available": -10000000
    }
}
```
```json
{
    "EXCHANGE": {
        "asset": "BTC",
        "balance": 50000000000,
        "available": 50000000000,
        "denom": 100000000
    },
    "MARGIN": null,
    "FUNDING": null
}
```

EXCHANGE and MARGIN fields

Key | Type | Description 
--- | --- | ---
`asset` | [string] | `mnem` of asset |
`balance` | [integer] | balance |
`available` | [interger] | available amount to trade |
`denom` | [interger] | defaults to 100000000 = 8 [decimal places](rest_pub.html#Assets) once asset listed never be changed.|

FUNDING fields, `denom` shares EXCHANGE's

Key | Type | Description 
--- | --- | ---
`asset` | [string] | `mnem` of asset |
`loan` | [integer] | loaned amount pending on repay |
`cotributes` | [interger] | interest amount pending on repay |
`lend` | [interger] | lended amount pending on |
`offer` | [interger] | net-transfering-in from other wallet types, available |
`commissions` | [interger] | accumulative total commissions from lending |
`balance` | [integer] | balance = offer - loan |
`available` | [interger] | available amount to debit = balance - lend |

#### /v1/trader/balance

Field | Type | Required | Description
--- | --- | ---
`asset` | [string] | No | specify an [asset](rest_pub.html#Assets) `mnem`, if not sent then returns a list of trader's "EXCHANGE" balances. |
`from` | [string] | No | specify an wallet type of "EXCHANGE" "MARGIN" "FUNDING", if not sent then returns "EXCHANGE" balance by default. |


```bash
$ curl --location --request POST "http://test-api.rightbtc.com/v1/trader/balance" \
  --header "APIKEY: apiKey" \
  --header "SIGNATURE: signature" \
  --header "NONCE: 1561346769" \
  --header "Content-Type: application/json"
  --data "{\"asset\":\"BTC\",\"from\":\"EXCHANGE\"}"
```
```json
{
    "asset": "BTC",
    "balance": 500000000,
    "available": 400000000,
    "denom": 100000000,
}
```

{% blockquote %}
if NOT sent field `asset` then returns JSON array as following.
**Always list those assets which had incoming (bought/deposits).**
{% endblockquote %}
```json
[
    {
        "asset": "BTC",
        "balance": 500000000,
        "available": 400000000,
        "denom": 100000000,
    },
    {
        "asset": "ETP",
        "balance": 20000000000,
        "available": 19800000000,
        "denom": 100000000,
    }
]
```

{% blockquote %}
if sent field `from` is one of "MARGIN" "FUNDING" which has no incoming(funding/margin/transfer).
it will returns:
{% endblockquote %}
```json
[]
```

{% blockquote %}
if sent field `asset` under field `from` is one of "MARGIN" "FUNDING" which has no incoming(funding/margin/transfer).
it will returns:
{% endblockquote %}
```json
null
```

### Create New Order
{% note info Order/New %}
Palace a new order to a market.
EXCHANGE trading supports `LIMIT,MARKET,STOP,FOK,IOC,TRAILINGSTOP` and Hidden orders(non-display in orderbooks through public api calls).
MARGIN trading supports `MarginLIMIT,MarginMARKET,MarginSTOP,MarginFOK,MarginIOC,MarginTRAILINGSTOP` and Hidden orders(non-display in orderbooks through public api calls).
{% endnote %}

#### /v1/order/new

Field | Type | Required | Description
--- | --- | ---
`market` | [string] | Yes | `mnem` of market from [markets](rest_pub.html#Markets) |
`side` | [string] | Yes| order side "BUY" or "SELL" |
`lots` | [lots] | Yes | order lots, precision is defined by [contract](rest_pub.html#Contracts) |
`ticks` | [ticks] | Yes | order ticks, precision is defined by [contract](rest_pub.html#Contracts) |
`type` | [integer] | No | order types defaults to 0, 0-LIMIT;1-MARKET;2-STOP;3-TRAILINGSTOP;4-FOK;5-IOC;    10-MarginLIMIT;11-MarginMARKET;12-MarginSTOP;13-MarginTRAILINGSTOP;14-MarginFOK;15-MarginIOC |
`factor` | [integer] | No | order attribute, 2-HIDDEN , defaults to null (non-Hidden) |

```bash
$ curl --location --request POST "http://test-api.rightbtc.com/v1/order/new" \
  --header "APIKEY: apiKey" \
  --header "SIGNATURE: signature" \
  --header "NONCE: 1561346769" \
  --header "Content-Type: application/json"
```
```json
{
    "id": 100000011,
    "market": "BTCUSD",
    "state": "ACCEPTED"
}
```
Key | Type | Description 
--- | --- | ---
`id` | [integer] | `id` of order |
`market` | [string] | `mnem` of market |
`state` | [string] | Always "ACCEPTED" if POST ok, asynchronous processing |


{% blockquote %}
Order is unique identitfied by `market` and `id`(primary key), not single `id`.
{% endblockquote %}

### Cancel Active Order
{% note info Order/Cancel %} 
Order/Cancel to cancel one single order
Order/Cancel/Multi to cancel multiable orders
Order/Cancel/All to cancel all orders
{% endnote %}

#### /v1/order/cancel

Field | Type | Required | Description
--- | --- | ---
`id` | [integer] | Yes | `id` of order |
`market` | [string] | Yes | `mnem` of market from [markets](rest_pub.html#Markets) |

```bash
$ curl --location --request POST "http://test-api.rightbtc.com/v1/order/cancel" \
  --header "APIKEY: apiKey" \
  --header "SIGNATURE: signature" \
  --header "NONCE: 1561346769" \
  --header "Content-Type: application/json"
```
```json
{
    "id": 100000011,
    "market": "BTCUSD",
    "state": "ACCEPTED"
}
```
Key | Type | Description 
--- | --- | ---
`id` | [integer] | `id` of order |
`market` | [string] | `mnem` of market |
`state` | [string] | Always "ACCEPTED" if POST ok, asynchronous processing |

#### /v1/order/cancel/multi

Field | Type | Required | Description
--- | --- | ---
`ids` | [array of integer] | Yes | array of `id` of each order |
`market` | [string] | Yes | `mnem` of market from [markets](rest_pub.html#Markets) |

```bash
$ curl --location --request POST "http://test-api.rightbtc.com/v1/order/cancel/multi" \
  --header "APIKEY: apiKey" \
  --header "SIGNATURE: signature" \
  --header "NONCE: 1561346769" \
  --header "Content-Type: application/json"
```
```json
{
    "idsOnError": [100000011,100000012]
    "market": "BTCUSD",
    "state": "ACCEPTED"
}
```
Key | Type | Description 
--- | --- | ---
`idsOnError` | [array of integer] | `id` of order which has exception on cancel|
`market` | [string] | `mnem` of market |
`state` | [string] | Always "ACCEPTED" if POST ok, asynchronous processing |

#### /v1/order/cancel/all
This API call will cancel all avtive orders under a market.

Field | Type | Required | Description
--- | --- | ---
`market` | [string] | Yes | `mnem` of market from [markets](rest_pub.html#Markets) |

```json
{
    "market": "BTCUSD",
    "state": "ACCEPTED"
}
```

### Query Active Orders
{% note info Order/Status %}
Order State must be in [`NEW`,`TRADE`,`CANCEL`] and [`PENDING`,`ACCEPTED`]
* NEW - order is newly placed, not filled yet.
* TRADE - order is `full_filled` or `particularly_filled` (you can check `resd` for particularly or full filled).
* CANCEL - order is canceled by trader or system (STOP/FOK/IOC).
* PENDING - a temporary state between acception and execution, extremely short.
* ACCEPTED - a temporary state, extremely short.

** This API provides ACTIVE order query ONLY. **
If order is done(CANCEL/full_filled), please use [History/Order](#Query-History-Order), otherwise order not found(404).
{% endnote %}

#### /v1/order/status

Field | Type | Required | Description
--- | --- | ---
`id` | [integer] | No | `id` of order, query a active order under a market. |
`market` | [string] | No |  if specify `mnem` of market, then return orders under this market. if not specified, return all active orders. |
`page` | [integer] | No | page index, defaults to page 1, each page lists max 100 orders. |
`factor` | [integer] | No | 14 to EXCHANGE orders, 128 to MARGIN orders, defaults to null returns ALL of active orders |

```bash
$ curl --location --request POST "http://test-api.rightbtc.com/v1/order/status" \
  --header "APIKEY: apiKey" \
  --header "SIGNATURE: signature" \
  --header "NONCE: 1561346769" \
  --header "Content-Type: application/json"
```
```json
{
    "orders": [
        {
            "trader": "trader_id",
            "market": "BTCUSD",
            "contr": "BTCUSD",
            "id": 100000015,
            "ref": null,
            "type": "LIMIT",
            "state": "TRADE",
            "side": "BUY",
            "lots": 1000000,
            "ticks": 1100,
            "resd": 800000,
            "exec": 200000,
            "cost": 220000000,
            "lastLots": 200000,
            "lastTicks": 1100,
            "created": 1561022000931,
            "modified": 1561022008615
        },
        {
            "trader": "trader_id",
            "market": "BTCUSD",
            "contr": "BTCUSD",
            "id": 100000018,
            "ref": null,
            "type": "MarginLIMIT",
            "state": "NEW",
            "side": "SELL",
            "lots": 200000,
            "ticks": 5000,
            "resd": 200000,
            "exec": 0,
            "cost": 0,
            "hidden": true,
            "lastLots": null,
            "lastTicks": null,
            "created": 1561022064035,
            "modified": 1561022064035
        }
    ],
    "page": 1,
    "total_pages": 1,
    "total_count": 2
}
```
Key | Type | Description 
--- | --- | ---
`trader` | [string] | `mnem` of trader |
`market` | [string] | `mnem` of market |
`contr` | [string] | `mnem` of contract of market |
`id` | [integer] | `id` of order |
`ref` | [string] | client id , defaults to null |
`type` | [string] | enum of "LIMIT" "MARKET" "STOP" "TRAILINGSTOP" "FOK" "IOC" for EXCHANGE orders, "MarginLIMIT" "MarginMARKET" "MarginSTOP" "MarginTRAILINGSTOP" "MarginFOK" "MarginIOC" for MARGIN orders |
`state` | [string] | enum of "NEW" "TRADE" "CANCEL" "PENDING" |
`side`: | [string] | enum of "BUY" "SELL" |
`lots`: | [lots] | order lots filled by trader when created |
`ticks`: | [ticks] | order ticks filled by trader when created |
`resd`: | [lots] | residues of order lots, `if resd != 0` **and** state `TRADE` means `particularly_filled` |
`exec`: | [lots] | execution of order lots, `if exec == lots` **and** state `TRADE` means `full_filled` |
`cost`: | [integer] | cost = Σ(lastLots * lastTicks) |
`lastLots` | [lots] | last taken lots |
`lastTicks` | [ticks] | last taken ticks |
`created` | [time] | timestamp of created in milliseconds |
`modified` | [time] | timestamp of modified(taken/cancel) in milliseconds |

{% blockquote %}
Due to I/O limits of network, system returns max 100 orders at once, all orders paged by each 100 orders.
You can set field `page` number if your active orders are over 100.
{% endblockquote %}

### Query History Order
{% note info %}
** This API provides HISTORY(CANCEL/full_filled) order query ONLY. **
If active order, please use [Order/Status](#Query-Actived-Orders), otherwise order not found(404).
{% endnote %}

#### /api/trader/v1/order/history
List your history orders of a market.

| Field       | Type      | Description |
| ----------- | --------- | ----------- |
| `market`    | [string]  | `mnem` of market  |
| `pageIndex` | [integer] | page index  |
| `pageSize`  | [integer] | total pages |

```shell
curl --location --request POST "/api/trader/v1/order/history" \
  --header "apikey: apikey" \
  --header "signature: signature" \
  --header "nonce: xxxxxx" \
  --header "Content-Type: application/json" \
  --data "{
    \"market\": \"BTCETP\",
    \"pageIndex\": 0, 
    \"pageSize\": 20
}"
```

#### /api/trader/v1/order/
Get **a** history order by market and id.

| Field    | Type      | Description |
| -------- | --------- | ----------- |
| `market` | [string]  | `mnem` of market |
| `id`     | [integer] | `id` of order    |

```shell
curl --location --request POST "/api/trader/v1/order/" \
  --header "apikey: apikey" \
  --header "signature: signature" \
  --header "nonce: nonce" \
  --header "Content-Type: application/json" \
  --data "{
	\"market\": \"BTCETP\",
	\"id\": 111111
}"
```

### Query History Trade
{% note info History/Trade %}
Only provides query method by order, not listing.
{% endnote %}

#### /v1/history/trade
to be written...

