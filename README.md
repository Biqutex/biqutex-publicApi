# Biqutex Public API Endpoints

------

Версия 1.0 from 05.02.2025 



## API access

#### **Testnet**: https://api.testnet.biqutex.dev/ 

  **Note**: API may be temporarily unavailable due to upgrade period

  **Note**: HTTP 1.1 and HTTP 2.0 protocols are supported, TLS 1.2+ only, IPv4 only

  **Rate limits**: 2 requests per second per IP are allowed

  **Content-type**: application/json; charset=utf8

#### **Production**: https://api.biqutex.com

​	**Note**: Use a Cloudflare for api domain access

​	**Note**: HTTP 1.1, 2.0 and HTTP 3.0/QUICK protocols are supported, TLS 1.2+, IPv4/v6 support

​	**Rate limits**: 3 req. per second per IP are allowed

​	**Content-type**: application/json; charset=utf8







## /api/v1/heartbeat

Check Biqutex availability server-side

#### **Params**

​	none

#### **Response**

```javascript
{
	status: "OK"
}
```

or

```javascript
{
	status: "FAILURE"
}
```

#### Example

- https://api.testnet.biqutex.dev/api/v1/heartbeat



## /api/v1/time

Get a Biqutex server time (for sync etc.)

#### **Params**

​	none

#### **Response**

```javascript
{
	status: "OK",
	data: {
          datetime: "2025-02-06T11:16:18.180Z",	//Datetime in human format
          ts: 1738840578180,    //Timestamp (UTC) in milliseconds
          tx: 1738840578180399    //Timestamp (UTC) with micro-seconds accuracy
	}
}
```

#### **Example** 

- https://api.testnet.biqutex.dev/api/v1/time 	



## /api/v1/contracts

Overview of market and traded contracts information

#### **Params**

- **symbol** - a symbol name or list of symbols separated by commas, e.g: *BTC/USD-PERP* or *BTC/USD-PERP,ETH/USD-PERP*
- **base** - base asset symbol, for example: BTC *or* *ETH*
- **quote** - a quote currency symbol, for example *USD*. Currently, only USD (default) is supported.
- **type** - a contract type. Currently only "*futures_perpetual*" is supported.

Request without any parameters will return ALL traded data OR use any combination of them.

#### Response

```javascript
{
	status: "OK",
	data: {
        BTC/USD-PERP: {
            ticker_id: "BTC/USD-PERP",    //Instrument symbol
            base_currency: "BTC",    //Base asset
            quote_currency: "USD",    //Quote asset
            contract_size: 0.0001,    //Contract size (in base asset)
            last_price: 96474.2,    //Last price (in quote)
            base_volume: 0.001,    //Volume in base asset
            quote_volume: 3857,    //Volume in quote asset
            USD_volume: 3857,    //USD-based volume
            bid: 97754.4,    //Best bid
            ask: 97754.5,    //Best ask
            high: 98999.95,    //24h higest price
            low: 95631.85,    //24h lowest price
            product_type: "futures_perpetual",    //Product type
            open_interest: 32.371,    //Open Interest in underlying asset
            open_interest_usd: 3166157,    //Open Interest in the quoted asset
            index_price: 97808.44,		//Last markPrice
            index_name: ".BTC/USD",		//Index price symbol code
            index_currency: "USD",    //Index price currency
            creation_timestamp: 1725138000000,    //Start date of derivative
            expiry_timestamp: 0,    //Expiration date 
            funding_rate: 0.003448,    //Current funding rate (in %)
            next_funding_rate: 0.003423,    //Predicted funding rate
            next_funding_rate_timestamp: 1738944000000,	//Next funding rate timestamp
            maker_fee: 0.02,    //A base commission fee (as maker), in %
            taker_fee: 0.055,    //A base commission fee (as taker), in %
            status: "active"    //Trading status of a contract
        },
        ETH/USD-PERP: {
		...
		}
	}
}
```

#### **Examples**

- https://api.testnet.biqutex.dev/api/v1/contracts?symbol=BTC/USD-PERP
-  https://api.testnet.biqutex.dev/api/v1/contracts?symbol=BTC/USD-PERP,ETH/USD_PERP
- https://api.testnet.biqutex.dev/api/v1/contracts?base=BTC
- https://api.testnet.biqutex.dev/api/v1/contracts?quote=USD	
- https://api.testnet.biqutex.dev/api/v1/contracts?symbol=BTC/USD-PERP&base=SUI



## /api/v1/trades

A list of recent trades on an active Biqutex exchange.

#### **Params**

- **symbol** - contract symbol, e.g.: BTC/USD-PERP. **REQUIRED!**
- **limit** - how many of the last trade to select for a response. **Default**: 100 (max 250)

#### **Response**

```javascript
{
	status: "OK",
	data: {
		BNB/USD-PERP: [
            {
                trade_id: 828562075,	//Global unique trade id
                price: 584.5,			//Price (in a quote currency)
                base_volume: 0.01,		//Volume (in a base currency)
                target_volume: 584,		//Notion volume (in quote currency)
                trade_timestamp: 1738932495637,	//Unix timestamp
                type: "BUY",			//Direction of the trade
                liquidation: false		//Liquidation flag
            },
            {
                trade_id: 828557228,
                price: 583.8,
                base_volume: 4.01,
                target_volume: 234103,
                trade_timestamp: 1738932401517,
                type: "BUY",
                liquidation: false
            },
            {
                trade_id: 797326515,
                price: 534.25,
                base_volume: 0.82,
                target_volume: 43808,
                trade_timestamp: 1738547745129,
                type: "SELL",
                liquidation: true
            }
		]
	}
}
```

#### **Examples**

- https://api.testnet.biqutex.dev/api/v1/trades?symbol=BTC/USD-PERP&limit=5



## /api/v1/orderbook

Retrieve order book information about bid and ask orders for requested symbol.

#### Params

- **symbol** - contract symbol, e.g.: BTC/USD-PERP. **REQUIRED!**

#### Response

```javascript
{
	status: "OK",
	data: {
		timestamp: 1738938616491,		//Datetime
		ticker_id: "BTC/USD-PERP",	
		bids: [		//Array of bids
	            [99490, 0.574],		//A price (in quote currency), size (in base asset)
	            [99489.9, 0.001],
	            [99489.6, 0.002],
	            [99489.1, 0.114],
	            [99489, 0.2],
	            [99488.6, 0.005],
	            [99488.4, 0.007],
	            [99488.1, 0.005],
	            [99488, 0.115],
	            [99486, 0.047],
	            [99485.6, 0.04],
	            [95626.6, 0.001],
	            [95626.4, 0.009],
	            [95626, 0.342]
		],
		asks: [
	            [99490.1, 8.145],
	            [99490.2, 0.114],
	            [99491.3, 0.338],
	            [99491.5, 0.002],
	            [99491.6, 0.001],
	            [99492, 0.047],
	            [99492.5, 0.002],
	            [99492.6, 0.592],
	            [99492.9, 0.192],
	            [99493.5, 0.002],
	            [99494, 0.047],
	            [99971.5, 0.03],
	            [99971.6, 0.075],
	            [99971.7, 0.26],
	            [99971.8, 0.103],
	            [99971.9, 0.601],
	            [99975.3, 2.609],
	            [99975.4, 3.266],
	            [99975.7, 0.013],
	            [99975.8, 0.013]
	]
	}
}
```

#### Examples

- https://api.testnet.biqutex.dev/api/v1/orderbook?symbol=BTC/USD-PERP



## /api/v1/ohlc

Provide a k-line data for a chart (TradingView compatibility)

#### Params

- **symbol** - contract symbol, e.g.: BTC/USD-PERP. **REQUIRED!**
- **resolution** - timeframe for k-line. Possible: digits 1,3,5,10,15,30,60 - as minutes OR {N}h (hour), {N}D (days), {N}W (week) or {N}M (month).
- **limits** - a maximum of 1000 points, default: 300
- **indextype** - a type of data: "markprice", "spotprice" or "fundingrate" as possible. Defaults is markprice.

#### Reponce

```javascript
{
	status: "OK",
	data: {
	s: "ok",	//Status
	t: [		//Times
            1739009353,
            1739009533,
            1739009713,
            1739009893,
            1739010073,
            1739010253
        ],
	c: [		//Close prices
            96158.6,
            96167.89,
            96138.51,
            96163.71,
            96196.3,
            96216.4
        ],
	o: [		//Open prices
            96091.88,
            96158.5,
            96167.87,
            96138.38,
            96163.71,
            96196.3
        ],
	h: [		//Hight prices
            96158.6,
            96185.4,
            96189.7,
            96189.3,
            96197.6,
            96228.52
        ],
	l: [		//Low prices
            96091.88,
            96158.5,
            96138.51,
            96138.38,
            96155.62,
            96196.2
        ],
	v: [ ]		//Volumes (optional, not populated now)
	}
}
```

#### Examples

- https://api.testnet.biqutex.dev/api/v1/ohlc?symbol=BTC/USD-PERP
- https://api.testnet.biqutex.dev/api/v1/ohlc?symbol=BTC/USD-PERP&resolution=3m
- https://api.testnet.biqutex.dev/api/v1/ohlc?symbol=BTC/USD-PERP&resolution=3m&limits=1000
- https://api.testnet.biqutex.dev/api/v1/ohlc?symbol=BTC/USD-PERP&resolution=3m&limits=250&indextype=fundingrate
