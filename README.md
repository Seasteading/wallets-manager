# wallets-manager

`v0.1.0` — beta

1. Install Docker
2. Clone repo
3. Run `docker-compose up`

## API

API methods documentation. No Swagger yet.

All questions: @nikonovcc in Telegram

### Access API

At `localhost:8080/…` (i can change service port if needed)

Send all requests to wallets-manager API with Header `Authorization: {Game Codename}-{Access Token}`. All connected games are managed manually by adding in `connected-games.json` file.

### API Methods

There are two kinds of managed wallets in system:

* deposit-wallets — `/deposit/{method}` — type of wallets which used for *deposits* in games
* user-walletss — `/user/{user-id}/{method}` — type of wallets which has every user, this wallet is his game account

#### /deposit

*API methods to interact with master-wallets*

* `PUT /deposit`
MUST BE USED BY PEOPLE, NOT GAMES
Connect new game to wallets-manager. It creates new master wallet and because there is *only one master-wallet per game* it means, that you connet new game.

Example query:

```json
{
    codename: string // future game CODENAME for authentication
}
```

Example response:

```json
    {
        access_token: string
    }
```

* `GET /deposit`
Return games' deposit-wallet blockchain address and (all transactions?)

Example response:

```json
    {
        blockchain_address: string
    }
```

* `POST /deposit/withdraw`

Withdraw money from deposit-wallet to user-wallet

Example query:

```json
{
    amount: float
    to: int // user id in game database
}
```

Example response:

```json
    {
        successful: bool
        error: string // retuned if there was an error
    }
```

* `POST /deposit/enter`

Send (enter deposit) money from user-wallet to deposit-wallet

Example query:

```json
{
    amount: float
    from: int // user id in game database
}
```

Example response:

```json
    {
        successful: bool
        error: string // retuned if there was an error
    }
``` 

#### /user


