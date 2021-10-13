# wallets-manager

`v0.5` currently. Everything is module. Getting ready for OGLC-BNB exchange (v0.5), setting up NFT dev env.

Start the service:

1. Install Docker, enable it in systemctl,
2. `git pull` this repository,
3. do `cd service` and `npm install`,
4. create .env file peeping in example.env,
5. go back (`cd ..`) and start wallets-manager finally:

- startup with blockhain's `mainnet`: `docker-compose up -d`,
- with blockhain's `testnet`: `docker-compose -f docker-compose.testnet.yml up --build service`

if you got an error — contact maintainer :)

## API

API methods documentation. Access at `127.0.0.1:2311/…` without auth. Maintainer — @RickCastle2018 (@nikonovcc in Telegram).

Built with REST in mind. So all query examples are JSON which you have to send in POST-data to service. And there's example for each API method: *Q — Query struct. R — Response struct (provided if needed, otherwise just responce code).*

In examples you can find unusual type *Wei*. In blockchain all money amounts should be provided in Wei. Represented by `string` in JSON. Learn more: https://www.investopedia.com/terms/w/wei.asp

### game-wallet

game-wallet (`/game-wallet/{method}`) — it's our 'bank'. *In future* there will be a few types (ex. `/game-wallets/oglc/…`)

#### GET /game-wallet

Return game's game-wallet data: blockchain address and balance.

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

Withdraw money from game-wallet to user-wallet.

```js
Q:

{   
    transaction_id: int,
    amount: wei,
    to: int, // idInGame
    currency: string // oglc or bnb
}
```

#### POST /game-wallet/buy

It's simple: send money from user-wallet to game-wallet.

```js
Q:

{   
    transaction_id: int,
    currency: string, // oglc or bnb
    amount: wei,
    from: int // idInGame
}
```

### user-wallets

user-wallet (`/user-wallets/{user-id}/{method}`) — is a wallet which every user has, it's user's game account

`{user_id}` — should be the same as user's id in game's database.

#### GET /user-wallets/{user_id}

Get user data: balance and blockchain address.

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

Create new user-wallet with the following `{user_id}`. Returns blockchain address.

```js
R:

{   
    address: string
}
```

#### POST /user-wallets/{user_id}/withdraw

Withdraw OGLC/BNB out of game system. Fee will be paid.

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

Change exchange fees or bnbPrice. You can provide only one value (bnbPrice of fee). Notice: on init value will be from env in docker-compose.

```js
Q:

{   
    bnbPrice: int,
    fee: float
}
```

#### POST /user-wallets/{user_id}/exchange/calculate

Get calculations for exchange.

```js
Q:

{   
    transaction_id: int,
    currency: string, // bnb or oglc (what to exchange)
    amount: wei
}

R:

{
    possible: bool,
    why: string, // Reason, why exchange is impossible. Provided only if exchange possible: false.
    amount: wei, // how much user will get for amount of OGLC/BNB
    fee: wei, // FEE OF GAME IN WEI (our fee)
    bnbPrice:
}
```

#### POST /user-wallets/{user_id}/exchange

Exchange.

```js
Q: // Responce of /exchange/dry

{   
    transaction_id: int,
    currency: string, // bnb/oglc (what to exchange)
    amount: wei
}
```

### Webhooks

wallets-manager sends webhook to game when requested transaction processed or user-wallet was refilled. So game should listen POST requests, URL of this listener should be passed in .env file.

**Notice:** it works only for game-requested transactions so webhook won't be sent when user refilled his wallet for ex. And this will be the case until we set up our blockchain nodes with websockets.

Webhook JSON:

```js
{   
    transaction_id: int,
    type: string, // see types list below
    successful: bool,
    gasPaid: string // gas
    user: { // data about requested wallet after transaction (if requested /user-wallets/… there will be data about the following user-wallet, if /game-wallet - data about game-wallet)
        id: int, // will be provided only with transactions with user-wallets
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
2. "withdraw" — transfer from user-wallet to the external address
3. "purchase" — money from *user-wallet* to *game-wallet* transaction
4. "exit" — money from *game-wallet* to *user-wallet* transaction. This type is almost similar to "refill", but it has transaction_id, but "refill" don't (because it can't be requested by the game)

In future there will be also NFT-events.

### Backups

You know, without backups you can loose everything. So, see `/db/backup.sh` and `/db/restore.sh`. `backup.sh` should be set in CRON.
