# OAuth APIs

OAuth APIS are standard OAuth2 APIs.

* **Authorize URL**: `https://dev.pocketfulofquarters.com/oauth/authorize`
* **Access Token URL**: `https://api.dev.pocketfulofquarters.com/v1/oauth/token`

### Authorize

```CURL
GET https://dev.pocketfulofquarters.com/oauth/authorize?response_type=code&client_id=YOUR_CLIENT_ID&redirect_uri=YOUR_REDIRECT_URL&inline=true
```

Parameters:

| Parameter       | Description                                                                              |
| --------------- | ---------------------------------------------------------------------------------------- |
| `response_type` | **Required** Value `code`                                                                |
| `client_id`     | **Required** The client ID you received after registering your application.              |
| `inline`        | **Required** Value `true`                                                                |
| `redirect_uri`  | **Optional** The URL (encoded) in your app where users will be sent after authorization. |

### Code retrieval

On user approval, Quarters will redirect user to `redirect_uri` with following code as query params:

| Query param | Description                               |
| ----------- | ----------------------------------------- |
| `code`      | Temporary `code` to retrieve access token |

### Refresh token

```CURL
POST https://api.dev.pocketfulofquarters/v1/oauth/token
```

with following parameters:

| Parameter       | Description                                                              |
| --------------- | ------------------------------------------------------------------------ |
| `grant_type`    | **Required** Value `authorization_code`                                  |
| `code`          | **Required** Value from step 2                                           |
| `client_id`     | **Required** The APP ID you received after registering your application  |
| `client_secret` | **Required** The APP_KEY you received after registering your application |

Example request:

```CURL
curl https://api.dev.pocketfulofquarters.com/v1/oauth/token \
  -X POST \
  -H 'Content-Type: application/json;charset=UTF-8' \
  --data-binary '{"client_id":"Lpk5sPrA7P59HFlN7obS","client_secret":"1s4x2v8h3b9ollw1pt2afj8knheamvmvv","grant_type":"authorization_code","code":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOiJ2R2lMR3Z3UERYVHUzVnlFQlRySmNJdTJhNzAyIiwicmFuZG9tIjoiMDgxMmMxNTAtZDY2NC0xMWU3LTg3Y2UtZjEyMTk1M2I0ZWQxIiwiYXBwSWQiOiJMcGs1c1ByQTdQNTlIRmxON29iUyIsInRva2VuVHlwZSI6Imp3dDphdXRob3JpemF0aW9uX2NvZGUiLCJpYXQiOjE1MTIxMTEwNzcsImV4cCI6MTUxMjExMTEzN30.Jljjd7yjk-cr1uYSq0kSTwCpK8zNq8YGrVk_AQu6u4c"}'
```

After a successful request, a valid access token will be returned in the response:

```json
{
  "client_id": "Lpk5sPrA7P59HFlN7obS",
  "refresh_token": "d832e7c419869...5918b96",
  "access_token": "eyJhbGciOiJIU...81plg"
}
```

!!! info

     `access_token` is short lived token. It will expire in 30 minutes. Use `refresh_token` to retrieve new `access_token`.

You can know more about `refresh_token` here: [Refresh tokens and access tokens](../oauth/refresh-tokens.md)

### Access token

Use `refresh_token` to retrieve `access_token`

```CURL
POST https://api.dev.pocketfulofquarters.com/v1/oauth/token
```

with following parameters:

| Parameter       | Description                                                              |
| --------------- | ------------------------------------------------------------------------ |
| `grant_type`    | **Required** Value `refresh_token`                                       |
| `refresh_token` | **Required** Value of refresh_token                                      |
| `client_id`     | **Required** The APP ID you received after registering your application  |
| `client_secret` | **Required** The APP_KEY you received after registering your application |

```CURL
curl https://api.dev.pocketfulofquarters.com/v1/oauth/token \
  -X POST \
  -H 'Content-Type: application/json;charset=UTF-8' \
  --data-binary '{"client_id":"Lpk5sPrA7P59HFlN7obS","client_secret":"1s4x2v8h3b9ollw1pt2afj8knheamvmvv","grant_type":"refresh_token","refresh_token":"eyJhbGciOiJIUzI1Ni....kSTwCpK8zNq8YGrVk_AQu6u4c"}'
```

After a successful request, a valid access token will be returned in the response:

```json
{
  "client_id": "Lpk5sPrA7P59HFlN7obS",
  "access_token": "eyJhbGciOiJIU...81plg"
}
```

### Make an authenticated API call

```
GET https://api.dev.pocketfulofquarters.com/v1/me
```

with following headers:

| Parameter       | Description                                   |
| --------------- | --------------------------------------------- |
| `Authorization` | **Required** Format - `Bearer <access-token>` |

Example request:

```CURL
curl https://api.dev.pocketfulofquarters.com/v1/me \
  -H 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOiJ2R2lMR3Z3UERYVHUzVnlFQlRySmNJdTJhNzAyIiwicmFuZG9tIjoiMWE5Njk3YzAtZDY2NC0xMWU3LTg3Y2UtZjEyMTk1M2I0ZWQxIiwiYXBwSWQiOiJMcGs1c1ByQTdQNTlIRmxON29iUyIsImFkbWluIjpmYWxzZSwiZGV2ZWxvcGVyIjpmYWxzZSwidG9rZW5UeXBlIjoiand0OnVzZXIiLCJpYXQiOjE1MTIxMTExMDgsImV4cCI6MTUxMjExMTcwOH0.PFlJjGoN3xza1Qk3ZIBaOzMPyYbrQvnjyvyvQf81plg'
```

Example response:

```json
{
  "id": "vGiLGvwPDXTu3VyEBTrJcIu2a702",
  "userId": "vGiLGvwPDXTu3VyEBTrJcIu2a702",
  "displayName": "User1",
  "email": "user1@example.com",
  "emailVerified": true
}
```

All quarters related APIs are private APIs. Use `access_token` to access them.
