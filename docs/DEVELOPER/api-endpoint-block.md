---
title: API Endpoint
slug: api-endpoint-blocks
icon: {"faIcon":"fa-solid fa-plug"}
docTags: Revenge
createdAt: 2026-03-30T10:00:00.000Z
updatedAt: 2026-03-31T10:00:00.000Z
---

:::::ApiMethodV2
```json
{
  "name": "Get Cakes",
  "method": "GET",
  "url": "https://api.cakes.com",
  "description": "Get a cake by its ID",
  "tab": "examples",
  "examples": {
    "languages": [
      {
        "id": "rMPOQU_1kHGiTPpScUYdI",
        "language": "javascript",
        "code": "var myHeaders = new Headers();\nmyHeaders.append(\"Accept\", \"application/json\");\nmyHeaders.append(\"Content-Type\", \"application/json\");\n\nvar raw = JSON.stringify({\n   \"id\": \"string\"\n});\n\nvar requestOptions = {\n   method: 'GET',\n   headers: myHeaders,\n   body: raw,\n   redirect: 'follow'\n};\n\nfetch(\"https://api.cakes.com\", requestOptions)\n   .then(response => response.text())\n   .then(result => console.log(result))\n   .catch(error => console.log('error', error));",
        "customLabel": ""
      }
    ],
    "selectedLanguageId": "rMPOQU_1kHGiTPpScUYdI"
  },
  "results": {
    "languages": [
      {
        "id": "SUm_D6qiRs0Y1D1Y1lZOy",
        "language": "200",
        "code": "{\n  \"name\": \"Cake's name\",\n}",
        "customLabel": ""
      },
      {
        "id": "sckvSeRYTZ93lmHKVedre",
        "language": "404",
        "code": "{\n  \"message\": \"Ain't no cake like that.\"\n}",
        "customLabel": ""
      }
    ],
    "selectedLanguageId": "SUm_D6qiRs0Y1D1Y1lZOy"
  },
  "request": {
    "pathParameters": [],
    "queryParameters": [],
    "headerParameters": [],
    "bodyDataParameters": [
      {
        "name": "id",
        "kind": "required",
        "type": "string",
        "description": "ID of the cake to get"
      }
    ],
    "formDataParameters": []
  },
  "currentNewParameter": {
    "label": "Body Parameter",
    "value": "bodyDataParameters"
  },
  "hasTryItOut": false
}
```
:::::
