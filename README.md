# wallets-manager

`v0.1.1ß` — in development, will be released after all bugs fixed

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

**If you got err that mentioning something about Python when `docker-compose build`, you can pay no attention.** Otherwise, text developer.

## API

API methods documentation.

All questions: @nikonovcc in Telegram

### Access API

At `127.0.0.1:2311/…` without auth.

### API Methods

There are two kinds of managed wallets in system:

* round-wallets — `/round-wallet/{method}` — type of wallets which used for *deposits* in games. Game has only one round wallet.
* user-wallets — `/user-wallets/{user-id}/{method}` — type of wallets which every user has, this wallet is users' game account

*Notice*: all query examples are JSON which you have to send in POST-data to service.

*Q — Query example. R — Response example.*

#### round-wallet methods

Interact with round wallets

##### GET /round-wallet/

Return games' round-wallet blockchain address and balance.

R:

```js
{
    blockchain_address: string,
    balance: float
}
```

##### POST /round-wallet/withdraw

Withdraw money from round-wallet to user-wallet

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

##### POST /round-wallet/deposit

Send (enter deposit) money from user-wallet to round-wallet

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

  so wallets-manager is RPC API with WebhookMaster,
  Game will be notified about transaction status
```

#### user-wallets methods

API methods to interact with user-wallets

`{user_id}` — users' id in *game* database (it will be the same). Create user-wallet linkedto his id in game database.

##### GET /user-wallets/{user_id}

Get user data (balance & blockchain address)

R:

```js
{   
    balance: float,
    blockchain_address: string
}
```

##### PUT /user-wallets/{user_id}

Create user-wallet.

Q:

`Nothing, just {user_id} in URL. Everything else generated automatically.`

R:

```js
{   
    blockchain_address: string
}
```

##### POST /user-wallets/{user_id}/withdraw

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

  so wallets-manager is RPC API with WebhookMaster,
  Game will be notified about transaction status
```

##### POST /user-wallets/{user_id}/buy

Method for handling game microtransctions. User buys new egg or some beauty hat for monster for example. Will be implemented if needed.

<!-- 
 -->

##### GET /user-wallets/{user_id}/transactions

Will be implemented after beta

<!--
R:

```js
{   
    transactions: [
        {
            TRANSACTION JSON
        },
        …
    ]
}
```  -->

### WebhookMaster

Sub-service (node `child_process`), that masters blockhain transactions statuses and notifies game about their changes.

Webhook listener url (destination) should be passed in docker-compose service environment. Webhook listener should listen POST requests.

#### Webhook JSON

`type`s:

For now, game will get webhooks *only* when "refill", no transaction_ids!:

1. "refill" — user-wallet has been refilled from 'outside' (no transaction_id will be sent)

JSON:

```js
{   
    user_id: int,
    type: "refill",
    amount: float
}
```

*Will be supported in v0.2 (codebase is ready, all vars processed, need to implement logics):*

JSON:

```js
{   
    user_id: int,
    type: string, // more info below
    transaction_id: int,
    status: "success"/"fail" string,
    data: {
        ...
    }
}
```

1. "withdraw" — withdraw from user-wallet to 'outside' wallet completed successfully/failed | *data:* {amount: float, balance: float}
2. "deposit" — money from *user-wallet* to *round-wallet* transaction successfull/failed | *data:* {amount: float - how much money round-wallet got - how much money user's account got, balance: float - user-wallet balance after transaction}
3. "exit" — money from *round-wallet* to *user-wallet* transaction successfull/failed | *data:* {}

*NOTICE:* type "exit" almost similar to "refill", but it has transaction_id, when "refill" don't (because it can't be requested by the game :)

## External Useful Links

1. Binance Chain Docs (general) — https://docs.binance.org/guides/intro.html
2. Binance Chain Docs (developers) — https://docs.binance.org/guides/node/install.html
