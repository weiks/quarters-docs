# Node SDK

Node SDK provides helpers for Quarters APIs and OAuth2. It requires `appId`
(`client_id`) and `app_secret` (Server API key).

The first thing you'll need to do is sign up for
[Quarters](https://pocketfulofquarters.com) to
[create an app](../guides/create-app.md).

### Features

* Support for both API Key + Secret and OAuth 2 authentication.
* Convenient methods for making calls to the API.
* Automatic parsing of API responses into relevant JavaScript objects.

### Installation

```bash
npm install --save node-quarters
```

### Get started

Create a client -

```js
var client = new Quarters({
  appId: <APP_ID>,
  appKey: <APP_KEY>,
  serverAPIKey: <SERVER_API_KEY>,
  address: options.address, // Your ethereum account address (from app page)
  quartersURL: 'https://pocketfulofquarters.com',
  apiURL: 'https://api.pocketfulofquarters.com/v1/'
});
```

### OAuth APIs

You will need to do some work to obtain OAuth credentials for your players;
please refer to our
[OAuth 2 tutorial and documentation](../oauth/introduction.md).

Validate `code` and fetch `refresh_token` and `access_token`

```js
client.createRefreshToken(code).then(function(data) {
  // data.refresh_token
  // data.access_token
  //
  // send to UI
  //
})
```

### APIs

#### Create access token

If you already have `refresh_token`, you can generate `access_token` for player.

```js
client.createAccessToken(refreshToken).then(function(data) {
  // data.access_token
  // ...
  // ....
})
```

#### Player details

Once you have `access_token`, you can fetch user details and user related other
informations.

```js
client.fetchUser(accessToken).then(function(user) {
  console.log(user)
})
```

#### Transfer quarters to player (reward and refund)

```js
client.transferQuarters({
  user: <USER-ID>, // user id
  amount: 10 // 10 quarters
}).then(function(data) {
  // data.txId => Ethereum transaction hash of quarters transfers
});
```

### Examples

#### server based apps

```js
// On node server
var client = new Quarters({
  key: <APP_ID>,
  webSecret: <APP_KEY>, // Server API key
  secret: <SERVER_API_KEY>,
  address: <APP_ETH_ADDRESS>,
  quartersURL: 'https://pocketfulofquarters.com',
  apiURL: 'https://api.pocketfulofquarters.com/v1/'
});

// Validate code and fetch refresh token for user
client.createRefreshToken(code).then(function(data) {
  // fetch user details
  return client.fetchUser(data.access_token)
}).then(function(user) {
  var userId = user.id;
})

// refund/reward player the quarters
client.transferQuarters({
  user: userId,
  amount: 10
}).then(function(data) {
  // data.txId => transaction id
})
```
