# User APIs

### User details

```
GET https://api.pocketfulofquarters.com/v1/me
```

with following headers:

| Parameter       | Description                                   |
| --------------- | --------------------------------------------- |
| `Authorization` | **Required** Format - `Bearer <access_token>` |

It's a private API and only be accessible with `access_token`. [ Check out Oauth APIs](./user.md)
to know how to retrieve `access_token`.

Example request:

```CURL
curl https://api.pocketfulofquarters.com/v1/me \
  -H 'Authorization: Bearer eyJhbGci...YbrQvnjyvyvQf81plg'
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

### Accounts

```
GET https://api.pocketfulofquarters.com/v1/accounts
```

with following headers:

| Parameter       | Description                                   |
| --------------- | --------------------------------------------- |
| `Authorization` | **Required** Format - `Bearer <access_token>` |

Currently response will have only one account in account list.

Example request:

```CURL
curl https://api.pocketfulofquarters.com/v1/accounts \
  -H 'Authorization: Bearer eyJhbGciOiJIU...vyvQf81plg'
```

Example response:

```json
[
  {
    "id": "0xfB6916095ca1df60bB79Ce92cE3Ea74c37c5d359",
    "address": "0xfB6916095ca1df60bB79Ce92cE3Ea74c37c5d359",
    "created": "November 29, 2017 at 5:25:14 AM",
    "userId": "vGiLGvwPDXTu3VyEBTrJcIu2a702"
  }
]
```

### Account balance

```
GET https://api.pocketfulofquarters.com/v1/accounts/{account-address}/balance
```

with following url params:

| Url params        | Description                                                     |
| ----------------- | --------------------------------------------------------------- |
| `account-address` | **Required** Account address. It must be valid Ethereum address |

with following headers:

| Parameter       | Description                                   |
| --------------- | --------------------------------------------- |
| `Authorization` | **Required** Format - `Bearer <access_token>` |

Example request:

```CURL
curl https://api.pocketfulofquarters.com/v1/accounts/0xfB6916095ca1df60bB79Ce92cE3Ea74c37c5d359/balance \
  -H 'Authorization: Bearer eyJhbGciOiJIU...vyvQf81plg'
```

Example response:

```json
[
  {
    "quarters": "5000",
    "formattedQuarters": "5,000",
    "ethers": "1000000000000000000",
    "formattedEthers": "1.00"
  }
]
```

### Account reward

```
GET https://api.pocketfulofquarters.com/v1/accounts/{account-address}/rewards
```

with following url params:

| Url params        | Description                                                     |
| ----------------- | --------------------------------------------------------------- |
| `account-address` | **Required** Account address. It must be valid Ethereum address |

with following headers:

| Parameter       | Description                                   |
| --------------- | --------------------------------------------- |
| `Authorization` | **Required** Format - `Bearer <access_token>` |

Example request:

```CURL
curl https://api.pocketfulofquarters.com/v1/accounts/0xfB6916095ca1df60bB79Ce92cE3Ea74c37c5d359/rewards \
  -H 'Authorization: Bearer eyJhbGciOiJIU...vyvQf81plg'
```

Example response:

```json
[
  {
    "rewardAmount": "2000",
    "rewardAmountFormatted": "2,000"
  }
]
```

### Creating new guest accounts

```
POST https://api.dev.pocketfulofquarters.com/v1/new-guest
```

with following headers:

| Parameter       | Description                                                                                            |
| --------------- | ------------------------------------------------------------------------------------------------------ |
| `Authorization` | **Required** `Bearer <SERVER-API-KEY>` <br/> Server API key which you received after app registrations |

Example request:

```CURL
curl https://api.dev.pocketfulofquarters.com/v1/new-guest \
  -H 'Authorization: Bearer eyJhbGciOiJIU...vyvQf81plg'
```

Example response:

```json
[
  {
    "user_id": "123",
    "access_token": "foo...bar",
    "address": "0xfB6916095ca1df60bB79Ce92cE3Ea74c37c5d359"
  }
]
```

### Converting guest accounts

```CURL
GET https://dev.pocketfulofquarters.com/guest?response_type=code&client_id=YOUR_CLIENT_ID&token=GUEST_ACCESS_TOKEN&redirect_uri=YOUR_REDIRECT_URL&inline=true
```

When redirecting a user to Quarters guest signup page, you'll need to construct the URL with the correct parameters. Here's a list of parameters you should always specify:

| Parameter       | Description                                                                              |
| --------------- | ---------------------------------------------------------------------------------------- |
| `token`         | **Required** Guest access token you received after creating the guest user account.      |
| `response_type` | **Required** Value `code`                                                                |
| `client_id`     | **Required** The client ID you received after registering your application.              |
| `inline`        | **Required** Value `true`                                                                |
| `redirect_uri`  | **Optional** The URL (encoded) in your app where users will be sent after authorization. |

Example of a guest signup URL:

```CURL
GET https://dev.pocketfulofquarters.com/guest?response_type=code&client_id=Lpk5sPrA7P59HFlN7obS&token=foobar&inline=true&redirect_uri=https%3A%2F%2Fexample.com%2Foauth%2Fcallback
```

**Code retrieval**

On successful guest signup, Quarters will redirect user to `redirect_uri` with following code as query params:

| Query param | Description                               |
| ----------- | ----------------------------------------- |
| `code`      | Temporary `code` to retrieve access token |

**Note:** After your users link their guest account with their email and password, you need to retrieve a [OAuth Token](./oauth.md) for them using the `code` from `redirect_uri` as the guest tokens gets invalidated.
