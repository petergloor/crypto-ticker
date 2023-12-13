# Crypto Ticker

##### This is a slightly modified fork of the repository found at https://github.com/Howchoo/crypto-ticker.
##### If you use this repository you must also follow the instructions below that explains some differences to the original instructions that can be found at https://howchoo.com/pi/raspberry-pi-cryptocurrency-ticker.

A library for displaying crypto asset prices on an LED matrix panel using a Raspberry Pi.

Requires:

  * Adafruit 64x32 LED Matrix Panel
  * Raspberry Pi Zero WH
  * CoinMarketCap API Account

See the Howchoo guide for installation and configuration instructions:

https://howchoo.com/pi/raspberry-pi-cryptocurrency-ticker

## Differences to the original instructions.

#### Step 1: Install Raspberry Pi OS
This is more a recommendation than an absolute requirement.

In the Imager make sure to select an image for the correct Pi device, which will probably be a _Raspberry Pi Zero_. 

In the Operating System tab open _Raspberry Pi OS (other)_ and choose _Raspberry Pi OS (Legacy, 32-bit) Lite_.

Finally, after you choose the Storage and press NEXT, you will be prompted for _Use OS customisation_. Make sure to fill in all the data in all sections, save your changes and select YES to create your customized image. If you are using a headless setup over ssh, it is important to use a customized user name other than _pi_ to make the first login over ssh possible. Otherwise a prompt will be displayed (on a monitor you might have attached to the HDMI port on your Raspberry Pi) and asks for a new username to be setup. In the documantation below I used the user name _demo_, but you can and should choose any other valid user name for a linux system you like.

At this point I would like to mention two other things. Due to the havy setup with Docker and the system building processes for your application, it's important to physically position your Raspberry Pi near a router or WLAN extender to make sure you have the best possible WLAN access to the Internet. In addition I highly recommend to use a highspeed MicroSDHC with read/write transfer rates of 80 or more MB/s (I used a Kingston Canvas Select Plus microSDHC Card 32GB). Any size between 8 and 32 GB is ok. Due to the havy write operations during the downloads and the build processes the speed makes a big difference in the duration of the build and it avoids errors related to timeouts.

#### Step 5: SSH into your Pi

After its first start it may take quite a long time before you can connect to your Pi. During the first setup the Pi the does a lot of things and it will reboot before a WLAN connection can be established. It might take several minutes before you can get access over SSH.

Please note, that you must use _your username_ and _hostneme_ and NOT the username _pi_ as mentioned in the documentation.
e.g.

    ssh demo@raspberrypi

.. or any other user and host name you have provided in your configuration settings.

#### Step 6: Install Docker

This section has completely changed. 

    # Set swapping off
    sudo swapoff -a

    # Install docker
    curl -sSL https://get.docker.com | sh

    # Add your user to the docker group (so you don't have to use root to use docker)
    sudo usermod -aG docker $USER

DON'T Install _docker-compose_, since it's now integrated with Dockers command _docker compose_.
If you install it separately, you will install conflicting libraries and your build will fail.

#### Step 7: Clone the crypto-ticker repository
Actually you will clone this slightly modified fork of the original crypto-ticker repository with the following command:

    git clone https://github.com/petergloor/crypto-ticker.git

From now on for all your operations you need to cd into the crypto-ticker directory:

    cd crypto-ticker

#### Step 8: (Optional) Create your own settings.env file_ 
Create the file as decribed in the original documentation (and near the end of this README).

#### Step 9: Run docker-compose up
Oops... never ever use the command _docker-compose_. 

From now on only use _docker compose_ without the dash!!!

With your settings.env file in place, you’re ready to start the application. Run the following:

    docker compose up -d

#### Final note

For the rest you can follow the documentation, but remember to use _docker compose_ (without dash) and use your username and/or hostname where pi@raspberrypi or pi@<ip> is mentioned.


## Settings

You can customize the application by adding any of the following settings to your settings.env file in the root directory of this repo:


| Name | Default | Description |
|--|--|--|
| SYMBOLS | btc,eth | The asset symbols you want to track. |
| CURRENCY | usd | The currency used to show asset prices. CoinGecko currently supports "usd" and "eur", while CoinMarketCap supports only "usd". |
| API | coingecko | The API you want to use to fetch price data. Currently supported APIs are "coingecko" and "coinmarketcap". |
| REFRESH_RATE | 300 | How often to refresh price data, in seconds. |
| SLEEP | 3 | How long each asset price displays before rotating, in seconds. |
| CMC\_API\_KEY | | The CoinMarketCap API key, required if you specified API=coinmarketcap. |
| SANDBOX | | Used for CoinMarketCap only. Set SANDBOX=false if you're developing and want to use the sandbox API. |

Example:

```
SYMBOLS=btc,eth,ltc,xrp
API=coingecko
```

Note: Some symbols are ambiguous. For example, `uni` currently corresponds to three different currencies in the CoinGecko API. To specify the
currency or token you want (with CoinGecko only), you can use the following:

```
SYMBOLS=btc,eth,uni:uniswap
```

The second value (uniswap) corresponds to the ID of the currency in the API. This is currently only supported for the CoinGecko API. You can
find the CoinGecko ID for a token in the URL. E.g. https://www.coingecko.com/en/coins/uniswap.
