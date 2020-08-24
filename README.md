# Market Info
The Market Info is a REST API that provides up-to-date information about the market. More precisely, it is the data of world commodities, currencies and cryptocurrencies. The service allows access to data in JSON via HTTP.

The web app connects 3 microservices and usus gRPC to communicate with all of them, so the service works properly only if other three microservices are also running. To configure the targets (hosts and ports), set the variables in the config.yml file.

## Table of content
    - Dependencies
        - Commodity service
        - Currency service
        - Cryptocurency service
    - Tools
    - API sources
    - API documentation
    - Usage
    - Examples
    - Configuration
    - Directory structure

## Dependencies
The REST API server will start without any of these dependencies, but the handler endpoints that use these services will return an error message on requests.

### Commodity service
- [Source code](https://github.com/chutified/commodity-prices)
- [Documentation](https://github.com/chutified/commodity-prices/blob/master/README.md)
- [Dockerfile](https://github.com/chutified/commodity-prices/blob/master/Dockerfile)
- [Supported commodities](https://github.com/chutified/commodity-prices#supported-commodities)

### Currency service
- [Source code](https://github.com/chutified/currencies)
- [Documentation](https://github.com/chutified/currencies/blob/master/README.md)
- [Dockerfile](https://github.com/chutified/currencies/blob/master/Dockerfile)
- [Supported currencies](https://github.com/chutified/currencies/blob/master/README.md#supported-currency-codes)

### Cryptocurency service
- [source code](https://github.com/chutified/crypto-currencies)
- [Documentation](https://github.com/chutified/crypto-currencies/blob/master/README.md)
- [Dockerfile](https://github.com/chutified/crypto-currencies)
- [Supported cryptocurrencies](https://github.com/chutified/crypto-currencies/blob/master/docs/currencies.md)

## Tools
- [Gin framework](https://gin-gonic.com)
- [gRPC](https://grpc.io)
- [Git](https://git-scm.com)
- [Docker Engine](https://www.docker.com)
- [Swagger](https://swagger.io)

## API sources
- Commodity ([Business Insider](https://markets.businessinsider.com/currencies))
- Currency ([Business Inseider](https://markets.businessinsider.com/currencies))
- Cryptocurency ([CoinMarketCap](https://coinmarketcap.com/all/views/all))

## API documentation
The API uses the industry standart Swagger tool for its documentation.
Swagger documentation file: <a href="https://github.com/chutified/market-info/blob/master/docs/swagger.json">JSON</a>/<a href="https://github.com/chutified/market-info/blob/master/docs/swagger.yaml">YAML</a>

## Usage
### GET `/commodity/{name}`
If the commodity is supported, server returns the commodity's `name`, current `price`/`currency`/`weight_unit`, the price's change in `percentage` and `float`, and the time of the `last update`.

### GET `/i/currency/{name}`
If the currency is suported, server response with the currency's `name`, `country` in which it originated, `description` as the full name of the currency, `rate to USD`, `change` of the currency value in percentages and the time of the `last update`. The currency service also supports both currency symbols and names as the {name} value in a path.

### GET `/c/currency/{base}/{dest}`
If both {base} and {dest} are valid currency names or symbols, server returns the exchange `rate` of the bases and destination currencies. The currency service also supports both currency symbols and names as the {name} value in a path.

### GET `/crypto/{name}`
If the cryptocurrency is suported, server response with the cryptocurrency's `name`, `symbol`, `price` value in USD, `market capitalization`, `volume`, `circulating supply`, changes in last `hour`/`day`/`week` and whether is the currency `mineable`. The cryptocurrency service also supports both cryptocurrency symbols and names as the {name} value in a path.

## Examples
### GET /commodity/{name}: `/commodity/coal`
```
{
    "Name": "coal",
    "Price": 49.3,
    "Currency": "USD",
    "Weight_unit": "ton",
    "ChangeP": -0.4,
    "ChangeN": -0.2,
    "LastUpdate": 1597363200
}
```

### GET /i/currency/{name}: `/i/currency/cad`
```
{
    "Name": "CAD",
    "Country": "Canada",
    "Description": "Canadian Dollar",
    "Change": 0.33,
    "RateUSD": 1.3229,
    "UpdatedAt": "2020-08-24 15:04:00 +0000 UTC"
}
```

### GET /c/currency/{base}/{dest}: `/c/currency/cad/usd`
```
{
    "Rate": 0.7559
}
```

### GET /crypto/{name}: `/crypto/bitcoin`
```
{
    "Name": "BITCOIN",
    "Symbol": "BTC",
    "MarketCapUSD": 216941001293,
    "price": 11745.65,
    "CirculatingSupply": 18469900,
    "Mineable": false,
    "Volume": 20469801085,
    "ChangeHour": "0.04%",
    "ChangeDay": "0.51%",
    "ChangeWeek": "-4.28%"
}
```

### Server logs
```
[STATUS] 2020/08/24 21:58:21 Server is running on the port 3001.
::1 - [Mon, 24 Aug 2020 21:58:42 CEST] "GET /v1/commodity/coal HTTP/1.1 200 2.998192ms" curl/7.69.1
::1 - [Mon, 24 Aug 2020 21:59:25 CEST] "GET /v1/currency/i/cad HTTP/1.1 200 1.354023ms" curl/7.69.1
::1 - [Mon, 24 Aug 2020 21:59:42 CEST] "GET /v1/currency/c/cad/usd HTTP/1.1 200 1.813872ms" curl/7.69.1
::1 - [Mon, 24 Aug 2020 22:00:08 CEST] "GET /v1/crypto/bitcoin HTTP/1.1 200 3.813747ms" curl/7.69.1
```

## Configuration
**Default:**
```yaml
---
api_port: 80
commodity_service_target: 'localhost:10501'
currency_service_target: 'localhost:10502'
crypto_service_target: 'localhost:10503'
```
*api_port:* the port of the host on which the server is running
*commodity_service_target:* the "host:post" of the commodity microservice server
*currency_service_target:* the "host:post" of the currency microservice server
*crypto_service_target:* the "host:post" of the cryptocurrency microservice server

## Directory structure
```bash
_
├── config
│   └── config.go
├── data
│   ├── commodity_service.go
│   ├── crypto_service.go
│   └── currency_service.go
├── docs
│   ├── docs.go
│   ├── swagger.json
│   └── swagger.yaml
├── handlers
│   ├── commodities.go
│   ├── cryptos.go
│   ├── currencies.go
│   ├── errors.go
│   └── handler.go
├── models
│   ├── commodity.go
│   ├── crypto.go
│   └── currency.go
├── server
│   ├── routes.go
│   └── server.go
├── config.yml
├── Dockerfile
├── go.mod
├── go.sum
├── main.go
├── Makefile
├── README.md
└── server.log
```
