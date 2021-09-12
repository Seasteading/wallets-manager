# wallets-manager

`v0.2` currently

1. Install Docker, enable it in systemctl
2. Git pull repo
3. `cd service` and `npm install --no-optional`
4. Check docker-compose.yml environment variables
5. Run `docker-compose build --no-cache` and then `docker-compose up -d`

## I'm getting ERRS with docker-compose up

If you got some errs in `docker-compose up` output, try:

1. `rm -rf node_modules`
2. `docker-compose build --no-cache`
3. `docker-compose up -d`

If it didn't help, text @nikonovcc in Telegram.

## API

API methods documentation.

All questions: @nikonovcc in Telegram

### Access API

At `127.0.0.1:2311/…` without auth.

### API Methods

There are two kinds of managed wallets in system:

* game-wallets — `/game-wallet/{method}` — type of wallets which used for *deposits* in games. Game has only one game-wallet.
* user-wallets — `/user-wallets/{user-id}/{method}` — type of wallets which every user has, this wallet is users' game account

*Notice*: all query examples are JSON which you have to send in POST-data to service.

*Q — Query example. R — Response example.*

#### GET /game-wallet/

Return games' game-wallet blockchain address and balance.

R:

```js
{
    blockchain_address: string,
    balance: float
}
```

#### POST /game-wallet/withdraw

Withdraw money from game-wallet to user-wallet

Q:

```js
{   
    transaction_id: int,
    amount: float,
    to: int // user id in game database
}
```

R:

```json
  HTTP 200 OK RESPONSE CODE, no json-body
  or
  HTTP 500 INTERNAL SERVER ERROR ;)

  so wallets-manager is RPC API with WebhookMaster,
  Game will be notified about transaction status
```

#### POST /game-wallet/deposit

Send (enter deposit) money from user-wallet to game-wallet

Q:

```js
{   
    transaction_id: int,
    amount: float,
    from: int // user id in game database
}
```

R:

```json
  HTTP 200 OK RESPONSE CODE, no json-body
  or
  HTTP 500 INTERNAL SERVER ERROR ;)
```

---


`{user_id}` — users' id in *game* database (it will be the same). Create user-wallet linkedto his id in game database.

#### GET /user-wallets/{user_id}

Get user data (balance & blockchain address)

R:

```js
{   
    balance: float,
    blockchain_address: string
}
```

#### PUT /user-wallets/{user_id}

Create user-wallet.

Q:

`Nothing, just {user_id} in URL. Everything else generated automatically.`

R:

```js
{   
    blockchain_address: string
}
```

#### POST /user-wallets/{user_id}/withdraw

Withdraw BNB to 'private' user wallet (out of game system)

Q:

```js
{   
    transaction_id: int,
    amount: float,
    to: string // blockchain address
}
```

R:

```json
  HTTP 200 OK RESPONSE CODE, no json-body
  or
  HTTP 500 INTERNAL SERVER ERROR ;)
```

---

In development

#### POST /shop/buyOgle/{user_id}

Method for handling game microtransctions. User buys new egg or some beauty hat for monster for example. Will be implemented if needed.

<!--
 -->

### WebhookMaster

Sub-service (node `child_process`), that masters blockhain transactions statuses and notifies game about their changes. Games' Webhook listener url (destination) should be passed in docker-compose service environment. Webhook listener should listen POST requests.

Types of Webhook events:

1. "refill" — user-wallet has been refilled from 'outside' (no transaction_id will be sent)

*data:* `{amount: float, balance: float}`
2. "withdraw" — withdraw from user-wallet to 'outside' wallet completed successfully/failed

*data:* `{amount: float, balance: float}`
3. "deposit" — money from *user-wallet* to *game-wallet* transaction successfull/failed 

*data:* `{amount: float - how much money game-wallet got - how much money user's account got, balance: float - user-wallet balance after transaction}`
4. "exit" — money from *game-wallet* to *user-wallet* transaction successfull. This type is almost similar to "refill", but it has transaction_id, but "refill" don't (because it can't be requested by the game :)

*data:* `{}`

JSON:

```js
{   
    type: string, // more info below
    transaction_id: int,
    successful: bool,
    user: {
        id: int,
        balance: ,
        
    }
}
```

## External Useful Links

1. Binance Chain Docs (general) — https://docs.binance.org/guides/intro.html
2. Binance Chain Docs (developers) — https://docs.binance.org/guides/node/install.html
