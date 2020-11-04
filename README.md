![logo](https://i.imgur.com/YAME6yT.png "#FrostyBot")

## Frostybot-JS : A NodeJS API endpoint for Cryptocurrency Trading

FrostyBot-JS is an API endpoint for REST and webhooks that is designed to receive API requests, such as Tradingview webhooks, and to submit them as orders to a cryptocurrency exchange.

The way it works is simple:

* There are six main trading commands, namely: 
  * **long** : Create a long order for the size provided. If you are already long, the order size will be adjusted so that the resulting position size equals the size you provided. If you are currently short, the order size will be adjusted so that your resulting position will be long the size you requested.
  * **short** : The inverse of the long command above
  * **buy**: Will create a simple order that will buy the amount requested
  * **sell**: The inverse of the buy order above
  * **close**: To reduce or close your current position. The size can be provided as a percentage, in case you would like to close 15% of your position (as an example)
  * **cancel**: To cancel one or more existing open orders
  Depending on the parameters you give these commands, you can open long or short positions, close positions, take profit (partial close) or cancel existing unfilled orders. The bot supports both limit and market orders and the commands are triggered either via a Tradingview alert webhook, or manually by you via the commandline.
* The size of the order is given using the size=xxx parameter (which automatically converts to USD sizing, even for non-USD pairs). Alternatively you can size the order by the base or quote currencies by using the base=xxx or quote=xxx parameters respectively.
* If you specify the price using the price=xxx parameter, a limit order will be used. If you omit the price, a market order will be used at the current market price.
* Anything that can trigger a webhook (like an alert in Tradingview) can be used with this bot for trade execution.

## Documentation

All documentation can be found on our [Wiki](https://github.com/CryptoMF/frostybot-js/wiki)

## Authors
Developers listed below can normally be found on the [FrostyBot Discord Server](https://discord.gg/yK4U93s). They are also known to frequent #the-lab channel on [Krown's Crypto Cave Discord Server](https://discordapp.com/invite/hzKU7qe):

* FrostyAF
* Barnz
* SemiQuasi

We kindly ask that you fully [**read the documentation**](https://github.com/CryptoMF/frostybot-js/wiki) before requesting any support, as most of the questions we get asked are already well documented.

## Disclaimer
Use this API at your own risk. The authors accept no responsibility for losses incurred through using this software. This is a 0.x release which means it's beta software. So it may and probably will have some bugs. We strongly advise you to use a sub-account with a limited balance, or a testnet account to ensure that the bot is profitable before going live with any strategy. While we have gone to great lengths to test the software, if you do find any bugs, please report them to us in the [FrostyBot Discord Server](https://discord.gg/yK4U93s) or on Github, and we will sort them out. Remember that risk management is your responsibility. If you lose your account, that's entirely on you.

## Supported Exchanges
Currently only FTX is supported. But we will add support for Deribit, Binance (Spot and Futures), and Bitmex in the near future. These exchanges are already supported on the PHP version of Frostybot, which you can find on this Github

## Scope      
This bot is specifically designed to execute orders based on webhook or REST API requests. These can be from Tradingview, or any other software that can execute webhooks.

While it may seem a little complicated to setup and configure for novices, the point of Frostybot is that it's entirely free. You retain total control over your API keys and trading engine. It is perfect for integration projects where you have a trading engine (like a Tradingview strategy or some custom Python script) that you want to integrate it with your exchange.

## Requirements
In order to use Frostybot, you will need the following:
* A Linux server, preferably running Ubuntu 20.04 LTS  (we recommend Amazon Lightsail for this purpose)
* A public static IP address

## Installation

To install Frostybot-JS on Ubuntu 20.04 LTS, run these commands:
```
curl -skL https://tinyurl.com/frostybot-js -o /tmp/install.sh 
sudo chmod +x /tmp/install.sh
sudo /tmp/install.sh
````
If you would like to monitor the install progress, you can tail the installer log file at /tmp/install.log

Alternatively, the latest docker image is available at frostyio/frostybot-js:latest

## Post-Install Configuration

#### Start the Frostybot server

To start the Frostybot server, use this command:
```
frostybot start
```

To see the status of the server, use this command:
```
frostybot status
```

#### Add your exchange API keys to Frostybot

You will need to identify this account in Frostybot using what is called a stub. It is a lowercase single word to name the account in Frostybot. You will use this stub in all subsequent interactions with the exchange. For example, if your exchange subaccount is called "Algo"-Trading"", add your API keys using this command:
```
frostybot accounts:add stub=mystub exchange=ftx apikey="<apikey>" secret="<secret>" subaccount="Algo-Trading"
```

**Important:** The subaccount parameter is case-sensitive. Make sure that it is exactly the same as the name on the exchange. The API keys will be tested with the exchange when you add them so you can be sure they work correctly. Once your API keys have been added, you're ready to start using Frostybot!

## Usage

Frostybot can take orders from the commandline and from webhooks or API requests. The commandline commands are the same as the commands provided in a webhook. They can be provided in inline command format, or in JSON format. Lets go through a couple of inline commands so that you can get a feel for how it works. These commands can be supplied on your Linux commandline, or in the message box of a Tradingview alert:

#### Account Information Commands
```
   frostybot trade:mystub:markets                       (Get list of markets supported on the exchange)
   frostybot trade:mystub:market symbol=SOL/USD         (Get market info for the SOL/USD pair)
   frostybot trade:mystub:positions                     (Get all current positions from exchange)
   frostybot trade:mystub:position symbol=LTC-PERP      (Get current position info for the LTC-PERP market)
   frostybot trade:mystub:orders                        (Get list of orders from the exchange)
   frostybot trade:mystub:order status=open             (Get list of open orders)
   frostybot trade:mystub:market symbol=SOL/USD         (Get market info for the SOL/USD pair)
   frostybot trade:mystub:balances                      (Get current equity balances from the exchange)     
```

#### Basic Trading Commands
```
    frostybot trade:mystub:long symbol=BTC-PERP size=1000             ($1000 market buy on BTC-PERP)
    frostybot trade:mystub:short symbol=BTC/USD size=2000 price=7600  ($2000 limit sell at $7600 on FTX BTC/USD)
    frostybot trade:mystub:long symbol=ETH-PERP size=3x               (Market buy 3x of my account balance ETH-PERP)
    frostybot trade:mystub:buy base=5 symbol=ADA/USD                  (Market buy 5 ADA using USD)
    frostybot trade:mystub:sell quote=500 symbol=ADA/USD              (Market sell $500 worth of ADA)
    frostybot trade:mystub:buy symbol=ETH/BTC usd=250                 (Market buy $250 worth of ETH, using BTC)
    frostybot trade:mystub:cancel id=10483883                         (Cancel order 10483883)
    frostybot trade:mystub:cancelall symbol=RUNE-PERP                 (Cancel all RUNE-PERP orders)
```

#### Advanced Commands

**TODO:** We will add some usage examples here in the next day or two, but for the moment there are plenty of examples on our Discord server
 
## Report Bugs

If you find a bug in the software, kindly report it [here](https://github.com/CryptoMF/frostybot-js/issues/new) so that we can properly track it.