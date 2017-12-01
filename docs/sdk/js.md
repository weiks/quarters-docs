# Javascript SDK

Javascript SDK provides helpers for Quarters APIs and OAuth2. It requires
`appId` (`client_id`) and `app_web_secret`.

The first thing you'll need to do is sign up for [Quarters](https://dev.pocketfulofquarters.com) to [create app](../guides/create-app.md)

!!! Warning
    Do not use `app_secret` (Server API Key). Anyone can see parameters
    passed to Javascript SDK as it will be used on browser.

### Features

* Support for both API Key + Secret and OAuth 2 authentication.
* Convenient methods for making calls to the API.
* Automatic parsing of API responses into relevant Javascript objects.

### Installation

```html
<script src='https://raw.githubusercontent.com/weiks/quarters-js/master/lib/Quarters.min.js'></script>
```

Using npm

```bash
npm install --save quarters-js

# Quarter = require("Quarters")
```

### Get started

Create client using `app_id` and `app_web_secret`.

```js
var client = new Quarters({
  appKey: <APP_ID>,
  appSecret: <APP_WEB_SECRET>,
  quartersURL: 'https://dev.pocketfulofquarters.com',
  apiURL: 'https://api.dev.pocketfulofquarters.com/v1/'
})
```

### OAuth APIs

If you're writing code that will act on behalf of another user, start by creating a new OAuth2 application. You will need to do some work to obtain OAuth credentials for your users; please refer to our [OAuth 2 tutorial and documentation](../oauth/introduction.md).

Authorize user through Quarters.

```js
client.authorize('iframe', function(data) {
  // data.code => temporary auth code
})
```

If you have `code` through `authorize`, you can get `refresh_token` and `access_token` using `setAuthCode` method. This method also set `refresh_token` in `client`

```js
client.setAuthCode(code).then(function(data) {
  // data.refresh_token => refresh token for client
  // data.access_token => access token for subsequent APIs calls

  // ...
  // ....
  // start making API calls
})
```

!!! Warning
    Setting `code` directly through SDK without validating at server is not safe. Use this method only for web-only applications. For server based applications, use server (Node SDK or APIs) to validate and get `refresh_token` and `access_token` from quarters server. Then, use `setRefreshToken` to set `refresh_token` to quarters client object.

### APIs

#### Set refresh token

If you already have `refresh_token`, you can skip `authorize` and `setAuthCode`, and set refresh token to client by calling `setRefreshToken`.

```js
client.setRefreshToken(refreshToken).then(function() {
  // ...
  // ....
  // start making API calls
})
```

#### User details

If you already have `refresh_token`, you can skip `authorize` and `setAuthCode`, and set refresh token to client by calling `setRefreshToken`.

```js
client.setRefreshToken(refreshToken).then(function() {
  // ...
  // ....
  // start making API calls
})
```

### Examples

#### web-only app

```js
var client = new Quarters({
  appKey: <APP_ID>,
  appSecret: <APP_WEB_SECRET>,
  quartersURL: 'https://dev.pocketfulofquarters.com',
  apiURL: 'https://api.dev.pocketfulofquarters.com/v1/'
})

client.authorize('iframe', function(data) {
  if (data.error) {
    // show alert (data.message contains error message)
  } else if (data.code) {
    client.setAuthCode(code).then(function() {
      // Get refresh token and store into local storage/cookie (optional)
      var refreshToken = client.getRefreshToken();
      window.localStorage.setItem('quarters-refresh-token', refreshToken);

      // ...
      // fetch user details
      // ...
    })
  }
})

// if you have refresh token already in storage or cookie
var refreshToken = window.localStorage.getItem();
if (refreshToken) {
  client.setRefreshToken(refreshToken).then(function() {
    // ...
    // fetch user details
    // ...
  })
}
```