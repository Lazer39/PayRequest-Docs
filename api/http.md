---
description: Simple HTTP api
---

# HTTP Requests

### The API is in BETA, did you encounter any issues! Let us know info\[at\]payrequest.io

## Gettings started

Because of certain security measurements we will always expect a token. This token can be found in your dashboard settings. For this example we'll use a fake token.

## Create a payment request

There is one url which we use to create a payment. The response however depends on the data you put in it. You can redirect your user to the HTTP link and they will finish their payment and safely return to your app/website or other solution. First you create the request.

```php
POST /api/create/
```

| Name | Type | Description |
| :--- | :--- | :--- |
| `token` | `required` | The token you can find in your dashboard settings |
| `id` | `required` | An unique id which you can use to verify the payment |
| `title` | `required` | Payment title that the customer sees |
| `amount` | `required` | The amount you want to charge the customer |
| `description` | `optional` | Payment description |
| `type` | `optional` | When not given, the url will return a payment link. When the value is `direct` the user can \(and should\) be redirected to the specific url. If omitted or `link` is given the url will respond with a generated payment link |
| `response` | `optional` | When not given, the payment will just show the verification page and nothing more. When an url is given the user will be redirect to that url after a payment has been completed or failed |

### Example

```javascript
{
  "token": "spJA4nChfSPDRSkD-Znb6iw-2WGkDR37GyxZ-GZomUo",
  "id": "PR-000012457",
  "title": "Invoice #15123-14",
  "amount": "12,25",
  "type": "direct",
  "response": "https://payrequest.io/responsehandler"
}
```

### Type = link or omitted

The response will be as shown:

```javascript
{
  "status": "success",
  "url": "https://link.payrequest.io/630ep3t"
}
```

### Return response

After the payment has been successfull or failed the url will return the user to the response url when given. The return will have `response` attached which will consist of a `MD5` encryption of `status`+`id`. This can be in our above request `successPR-000012457` or `failedPR-000012457`. To perform an extra check we attached `return_id` which will consist of the `token` + `id` encrypted with `MD5`.

### Possible Errors

Sometimes you can forget some information. We tried our best to make this as easy as possible for you.
An error response will be like this:

```javascript
{
  "status": "error",
  "error": "notoken"
}
```

Possible error codes:

| error | Description |
| :--- | :--- |
| `notoken` | The token is not provided |
| `noid` | No unique id is given |
| `novalidtoken` | No valid token is given |
| `nosuchlink` | When retrieving payment, unique id is not found |
| `notitle` | No payment title is given |
| `noamount` | No amount is given |
| `notvalidamount` | No valid amount is given |
| `nointegration` | No integration is setup, or not finished onboarding with your connected PSP |
| `pspnotsupported` | Connected PSP is not supported yet in our API |
| `unknown` | Unknown error has been generated. Please check `realerror` output to check why |


## Retrieve payment

After the customer is returned to your given url you may want to check if the payment was paid. This can be done with the following POST call.

```php
POST /api/get/
```

| Name | Type | Description |
| :--- | :--- | :--- |
| `token` | `required` | The token you can find in your dashboard settings |
| `id` | `required` | An unique id which you can use to verify the payment |

### Example

```javascript
{
  "token": "spJA4nChfSPDRSkD-Znb6iw-2WGkDR37GyxZ-GZomUo",
  "id": "PR-000012457"
}
```

### Response

The status that will be returned can be `paid`, `failed` or `pending`.

```javascript
{
  "status": "paid"
}
```

