title: REST PUBLIC ENDPOINTS
---
You can modify site settings in `_config.yml` or in an [alternate config file](#Using-an-Alternate-Config).

### Ticker

The ticker is a high level overview of the state of the market. It shows you the current best bid and ask, as well as the last trade price. It also includes information such as daily volume and how much the price has moved over the last day.

URI
```
http://api.rightbtc.com/v1/pub/ticker/$market
```

RESP

Setting | Description
--- | ---
`title` | The title of your website
`subtitle` | The subtitle of your website
`description` | The description of your website
`author` | Your name
`language` | The language of your website. Use a [2-lettter ISO-639-1 code](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes). Default is `en`.
`timezone` | The timezone of your website. Hexo uses the setting on your computer by default. You can find the list of available timezones [here](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones). Some examples are `America/New_York`, `Japan`, and `UTC`.

*You need set $market with `mnem` of target market.*

### Markets
A list of trading marketplace.
{% note info market %}
trading marketplace
{% endnote %}

URI
```
http://api.rightbtc.com/v1/markets
```

Key | Type | Description 
--- | --- | ---
`mnem` | [string] | Global unique id(mnem) of this market |
`display` | [string] | Display name of this market |
`contr` | [string] | Trading contract id(mnem) |
`settlDate` | [integer] | Settlement date, if spot then null |
`expiryDate` | [integer] | Expiry date, if spot then null |
`state` | [integer] | Open-1, Maintaince-503 |
`takerFee` | [decimal] | taker fee like 0.002 |
`makerFee` | [decimal] | maker fee like 0.001 |


*You may want to know what exactly `contr` is.*

### Contracts

A list of contracts.
{% note info contract%}
define contract
{% endnote %}

URI
```
http://api.rightbtc.com/v1/contracts
```

Key | Type | Description 
--- | --- | ---
`mnem`| [string] | Global unique id(mnem) of this contract |
`display`| [string] | Display name of this contract |
`asset`| [string] | subject asset of this contract |
`ccy`| [string] | currency asset of this contract |
`lotNumer`| [integer] | lots number of this contract, if spot then null |
`lotDenom`| [integer] | lot denom, define asset decimals to lot |
`qtyInc`| [decimal] | real qunatity of this contract |
`tickNumer`| [integer] | tick number, if spot then null |
`tickDenom`| [integer] | tick denom, define ccy(currency) decimals to tick |
`priceInc`| [decimal] | real qunatity of this contract |
`pipDp`| [integer] | Number of decimal places in real |
`minLots`| [integer] | minimum lots to ask/bid |
`maxLots`| [integer] | maximum lots to ask/bid |
`state` | [integer] | Open-1, Maintaince-503 |

*You may want to know what exactly `asset` is.*

