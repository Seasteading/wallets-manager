# wallets-manager

`v0.3` currently.

1. Install Docker, enable it in systemctl
2. Git pull repo
3. `cd service` and `npm install`
4. Check docker-compose.yml environment variables
5. MAINNET: Run `docker-compose build --no-cache` and then `docker-compose up -d`, TESTNET: `docker-compose -f docker-compose.testnet.yml up --build service`

*If you got some errs* while `docker-compose up`, try:

1. `rm -rf node_modules`
2. `docker-compose build --no-cache`
3. `docker-compose up -d`

if didn't help — contact maintainer.

## API

API methods documentation. Access API at `127.0.0.1:2311/…` without auth. Maintainer — RickCastle2018 (@nikonovcc in Telegram).

### API Methods

There are two kinds of managed wallets in system:

* game-wallets — `/game-wallet/{method}` — type of wallets which used for *deposits* in games. Game has only one game-wallet.
* user-wallets — `/user-wallets/{user-id}/{method}` — type of wallets which every user has, this wallet is users' game account

*Notice*: all query examples are JSON which you have to send in POST-data to service. *Q — Query example. R — Response example.*

**Type «wei»:** all money amounts should be provided in Wei. Learn more: web3.utils.fromWei/toWei

#### GET /game-wallet

Return games' game-wallet blockchain address and balance.

```js
R:

{
    address: string,
    balance: {
        bnb: wei,
        oglc: wei
    }
}
```

#### POST /game-wallet/withdraw

Withdraw money from game-wallet to user-wallet

```js
Q:

{   
    transaction_id: int,
    amount: wei,
    to: int // user id in game database
}
```

#### POST /game-wallet/deposit

Send (enter deposit) money from user-wallet to game-wallet

```js
Q:

{   
    transaction_id: int,
    amount: wei,
    from: int // user id in game database
}
```

---

`{user_id}` — users' id in *game* database (it will be the same). Create user-wallet linked to his id in game database.

#### GET /user-wallets/{user_id}

Get user data (balance & blockchain address)

```js
R:

{   
    balance: {
        bnb: wei,
        oglc: wei
    },
    address: string
}
```

#### PUT /user-wallets/{user_id}

Create user-wallet.

```js
R:

{   
    address: string
}
```

#### POST /user-wallets/{user_id}/withdraw

Withdraw OGLC/BNB to users private wallet (out of game system, to Metamask or Binance for example)

```js
Q:

{   
    transaction_id: int,
    amount: wei,
    to: string // blockchain address
    currency: string // bnb or oglc
}
```

#### POST /user-wallets/{user_id}/exchange

Simplified exchange. All calcualtions should be on games' side. *bnb* - how much BNB to transfer from user-wallet to game-wallet. *oglc* - how much OGLC to transfer from game-wallet to user-wallet.

```js
Q:

{
    transaction_id: int,
    bnb: wei,
    oglc: wei
}
```

### Webhooks

**NOW WEBHOOKS DON'T WORK. GAME SHOULD POLL SERVICE FOR BALANCES.**

Wallets-manager sends webhook to game when requested transaction approved or user-wallet was refilled. Games' Webhook listener url (destination) should be passed in docker-compose service environment. Webhook listener should listen POST requests.

JSON:

```js
{   
    transaction_id: int,
    type: string, // more info below
    successful: bool,
    user: {
        id: int, // in game
        balance: { // in Wei
            bnb: bigint,
            oglc: bigint
        },
        address: string
    }
}
```

Types of Webhook events:

1. "refill" — user-wallet has been refilled from 'outside' (no transaction_id will be sent)
2. "withdraw" — withdraw from user-wallet to the 'outside' wallet completed
3. "deposit" — money from *user-wallet* to *game-wallet* transaction
4. "exit" — money from *game-wallet* to *user-wallet* transaction. This type is almost similar to "refill", but it has transaction_id, but "refill" don't (because it can't be requested by the game)

In future there will be also NFT-events

## External Useful Links

1. Web3.js — https://web3js.readthedocs.io/en/v1.5.2/
2. Binance Smart Chain Docs (developers) — https://docs.binance.org/smart-chain/developer/create-wallet.html
3. Binance Smart Chain Docs (general) — https://docs.binance.org/smart-chain/guides/bsc-intro.html