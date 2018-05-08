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
