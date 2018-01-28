# Transfer quarters as reward or refund

In this short guide, you'll learn how to transfer quarters as reward or refund
to users.

Once users log in, let's transfer them 2 quarters as login reward.

We are going to use OAuth for authentication. If you want to know more about how
OAuth works, check out
[Digital Ocean's tutorial](https://www.digitalocean.com/community/tutorials/an-introduction-to-oauth-2).

If you can want to know how to add "Login with Quarters" in your app,
[checkout our guide here](./login-quarters.md).

Let's start with client side. First add [Quarters Javascript SDK](../sdk/js.md)
in `index.html`:

```html
<script src='https://raw.githubusercontent.com/weiks/quarters-js/master/lib/Quarters.min.js'></script>
```

Create client using `app id` and `web secret` like this:

```js
var quarterOptions = {
  appId: <APP_ID>,
  appKey: <APP_KEY>,
  quartersURL: 'https://dev.pocketfulofquarters.com',
  apiURL: 'https://api.dev.pocketfulofquarters.com/v1/'
}
var client = new Quarters(quarterOptions);
```

Once user click on login button you can `authorize` with `iframe`. Authorizing
with user generates temporary `code` which you can use to generate
`refresh_token`.

```js
client.authorize('iframe', function(data) {
  var code = data.code

  // send this code to our node server to validate and generate refresh_token
  // on successful validation of code, transfer 2 quarters to user
})
```

Let's now work on server side. This guide uses [express](http://expressjs.com/)
framework for Node server.

```bash
# Install node SDK
npm install --save node-quarters
```

Create `quartersClient` on server. This uses `app secret` (Server API key), not
`app web secret`

```js
// Create quarters client
var quartersClient = new Quarters({
  appId: <APP_ID>,
  appKey: <APP_KEY>,
  serverAPIKey: <SERVER_API_KEY>,
  address: <APP_ETH_ADDRESS>,
  quartersURL: 'https://dev.pocketfulofquarters.com',
  apiURL: 'https://api.dev.pocketfulofquarters.com/v1/'
});
```

Create `POST` `route` in `express` app to generate refresh token using OAuth
generated `code`

```js
// POST route
quartersClient.createRefreshToken(code).then(function(data) {
  // get refresh_token (and access_token) and return to UI
  // data.refresh_token
  // data.access_token

  // you can use access_token to fetch user details
  return quartersClient.fetchUser(access_token).then(function(user) {
    // let's transfer 2 quarters to user
    return quartersClient
      .transferQuarters({
        user: user.id, // user id
        amount: 2 // 2 quarters
      })
      .then(function(data) {
        // data.txId => Ethereum transaction hash
      })
  })
})
```

### Example

Here is full example.

#### Client

##### index.html

Add `Javscript SDK` and add login button into `index.html`

```html
<!DOCTYPE html>
<html>
  <head>
    ....
  </head>
  <body>
    ....
    <button onclick="login()">Login with Quarters</button>
    ....
  </body>
  <!-- jQuery -->
  <script src="https://code.jquery.com/jquery-3.2.1.min.js"     integrity="sha256-hwg4gsxgFZhOsEEamdOYGBf13FyQuiTwlAQgxVSNgt4=" crossorigin="anonymous"></script>

  <!-- Quarters javascript SDK -->
  <script src='https://raw.githubusercontent.com/weiks/quarters-js/master/lib/Quarters.min.js'></script>

  <!-- index.js file -->
  <script src="index.js" charset="utf-8"></script>
</html>
```

##### index.js

```js
var quarterOptions = {
  appId: <APP_ID>,
  appKey: <APP_KEY>,
  quartersURL: 'https://dev.pocketfulofquarters.com',
  apiURL: 'https://api.dev.pocketfulofquarters.com/v1/'
}
var client = new Quarters(quarterOptions);

//
// On click login
//
var login = function () {
  // authorize user to quarters using iframe
  client.authorize('iframe', function(data) {
    if (data.code) {
      // fetch refresh token using code
      $.ajax({
        url: '/code',
        method: 'POST',
        data: JSON.stringify({code: data.code}),
        contentType: 'application/json',
        dataType: 'json'
      }).then(function(res) {
        // res.refresh_token => you can store into local-storage or cookies

        // set refresh token to quarters client
        return client.setRefreshToken(res.refresh_token);
      }).catch(function(e) {
        console.log(e);
      });
    }
  })
}
```

#### Server (Node.js)

##### Install Node SDK

```bash
npm install --save node-quarters
```

##### app.js

```js
var Quarters = requrie('node-quarters');
var express = require('express');
var app = express();

// Create quarters client
var quartersClient = new Quarters({
  appId: <APP_ID>,
  appKey: <APP_KEY>,
  address: <APP_ETH_ADDRESS>,
  quartersURL: 'https://dev.pocketfulofquarters.com',
  apiURL: 'https://api.dev.pocketfulofquarters.com/v1/'
});

...
...

// POST method route
app.post('/code', function (req, res) {
  var code = req.body.code;
  // create refresh token for user and fetch user
 return quartersClient
    .createRefreshToken(code)
    .then(function (data) {
      // get refresh_token
      var refresh_token = data.refresh_token;

      // get access_token
      var access_token = data.access_token;

      // fetch user using access token
      return quartersClient.fetchUser(access_token).then(function(user) {

        // transfer 2 quarters to user
        return quartersClient.transferQuarters({
          user: user.id,
          amount: 2 // amount to transfer
        }).then(function() {
          res.json({success: true});
        })
      })
   })
   .catch(function (e) {
     return res.status(400).json({
       message: e.message
     })
   });
});
```
