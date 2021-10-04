# wallets-manager

`v0.4.3p` currently. October patch.

1. Install Docker, enable it in systemctl
2. Git pull repo
3. `cd service` and `npm install`
4. Check docker-compose.yml (and docker-compose.testnet.yml) environment variables
5. And start the service finally!

- MAINNET: Run `docker-compose build --no-cache` and then `docker-compose up -d`
- TESTNET: Run `docker-compose -f docker-compose.testnet.yml up --build service db` (don't forget to do `cd ..` if in /service dir)

---

*If you got some errs* while `docker-compose up`, try:

1. `rm -rf node_modules`
2. `npm install`
3. `docker-compose build --no-cache`
4. `docker-compose up -d`

if it didn't help — contact maintainer.

## API

API methods documentation. Access API at `127.0.0.1:2311/…` without auth. Maintainer — @RickCastle2018 (@nikonovcc in Telegram).

### API Methods

There are two kinds of managed wallets in system:

- game-wallet — `/game-wallet/{method}` — type of wallet which is our, let's say bank. Game has only one game-wallet.
- user-wallets — `/user-wallets/{user-id}/{method}` — type of wallets which every user has, this wallet is users' game account

*Notice*: all query examples are JSON which you have to send in POST-data to service. *Q — Query struct. R — Response struct.*

**Type *wei*:** all money amounts of BNB should be provided in Wei. Learn more: web3.utils.fromWei/toWei. Calculate (not for prod): `FLOATAMOUNT*(10**18)` (max 18 decimals), special BigNumbers lib should be used. Represented by `string` in JSON.

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
    amount: wei, // OGLC
    to: int, // user id in game database
    currency: string //oglc/bnb
}
```

#### POST /game-wallet/buy

It's simple: send money from user-wallet to game-wallet

```js
Q:

{   
    transaction_id: int,
    currency: string, //oglc/bnb
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

Withdraw OGLC/BNB to users private wallet (out of game system, to Metamask or Binance for example). Fee will be paid.

```js
Q:

{   
    transaction_id: int,
    amount: wei,
    to: string, // blockchain address
    currency: string // bnb or oglc
}
```

#### GET /user-wallets/{user_id}/exchange

Get exchange info.

```js
R:

{
    bnbPrice: int, // how much OGLC you can get for 1 BNB
    fee: float // int percentage (ex. 25)
}
```

#### POST /user-wallets/{user_id}/exchange/update

NOT IMPLEMENTED NOW

Change exchange fees or bnbPrice. You can provide only one value (bnbPrice of fee). Notice: on init value will be from env in docker-compose.

```js
Q:

{   
    bnbPrice: int,
    fee: float
}
```

#### POST /user-wallets/{user_id}/exchange

BUGGY. Not implemented.

Exchange. Service calculates everything itself.

```js
Q:

{   
    transaction_id: int,
    currency: string, // bnb/oglc (what to exchange)
    amount: wei
}

R:

{   
    possible
    amount: wei, // how much user will get for amount of OGLC/BNB
    fee: wei, // FEE OF GAME IN WEI OF EXCHANGED CURRENCY (our fee)
    bnbPrice: int // how much OGLC you can get for 1 BNB
}
```

#### POST /user-wallets/{user_id}/exchange/dry

If game wants to get fee, returned amount and so on.

```js
Q: // same, as /user-wallets/{user_id}/exchange

{   
    transaction_id: int,
    currency: string, // bnb/oglc (what to exchange)
    amount: wei
}

R:

{
    possible: bool,
    why: string, // Reason, why exchange is impossible. Provided only if exchange possible: false.
    amount: wei, // how much user will get for amount of OGLC/BNB
    fee: wei, // FEE OF GAME IN WEI (our fee)
}
```

### Webhooks

**NOTICE:** WORKS ONLY FOR GAME-REQUESTED TRANSACTIONS. NOT FOR REFILLS FROM OUTSIDE. GAME STILL SHOULD BE POLLING SERVICE FOR BALANCES.

Wallets-manager sends webhook to game when requested transaction approved or user-wallet was refilled. Games' Webhook listener url (destination) should be passed in docker-compose service environment. Webhook listener should listen POST requests.

JSON:

```js
{   
    transaction_id: int,
    type: string, // more info below
    successful: bool,
    user: { // data about game-wallet if /game-wallet method requested and data about user-wallet if /user-wallets method requested
        id: int, // in game IF THERE'S NO id, it means IT'S game-wallets
        balance: {
            bnb: wei,
            oglc: wei
        },
        address: string
    }
}
```

Types of Webhook events:

1. "refill" — user-wallet has been refilled from 'outside' (no transaction_id will be sent)
2. "withdraw" — withdraw from user-wallet to the 'outside' wallet completed
3. "purchase" — money from *user-wallet* to *game-wallet* transaction
4. "exit" — money from *game-wallet* to *user-wallet* transaction. This type is almost similar to "refill", but it has transaction_id, but "refill" don't (because it can't be requested by the game)

In future there will be also NFT-events

## External Useful Links

1. Web3.js — https://web3js.readthedocs.io/en/v1.5.2/
2. Binance Smart Chain Docs (developers) — https://docs.binance.org/smart-chain/developer/create-wallet.html
3. Binance Smart Chain Docs (general) — https://docs.binance.org/smart-chain/guides/bsc-intro.html