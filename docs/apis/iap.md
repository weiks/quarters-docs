# IAP APIs - In App Purchases Receipt Verification

Quarters IAP APIs allows for a very simple way for developers to integrate In App Purchases Receipt Validation for Apple, Google and Unity. Quarters are automatically transferred to associated users with the receipt if the IAP receipts are valid.

---

**Supported platforms**: Apple, Google, Unity (*currently only available on Beta API*).

---

The first thing you'll need to do is sign up for
[Quarters](https://dev.pocketfulofquarters.com) to
[create an app](../guides/create-app.md).

### Unity

```
POST https://api.dev.pocketfulofquarters.com/v1/apps/{appId}/verifyReceipt/unity
```

with following url params:

| Url params            | Description                                                                                 |
| --------------------- | ------------------------------------------------------------------------------------------- |
| `appId` | **Required** App id.|

with following headers:

| Parameter       | Description                                                                                            |
| --------------- | ------------------------------------------------------------------------------------------------------ |
| `Authorization` | **Required** `Bearer <SERVER-API-KEY>` <br/> Server API key which you received after app registrations |

with following parameters:

| Parameter           | Description                                          |
| ------------------- | ---------------------------------------------------- |
| `receipt`           | **Required** IAP Receipt to be validated             |
| `user`              | **Required** Buyer id                                |

Request example:

```CURL
curl https://api.dev.pocketfulofquarters.com/v1/apps/{appId}/verifyReceipt/unity \
  -X POST \
  -H 'Authorization: Bearer foo...bar' \
  -H 'Content-Type: application/json;charset=UTF-8' \
  --data-binary '{"receipt": {...}, "user": "<buyerId>"}'
```

Response example:

```json
{
  "txId": "0xdf00...0f0f"
}
```

This will yield following response:

| Field  | Description                                     |
| ------ | ----------------------------------------------- |
| `txId` | Ethereum transaction hash of quarters transfers |
