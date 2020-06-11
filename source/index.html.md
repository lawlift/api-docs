---
title: Lawlift API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - bash
  - javascript
  - python
  - php

# toc_footers:
# - <a href='#'>Sign Up for a Developer Key</a>

search: true
---

# Introduction

Welcome to the Lawlift API! You can use our API to access Lawlift API endpoints to export data from your system into Lawlift.

# Authentication

> To authorize get the API auth key from the Lawlift app.

> Make sure to replace `API_KEY` with your API key.

Lawlift uses API keys to allow access to the API.

Lawlift expects the API key to be included in all API requests to the server
in the request header in the following form:

`Authorization: Basic API_KEY`

<aside class="notice">
You must replace <code>API_KEY</code> with your personal API key.
</aside>

The API Auth key can be retrieved from the Lawlift app.
Open a Template in editing mode, choose Options and enable the 'API Mapping'.
The API key will be displayed unencrypted below.

NOTE: The API auth key will be in the form:
`<ApiClientId>@<ApiSecretKey>`

# Documents

## Generate a document in the Lawlift app with imported data

```bash
curl -H "Authorization: Basic <ApiKey>" \
 -H "Content-Type: application/json" \
 -X POST --data '{"templateId":"<templateId>","data":{"question1yes":true, "client": "Max"}}' \
 https://app.lawlift.de/api/v1/documents/generate
```

```javascript
// NodeJS (server side)
var request = require("request");

var headers = {
  Authorization: "Basic <ApiKey>",
  "Content-Type": "application/json",
};

var dataString =
  '{"templateId":"<templateId>","data":{"question1yes":true, "client": "Max"}}';

var options = {
  url: "https://app.lawlift.de/api/v1/documents/generate",
  method: "POST",
  headers: headers,
  body: dataString,
};

function callback(error, response, body) {
  if (!error && response.statusCode == 200) {
    console.log(body);
  }
}

request(options, callback);
```

```python
import requests

headers = {
    'Authorization': 'Basic <ApiKey>',
    'Content-Type': 'application/json',
}

data = '{"templateId":"<templateId>","data":{"question1yes":true, "client": "Max"}}'

response = requests.post('https://app.lawlift.de/api/v1/documents/generate', headers=headers, data=data)

```

```php
<?php
include('vendor/rmccue/requests/library/Requests.php');
Requests::register_autoloader();
$headers = array(
    'Authorization' => 'Basic <ApiKey>',
    'Content-Type' => 'application/json'
);
$data = '{"templateId":<templateId>,"data":{"question1yes":true}}';
$response = Requests::post('https://app.lawlift.de/api/v1/documents/generate', $headers, $data);

```

This endpoint generates a new document with the provided data pre-filled and returns a url
to the new document in Lawlift app.

### Prerequisites

To populate imported data into an newly created document based on an existing templates a manual
mapping of fields has to be provided for the template.

Enable the API Mapping in Lawlift and provide mapping fields for all the answers of a template.

- Login to Lawlift
- Go to 'Templates'
- 'Edit' the template you want to map
- Open 'Options'
- At the bottom enable 'Api Mapping' and copy the API auth key.
- At the left hand sidebar enter field names for all the answers you want to map to your export.

The mapping connects fields from the source system 1 to 1 with an answer of a document.

> The import data payload should look like this:

```json
{
  "answer1-yes": true,
  "answer1-no": false, //Can be omitted if it is `false`
  "answer2": "First Name",
  "answer3": "Last Name",
  "serialAnswer1_Name": ["Client 1", "Client 2", "Client 3"],
  "serialAnswer1_Address": ["Adresse 1", "Adresse 2", "Adresse 3"]
}
```

### HTTP Request

`POST https://app.lawlift.de/api/v1/documents/generate`

### Query Parameters

| Parameter              | Type   | Description                                                                                             |
| ---------------------- | ------ | ------------------------------------------------------------------------------------------------------- |
| templateId             | String | The Lawlift Id of the template from which a document should be generated.                               |
| data                   | JSON   | The data object with the mapped fields, can be serialized JSON or encrypted with `tweet-nacl` (Base64). |
| authorEmail (optional) | String | Email of the author of the document.                                                                    |
| clientName (optional)  | String | The name of the client.                                                                                 |
| caseName (optional)    | String | The name of the case.                                                                                   |
| meta (optional)        | Object | Custom meta data fields that will be exported into docx (settings.xml). Useful to insert data from your |
|                        |        | system and extract it from exported docx files.                                                         |
| nonce (optional)       | String | Only required if data payload is encrypted.                                                             |

# Templates

## Get list of templates

```bash
curl "https://app.lawlift.de/api/v1/templates"
  -H "Authorization: Basic <api_key>"
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "name": "Template name"
  },
  {
    "id": 2,
    "name": "Another template name"
  }
]
```

Get data of all available templates. Returns a list of template Ids and their respective names

### HTTP Request

`GET https://app.lawlift.de/api/v1/templates`

### Query Parameters

none

## Get data for a specific template

```bash
curl "https://app.lawlift.de/api/v1/template/:templateId"
  -H "Authorization: Basic <api_key>"
```

> The above command returns JSON structured like this:

```json
{
  "apiFields": [
    "fieldName_1",
    "fieldName_2",
    "fieldName_3",
    "fieldName_4",
    "fieldName_5",
    "fieldName_6"
  ]
}
```

Retrieve available fields for a template.

### HTTP Request

`GET https://app.lawlift.de/api/v1/template/:templateId`

# Errors

The Lawlift API uses the following error codes:

| Error Code | Meaning                                                                                   |
| ---------- | ----------------------------------------------------------------------------------------- |
| 400        | Bad Request -- Your request is invalid.                                                   |
| 401        | Unauthorized -- Your API key is wrong.                                                    |
| 403        | Forbidden -- The resource requested is hidden for administrators only.                    |
| 404        | Not Found -- The specified resource could not be found.                                   |
| 405        | Method Not Allowed -- You tried to access a resource with an invalid method.              |
| 406        | Not Acceptable -- You requested a format that isn't json.                                 |
| 410        | Gone -- The resource requested has been removed from our servers.                         |
| 429        | Too Many Requests -- You're requesting too many resources! Slow down!                     |
| 500        | Internal Server Error -- We had a problem with our server. Try again later.               |
| 503        | Service Unavailable -- We're temporarily offline for maintenance. Please try again later. |
