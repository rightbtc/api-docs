title: REST Public Endpoints
comments: false
---
You can access the following endpoints:
``` yaml
production-endpoint:
- https://api.rightbtc.com
test-api-endpoint:
- https://test-api.rightbtc.com
```
**All Authenticated Endpoints use GET requests.**
*All examples assume the `test-api-endpoint`*.

### Markets
{% note info Market %}
Trading marketplace that trader can palce order, identified by `mnem`.
{% endnote %}

#### /v1/markets
A list of trading marketplace.
```bash
$ curl --location --request GET https://test-api.rightbtc.com/v1/markets
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
        "mnem": "ETPUSD",
        "display": "display",
        "contr": "ETPUSD",
        "settlDate": null,
        "expiryDate": null,
        "state": 1,
        "makerFee": 0,
        "takerFee": 0
    }
]
```

Key | Type | Description 
--- | --- | ---
`mnem` | [string] | global unique mnem(id) of this market |
`display` | [string] | display name of this market |
`contr` | [string] | trading contract mnem(id) |
`settlDate` | [integer] | settlement date, if spot then null |
`expiryDate` | [integer] | Expiry date, if spot then null |
`state` | [integer] | Open-1, Maintaince-503 |
`takerFee` | [decimal] | taker fee expressed like 0.002 |
`makerFee` | [decimal] | maker fee expressed like 0.001 |


*You may want to know what exactly `contr` is.*

### Contracts
{% note info Contracts %}
Contracts defines what your are trading, it's type is one of SPOT or OPTION(not release yet).
if SPOT, use `lotDenom` and `tickDenom` to define precisions.
if OPTION, use entire fields to define an OPTION contract.
A contract can be palaced at a market, they are using same `mnem`.
{% endnote %}

#### /v1/contracts
A list of contracts.
```bash
$ curl --location --request GET https://test-api.rightbtc.com/v1/contracts
```
```json
[
    {
        "mnem": "BTCUSD",
        "display": "BTCUSD-contract-display-pytester",
        "asset": "BTC",
        "ccy": "USD",
        "lotNumer": 1,
        "lotDenom": 1000000,
        "qtyInc": 0.000001,
        "tickNumer": 1,
        "tickDenom": 100,
        "priceInc": 0.01,
        "pipDp": 0,
        "minLots": 1,
        "maxLots": 10000000,
        "state": 1
    }
]
```

Key | Type | Description 
--- | --- | ---
`mnem`| [string] | global unique mnem(id) of this contract |
`display`| [string] | display name of this contract |
`asset`| [string] | subject asset of this contract, `mnem` of [asset](#Assets) |
`ccy`| [string] | currency asset of this contract, `mnem` of [asset](Assets) |
`lotNumer`| [integer] | lot number of this contract, if null then spot |
`lotDenom`| [integer] | lot denominator, define asset decimals to lot |
`qtyInc`| [decimal] | real quantity of this contract |
`tickNumer`| [integer] | tick number, if null then spot |
`tickDenom`| [integer] | tick denominator, define ccy(currency) decimals to tick |
`priceInc`| [decimal] | real quantity of this contract |
`pipDp`| [integer] | number of decimal places in real |
`minLots`| [integer] | minimum lots for ask/bid |
`maxLots`| [integer] | maximum lots for ask/bid |
`state` | [integer] | Open-1, Maintaince-503 |

#### Lots
Let's say BTC floatingAmount is `0.1 BTC`, we give lots `1000000` due to `lotDenom:1000000`
```
lots = integer(floatingAmount * lotDenom)

eg: 100000 = integer(0.1 * 1000000)
```

#### Ticks
Let's say BTC floatingPrice is `$9000.00`, we give ticks `900000` due to `tickDenom:100`
```
ticks = integer(floatingPrice * tickDenom)

eg: 900000 = integer(9000 * 100)
```

### Assets
{% note info Asset %}
Asset is one of [StableCoin, UtiltyToken, SecurtiyToken, Fiat, Index, NonStandard].
`mnem` is refers to contracts.
`denom` defines precisions of this asset, is defaults to `100000000` (8 decimal palaces).
{% endnote %}

#### /v1/assets
A list of assets.
```bash
$ curl --location --request GET https://test-api.rightbtc.com/v1/assets
```
```json
[
    {
        "mnem": "BTC",
        "display": "BTC-asset-display-pytester",
        "type": "Coin",
        "denom": 100000000,
        "state": 1
    }
]
```

Key | Type | Description 
--- | --- | ---
`mnem`| [string] | global unique mnem(id) of this asset |
`display`| [string] | display name of this asset |
`type`| [string] | type of this asset |
`denom`| [integer] | denominator defaults to 100000000 stands for 8 decimal places |
`state` | [integer] | Open-1, Maintaince-503 |

{% note info Static Configurations %}
*Markets/Contracts/Assets* are static configurations of RightBTC, it defines the precision and states of each item, so you may need to load these information once your program starts.
{% endnote %}

### Ticker

{% note info Ticker %}
The ticker is a high level overview of the state of the market. It shows you the current best bid and ask, as well as the last trade price. It also includes information such as daily volume and how much the price has moved over the last day.
{% endnote %}

#### /v1/pub/ticker/$market_mnem
```bash
$ curl --location --request GET https://test-api.rightbtc.com/v1/pub/ticker/ETPBTC
```
```json
{
    "lots": 1000000,
    "ticks": 1100,
    "ask": 1200,
    "bid": 1000,
    "created": 1561346769801,
    "last24": {
        "high": 1100,
        "low": 1100,
        "tail": null,
        "vol": 1000000
    }
}
```
Key | Type | Description 
--- | --- | ---
`lots` | [lots] | lots of ticker |
`ticks` | [ticks] | ticks of ticker |
`ask` | [ticks] | ticks of best ask |
`bid` | [ticks] | ticks of best bid |
`created` | [time] | timestamp of ticker in milliseconds |
`high` | [ticks] | high of last 24 hours in ticks |
`low` | [ticks] | low of last 24 hours in ticks |
`tail` | [ticks] | the ticks of last 24 hours in ticks |
`vol` | [lots] | vol of last 24 hours in lots |

* See [market_mnem](#v1-markets)
* See [lots](#lots)
* See [ticks](#ticks)

#### /v1/pub/ticker
*curl request example*
```bash
$ curl --location --request GET https://test-api.rightbtc.com/v1/pub/ticker
```
An array of ticker, may empty as `[]`.
```json
[
    {
        "market": "BTCUSD",
        "lots": 1000000,
        "ticks": 1100,
        "created": 1561346769801,
    	"ask": 1200,
    	"bid": 1000,
        "last24": {
            "high": 1100,
            "low": 1100,
            "tail": null,
            "vol": 1000000
        }
    },
    {
        "market": "ETPUSD",
        "lots": null,
        "ticks": null,
        "created": null,
    	"ask": null,
    	"bid": null,
        "last24": {
            "high": null,
            "low": null,
            "tail": null,
            "vol": 0
        }
    }
]
```
### Trades

{% note info Trades %}
Latest trades of market.
{% endnote %}

#### /v1/pub/trades/$market_mnem
```bash
$ curl --location --request GET https://test-api.rightbtc.com/v1/pub/trades/ETPBTC
```
A list of last trades of target market.
```json
[
    {
        "lots": 1000000,
        "ticks": 1100,
        "side": "BUY",
        "created": 1561346769801
    }
]
```
Key | Type | Description 
--- | --- | ---
`lots` | [lots] | lots of each trade |
`ticks` | [ticks] | ticks of each trade |
`created` | [time] | timestamp of trade in milliseconds |
`side` | [string] | "BUY" or "SELL" |

* See [market_mnem](#v1-markets)


### Depth
{% note info Depth %}
order book of market.
Display 50 levels.
{% endnote %}

#### /v1/pub/depth/$market_mnem
```bash
$ curl --location --request GET https://test-api.rightbtc.com/v1/pub/depth/ETPBTC
```
```json
{
    "asks": [
        {
            "ticks": 1200,
            "lots": 1000000,
            "count": 1
        }
    ],
    "bids": [
        {
            "ticks": 1100,
            "lots": 1000000,
            "count": 1
        }
    ]
}
```
Key | Type | Description 
--- | --- | ---
`ticks` | [ticks] | level at this tick |
`lots` | [ticks] | total lots at this tick |
`count` | [ticks] | count of total orders at this tick |

#### /v1/pub/fulldepth/$market_mnem
Get full orderbook.
```bash
$ curl --location --request GET https://test-api.rightbtc.com/v1/pub/fulldepth/ETPBTC
```

### View
{% note info View %}
Overview of a market, get **Trades/Ticker/Depth** by 1 API call once rather than those 3 API calls.
{% endnote %}

#### /v1/pub/view/$market_mnem
```bash
$ curl --location --request GET https://test-api.rightbtc.com/v1/pub/view/ETPBTC
```

### Candlestick

{% note info Candlestick %}
{% endnote %}

#### /v1/pub/candlestick/$market_mnem
```bash
$ curl --location --request GET https://test-api.rightbtc.com/v1/pub/candlestick/ETPBTC
```
```json
[
    {
        "60": [
            {
                "o": 1100,
                "h": 1100,
                "l": 1100,
                "c": 1100,
                "v": 0,
                "m": 1561360920
            }
	]
    },
    {
        "300": [
            {
                "o": 1100,
                "h": 1100,
                "l": 1100,
                "c": 1100,
                "v": 0,
                "m": 1561360920
            }
	]
    },
    ...
]

```
Key | Type | Description 
--- | --- | ---
`60` | [string] | candlestick cycle is 60 seconds = 1 minute|
`300` | [string] | candlestick cycle is 300 seconds = 5 minutes|
`900` | [string] | candlestick cycle is 900 seconds = 15 minutes|
`1800` | [string] | candlestick cycle is 1800 seconds = 30 minutes|
`3600` | [string] | candlestick cycle is 3600 seconds = 1 hour|
`7200` | [string] | candlestick cycle is 7200 seconds = 2 hours|
`14400` | [string] | candlestick cycle is 14400 seconds = 4 hours|
`21600` | [string] | candlestick cycle is 21600 seconds = 6 hours|
`43200` | [string] | candlestick cycle is 43200 seconds = 12 hours|
`86400` | [string] | candlestick cycle is 86400 seconds = 1 day|
`604800` | [string] | candlestick cycle is 604800 seconds = 1 week |
`o` | [ticks] | open ticks |
`h` | [ticks] | high ticks |
`l` | [ticks] | low ticks |
`c` | [ticks] | close ticks |
`v` | [lots] | vol lots |
`m` | [time] | modified timestamp in seconds |

#### /v1/pub/candlestick/$market_mnem:$cycle
cycle should be in `60,300,900,1800,3600,7200,14400,21600,43200,86400,604800`.

```bash
$ curl --location --request GET https://test-api.rightbtc.com/v1/pub/candlestick/ETPBTC:300
```
```json
[
    {
       "o": 1100,
       "h": 1100,
       "l": 1100,
       "c": 1100,
       "v": 0,
       "m": 1561360920
    },
    {
       "o": 1100,
       "h": 1100,
       "l": 1100,
       "c": 1100,
       "v": 0,
       "m": 1561360920
    },
    ...
]

```

