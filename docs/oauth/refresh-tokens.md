# Access tokens and Refresh tokens

Quarters uses security feature of OAuth2 called refresh tokens. When you first authenticate, your app will be given an `access_token` and a `refresh_token`. The access token is used to authenticate all your requests, but expires in 10 minutes. Once an access token has expired, you will need to use the refresh token to obtain a new access token and a new refresh token. The refresh token never expires but it can only be exchanged once for a new set of access and refresh tokens. If you try to make a call with an expired access token, a 401 response will be returned.

This process adds some complexity for Quarters Connect integrations, but provides an valuable layer of security since a compromised access token is automatically revoked after 10 minutes.

To get a new access token, you'll need to do a POST request to `/oauth/token` just like before, but this time you must include your `refresh_token` and change the `grant_type` to `refresh_token`.

```curl
curl https://api.pocketfulofquarters.com/v1/oauth/token \
  -X POST \
  -d 'grant_type=refresh_token&
      client_id=YOUR_APP_ID&
      client_secret=YOUR_WEB_SECRET&
      refresh_token=REFRESH_TOKEN'
```

The expected result is a response containing the access token, as before:

```json
{
  "client_id": "...",
  "access_token": "...",
  "refresh_token": "..."
}
```

Note: If you are using an OAuth2 library that supports refresh tokens, you’re in luck - the library will automatically take care of these details.
