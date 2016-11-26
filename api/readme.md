Developer API
================

Welcome to the Snowflake API.

All successful API request to the Snowflake API will return a status code in the 200 range, unsuccessful requests will return an [error status code](#developer-api-errors).

Authentication
--------------

Almost all calls to the Snowflake API require you to authenticate yourself to ensure no unauthorised requests are being made. We use API keys to allow access, you can create a new API keys from the Snowflake UI.
```
curl "https://api.snowflake.ai" \
-H "Content-Type: application/json"
-H "Authorization: Bearer {{your api key}}"
```

Messages
--------

The `/messages` endpoint is used to trigger the sending of individual messages and also to retrieve the status of previously sent messages.

### Send a new message


```
curl "https://api.snowflake.ai/messages" \
-H "Content-Type: application/json" \
-H "Authorization: Bearer {{your api key}}" \
-X POST \
-d '{
  "communicationId": "{{communication id}}",
  "environment": "draft",
  "data": {
    "channel": "sms",
    "foo": "Bar",
    "agent": {
      "phoneNumber": "+4712345678",
      "emailAddress": "sender@test.com",
      "name": "Sammy Sender"
    },
    "customer": {
      "phoneNumber": "+4787654321",
      "emailAddress": "recipient@test.com",
      "name": "Roger Recipient"
    }
  }
}'
```
The command returns JSON structured like this:

```
{
  "id": "{{communication id}}",
  "href":"/api/messages/{{communication id}}"
}
```

This endpoint sends a new message via the appropriate channel.

The example assumes that the templates are set up to use the agents email / phone number as sender email / phone number and the customers email / phone number as the recipient email / phone numbers.

The data in the message needs to match the schema used for the given communication.

### HTTP Request

POST https://api.snowflake.ai/messages

#### Parameters

Parameter | Description
----------|------------
communicationId | The ID of the communication you want to send
environment | The environment to pick a template from. Either ‘draft’ or 'release’ at this point.
data | The data you want to use to send the message

### Get a Specific Message

This endpoint retrieves a specific message.

```
curl "https://api.snowflake.ai/messages/{{message id}}" \
-H "Content-Type: application/json" \
-H "Authorization: Bearer {{your api key}}"
```
The above command returns JSON structured like this:

```
{
  "id": "{{message id}}",
  "status": "sent",
  "opens": 2,
  "clicks": 1
}
```


### HTTP Request

GET https://api.snowflake.ai/messages/<ID>

##### URL Parameters

Parameter | Description
----------|------------
ID | The ID of the message to retrieve

### List messages

```
curl "https://api.snowflake.ai/messages/" \
-H "Authorization: Bearer {{your api key}}"
```

The above command returns JSON structured like this:

```
{
  "smsMessages":
  [
    {
      "to": "+4746748688",
      "status": "sent",
      "sentAt": "2015-11-19T17:49:15Z",
      "id": "004e49a0-d1e4-4a9d-a58b-308629868a50",
      "href": "/api/messages/004e49a0-d1e4-4a9d-a58b-308629868a50",
      "from": "+4712345678"
    }
  ],
  "emailMessages":
  [
    {
      "to": "foo@test.com",
      "status": "sent",
      "sentAt": "2015-11-07T08:59:37Z",
      "id": "674ae3bcad8d489d948055e6581ac590",
      "href": "/api/messages/674ae3bcad8d489d948055e6581ac590",
      "from": "bar@test.com"
    }
  ]
}
```

This endpoint lists messages sent from your account.

### HTTP Request

GET https://api.snowflake.ai/messages/






Errors
------

The Snowflake API will return normal HTTP error codes when something goes wrong. Depending on the error code you may want to just retry the same command.

Error|Code|Meaning
-----|----|-------
400 | Bad Request | Your request is invalid
401 | Unauthorized | Your API key is wrong
403 | Forbidden | You do not have access to the current resource
404 | Not Found | The specified resource could not be found
405 | Method Not Allowed | You tried to access a resource with an invalid method
429 | Too Many Requests | You are exceeding our rate limits.
500 | Internal Server Error | We had a problem with our server. Please try again later.
503 | Service Unavailable | We’re temporarially offline for maintanance. Please try again later.