# Login with Quarters

In this short guide, you'll learn how to add "Login with Quarters" and how to
retrieve player details. We are going to use OAuth for authentication. If you
want to know more about how OAuth works, check out
[Digital Ocean's tutorial](https://www.digitalocean.com/community/tutorials/an-introduction-to-oauth-2).

Now, let's create simple login functionality and fetch user information from
Quarters server. This example uses node server to validate OAuth generated code.

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
  quartersURL: 'https://pocketfulofquarters.com',
  apiURL: 'https://api.pocketfulofquarters.com/v1/'
}
var client = new Quarters(quarterOptions);
```

Once user click on login button you can `authorize` with `iframe`. Authorizing
with user generates temporary `code` which you can use to generate
`refresh_token`.

After receiving `refresh_token`, you can set `refresh_token` to
`setRefreshToken` SDK API.

```js
client.authorize('iframe', function(data) {
  var code = data.code

  // send this code to our node server to validate and generate refresh_token

  // set refresh_token to client
  client.setRefreshToken(refresh_token).then(function() {
    // start using client object to fetch user details
  })
})
```

Once you have set `refresh_token` to `client`, you can now use `client` object
to fetch user details, create transfer request, etc...

Fetch user:

```js
client.me().then(function(user) {
  // Use user object
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
  quartersURL: 'https://pocketfulofquarters.com',
  apiURL: 'https://api.pocketfulofquarters.com/v1/'
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
  // quartersClient.fetchUser(access_token)
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
  quartersURL: 'https://pocketfulofquarters.com',
  apiURL: 'https://api.pocketfulofquarters.com/v1/'
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
      }).then(function() {
        // fetch user details
        return fetchUserDetails();
      }).catch(function(e) {
        console.log(e);
      });
    }
  })
}


// Fetch user details using client after setting refresh_token
var fetchUserDetails = function () {
  return client.me().then(function(user) {
    console.log(user);
  });
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
  quartersURL: 'https://pocketfulofquarters.com',
  apiURL: 'https://api.pocketfulofquarters.com/v1/'
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
      // quartersClient.fetchUser(access_token);

      // return tokens to UI
      return res.json({
        access_token: access_token,
        refresh_token: refresh_token
      });
   })
   .catch(function (e) {
     return res.status(400).json({
       message: e.message
     })
   });
});
```
