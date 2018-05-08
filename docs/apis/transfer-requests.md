# Transfer request APIs

Quarters allows you to create "transfer requests" and provide a way to ask users for approval. Typical flow for transfer requests:

1. Create a "transfer request". It generates a `requestId`. It requires user `access_token` for given user.
2. User approves or declines the request manually on Quarters user-interface (UI). And quarters server will handle transfers.

### Create transfer request for user

```CURL
POST https://api.pocketfulofquarters/v1/oauth/token
```

with following parameters:

| Parameter     | Description                                                                     |
| ------------- | ------------------------------------------------------------------------------- |
| `tokens`      | **Required** Number of quarters to transfer                                     |
| `description` | **Optional** Transfer description - will be shown on screen while user approval |
| `app_id`      | **Required** The APP ID you received after registering your application         |

with following headers:

| Parameter       | Description                                   |
| --------------- | --------------------------------------------- |
| `Authorization` | **Required** Format - `Bearer <access_token>` |

Request example:

```CURL
curl https://api.pocketfulofquarters/v1/requests \
  -X POST \
  -H 'Authorization: Bearer eyJhbGciOiJIU...vyvQf81plg'
  -H 'Content-Type: application/json;charset=UTF-8' \
  --data-binary '{"tokens": 10,"description":"Power ups","app_id":"Lpk5sPrA7P59HFlN7obS"}'
```

Response example:

```json
{
  "id": "01be8a20-04bb-11e8-94b8-91a2ba7c5972",
  "tokens": 10,
  "appId": "Lpk5sPrA7P59HFlN7obS",
  "appAddress": "0x1e2aa0184dcde9502568593d6c5da68966a3275a",
  "embedCode": "01be8a20-04bb-11e8-94b8-91a2ba7c5972",
  "description": "Power ups",
  "type": "transfer",
  "endpoint": "/v1/requests/01be8a20-04bb-11e8-94b8-91a2ba7c5972",
  "created": {},
  "userId": "Lpk5sPrA7P59HFlN7obS"
}
```

#### Authorization

You can redirect to following URL to get approval from user.

* **URL to Authorize transfer** `https://pocketfulofquarters.com/requests/{request_id}?inline=true`

| URL params   | Description                                                              |
| ------------ | ------------------------------------------------------------------------ |
| `request_id` | **Required** Request id from [step 1](#create-transfer-request-for-user) |


Response fields will be in query params in case of redirection, and `window.onQuartersCallback` in case of iframe.

Response fields:

| Field       | Description                                     |
| ----------- | ----------------------------------------------- |
| `txId`      | Ethereum transaction hash of quarters transfers |
| `requestId` | Transfer request id                             |

### Transfer quarters (Available only for server-to-server)

```
POST https://api.pocketfulofquarters.com/v1/accounts/{app-account-address}/transfer
```

with following url params:

| Url params            | Description                                                                                 |
| --------------------- | ------------------------------------------------------------------------------------------- |
| `app-account-address` | **Required** App account address.<br/>An address which you received after app registrations |

with following headers:

| Parameter       | Description                                                                                            |
| --------------- | ------------------------------------------------------------------------------------------------------ |
| `Authorization` | **Required** `Bearer <SERVER-API-KEY>` <br/> Server API key which you received after app registrations |

with following parameters:

| Parameter           | Description                                          |
| ------------------- | ---------------------------------------------------- |
| `amount`            | **Required** Number of quarters to transfer          |
| `user` or `address` | **Required** User id or user account's valid address |

Request example:

```CURL
curl https://api.pocketfulofquarters/v1/accounts/0x898...89/transfer \
  -X POST \
  -H 'Authorization: Bearer eyJhbGciOiJIU...vyvQf81plg'
  -H 'Content-Type: application/json;charset=UTF-8' \
  --data-binary '{"amount":10,"user":"Lpk5sPrA7P59HFlN7obS"}'
```

Response example:

```json
{
  "txId": "0xd832e7c419869...5918b96"
}
```

This will yield following response:

| Field  | Description                                     |
| ------ | ----------------------------------------------- |
| `txId` | Ethereum transaction hash of quarters transfers |
