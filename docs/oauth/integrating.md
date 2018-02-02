# Integrating Quarters OAuth with your web game apps

Before integrating Quarters OAuth, you'll need to register a new OAuth2 application under [your profile](https://dev.pocketfulofquarters.com/apps/new). If you're using a previous OAuth2 implementation, you may need to configure the following settings:

* **Authorize URL**: `https://dev.pocketfulofquarters.com/oauth/authorize`
* **Access Token URL**: `https://dev.pocketfulofquarters.com/oauth/token`

!!! warning

      Quarters is still in beta. API may change in stable version.

To integrate your third-party web server application with Quarters, use the following flow:

### 1. Redirect users to request Quarters access

```CURL
GET https://dev.pocketfulofquarters.com/oauth/authorize?response_type=code&client_id=YOUR_CLIENT_ID&redirect_uri=YOUR_REDIRECT_URL&inline=true
```

When redirecting a user to Quarters to authorize access to your application, you'll need to construct the authorization URL with the correct parameters. Here's a list of parameters you should always specify:

| Parameter       | Description                                                                              |
| --------------- | ---------------------------------------------------------------------------------------- |
| `response_type` | **Required** Value `code`                                                                |
| `client_id`     | **Required** The client ID you received after registering your application.              |
| `inline`        | **Required** Value `true`                                                                |
| `redirect_uri`  | **Optional** The URL (encoded) in your app where users will be sent after authorization. |

Example of an authorization URL:

```CURL
GET https://dev.pocketfulofquarters.com/oauth/authorize?response_type=code&client_id=Lpk5sPrA7P59HFlN7obS&inline=true&redirect_uri=https%3A%2F%2Fexample.com%2Foauth%2Fcallback
```

### 2. Quarters redirects back to your site

If the user approves your application, Quarters will redirect them back to your `redirect_uri` with a temporary code parameter.

Example of the redirect:

```CURL
GET https://example.com/oauth/callback?success=true&code=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOiJ2R2lMR3Z3UERYVHUzVnlFQlRySmNJdTJhNzAyIiwicmFuZG9tIjoiNmFkODI2ZjAtZDY1ZS0xMWU3LTg3Y2UtZjEyMTk1M2I0ZWQxIiwiYXBwSWQiOiJMcGs1c1ByQTdQNTlIRmxON29iUyIsInRva2VuVHlwZSI6Imp3dDphdXRob3JpemF0aW9uX2NvZGUiLCJpYXQiOjE1MTIxMDg2NjYsImV4cCI6MTUxMjEwODcyNn0.Pwl3Mt_RN5Qut5VpCshP32FkZ_lES84PrDnzIGDG8J0
```

**OAuth2 Redirect URI**

For added security, all `redirect_uri` must use SSL (i.e. begin with `https://`). URIs without SSL can only be used for development and testing and will not be supported in production.

### 3. Exchange code for an access token

After you have received the temporary code, you can exchange it for valid access and refresh tokens. This can be done by making a `POST` call:

```CURL
POST https://api.dev.pocketfulofquarters/v1/oauth/token
```

With following parameters:

| Parameter       | Description                                                                     |
| --------------- | ------------------------------------------------------------------------------- |
| `grant_type`    | **Required** Value `authorization_code`                                         |
| `code`          | **Required** Value from step 2                                                  |
| `client_id`     | **Required** The app ID you received after registering your application         |
| `client_secret` | **Required** The **web secret** you received after registering your application |

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

### 4. Make an API call

After you have a valid access token, you can make your first API call:

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
