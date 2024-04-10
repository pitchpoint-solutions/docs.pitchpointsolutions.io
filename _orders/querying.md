---
title: Querying
layout: default
nav_order: 1
---
Beta
{: .label .label-yellow }

# Orders Query Implementation Guide

## Overview
Your system needs to query your orders the point services platform.

## Obtaining Authentication Tokens

Before you can test your query, you must obtain an access token for the pointservices platform.

### Step 1: Authenticate and Receive Tokens

Use the following command to authenticate with your credentials and receive your token. Replace `customerUsername` and `customerPassword` with your actual username and password.

```bash
curl -X POST https://api.pointservices.com/user-management-services-ws/oauth2/002/signInWithPassword \
-H "Content-Type: application/json" \
-d '{
  "username": "customerUsername",
  "password": "customerPassword"
}'
```

The response will be a json structure that includes:
- `access_token`: Used for authenticated requests to the webhook testing endpoint.
- `refresh_token`: Used to obtain a new access token once the current one expires.
- `expires_in`: The time in seconds until the access token expires.

Read the response to obtain the new `access_token`, `expires_in` and `refresh_token` values. The `expires_in` value will in most cases be 3600 seconds (1 hour) from the time the new access token was issued.

Example response:

```json
{
  "access_token": "eyJraWQiOiIxMjM0NSIsInR5cCI6IkpXVCIsImFsZyI6IlJTMjU2In0.eyJhdWQiOiJ0ZXN0LXRlbmFudCIsInN1YiI6InhwcHN8MTIyMyIsImVtYWlsX3ZlcmlmaWVkIjp0cnVlLCJ1c2VyX2lkIjoiMTIyMyIsImF1dGhfdGltZSI6MTcwODcxNTYwMiwiaXNzIjoiaHR0cHM6XC9cL3NlY3VyZXRva2VuLmdvb2dsZS5jb21cL3Rlc3QtdGVuYW50IiwiZXhwIjoxNzA4NzE5LCJpYXQiOjE3MDg3MTUsImVtYWlsIjoidGVzdHVzZXJAdGVuYW50LmNvbSJ9.dGVzdA==",
  "refresh_token": "AMf-vBxITbnfBBMJpPXMvop1qa1cMaUuqAKX_y1hYsmcgVtzhx7Al_9mWD",
  "expires_in": 3600
}
```
### Step 2: Send a request to list the first 100 orders under your account

Incorporate the access token in the `Authorization` header and send a test request as follows:

```bash
curl -X POST https://api.pointservices.com/riskinsight-services-ws/resources/v1/orders/query 
-H "Authorization: Bearer your_access_token_here" 
-H "Content-Type: application/json" 
-d ''
```

Replace `your_access_token_here` with the actual token you received in the `access_token` field from the authentication process.

Example Response

```json
{
  "count" : 100,
  "limit" : 100,
  "nextMarker" : "5705424597053369344",
  "results" : [ {
    "Completed" : "2024-02-05T20:49:44.000Z",
    "Created" : "2024-02-05T20:49:42.000Z",
    "Deleted" : false,
    "DossierGuid" : "9733914918566161680",
    "Fulfilled" : "2024-02-05T20:49:42.000Z",
    "OrderGuid" : "2992759493555823372",
    "OrderSummary" : "Cyril Figgins",
    "OwnerCompanyName" : "Bloggins Bank",
    "OwnerUserContactMechanism" : "joe@blogginsbank.com",
    "OwnerUserDisplayName" : "Bloggins, Joe",
    "ProductDisplayName" : "ADV-AB-BeforeReconfirm",
    "ProductType" : "ADV",
    "ReferenceNumber" : "7058934124",
    "ReportAvailable" : true,
    "Status" : "complete"
  }, ... ]
}
```

### Step 3: Send a request to list the next 100 orders under your account

Use the ```nextMarker``` field from the response in Step 2 for the ```marker``` field in this request. Set the ```limit``` field to 100 to limit the number of results to 100. Send a test request as follows:

```bash
curl -X POST https://api.pointservices.com/riskinsight-services-ws/resources/v1/orders/query 
-H "Authorization: Bearer your_access_token_here" 
-H "Content-Type: application/json" 
-d '{
  "marker" : "5705424597053369344",
  "limit" : 100
}'
```

Example Response

```json
{
  "count" : 100,
  "limit" : 100,
  "nextMarker" : "6705424597053369344",
  "results" : [ {
    "Completed" : "2024-02-06T20:49:44.000Z",
    "Created" : "2024-02-06T20:49:42.000Z",
    "Deleted" : false,
    "DossierGuid" : "97339714918566161680",
    "Fulfilled" : "2024-02-06T20:49:42.000Z",
    "OrderGuid" : "2122759493555823372",
    "OrderSummary" : "Cyril Figgins 2",
    "OwnerCompanyName" : "Bloggins Bank",
    "OwnerUserContactMechanism" : "joe@blogginsbank.com",
    "OwnerUserDisplayName" : "Bloggins, Joe",
    "ProductDisplayName" : "ADV-AB-BeforeReconfirm",
    "ProductType" : "ADV",
    "ReferenceNumber" : "7058774124",
    "ReportAvailable" : true,
    "Status" : "complete"
  }, ... ]
}
```

### Step 4: Find orders by Reference Number and limit the number of results to 5

```bash
curl -X POST https://api.pointservices.com/riskinsight-services-ws/resources/v1/orders/query 
-H "Authorization: Bearer your_access_token_here" 
-H "Content-Type: application/json" 
-d '{"filters":{"ReferenceNumber":{"eq":"7058774124"}},"limit":5}'
```

Example Response

```json
{
  "count" : 1,
  "limit" : 5,
  "nextMarker" : "6705424597053369344",
  "results" : [ {
    "Completed" : "2024-02-06T20:49:44.000Z",
    "Created" : "2024-02-06T20:49:42.000Z",
    "Deleted" : false,
    "DossierGuid" : "97339714918566161680",
    "Fulfilled" : "2024-02-06T20:49:42.000Z",
    "OrderGuid" : "2122759493555823372",
    "OrderSummary" : "Cyril Figgins 2",
    "OwnerCompanyName" : "Bloggins Bank",
    "OwnerUserContactMechanism" : "joe@blogginsbank.com",
    "OwnerUserDisplayName" : "Bloggins, Joe",
    "ProductDisplayName" : "ADV-AB-BeforeReconfirm",
    "ProductType" : "ADV",
    "ReferenceNumber" : "7058774124",
    "ReportAvailable" : true,
    "Status" : "complete"
  }]
}
```

### Step 5: Find orders greater than a specific date after the maker and limit the results to 5

```bash
curl -X POST https://api.pointservices.com/riskinsight-services-ws/resources/v1/orders/query 
-H "Authorization: Bearer your_access_token_here" 
-H "Content-Type: application/json" 
-d '{"filters":{"Completed":{"gt":"2024-02-06T20:49:43.000Z"}},"limit":5, "marker":"6705424597053369343"}
```

Example Response

```json
{
  "count" : 1,
  "limit" : 5,
  "nextMarker" : "6705424597053369344",
  "results" : [ {
    "Completed" : "2024-02-06T20:49:44.000Z",
    "Created" : "2024-02-06T20:49:42.000Z",
    "Deleted" : false,
    "DossierGuid" : "97339714918566161680",
    "Fulfilled" : "2024-02-06T20:49:42.000Z",
    "OrderGuid" : "2122759493555823372",
    "OrderSummary" : "Cyril Figgins 2",
    "OwnerCompanyName" : "Bloggins Bank",
    "OwnerUserContactMechanism" : "joe@blogginsbank.com",
    "OwnerUserDisplayName" : "Bloggins, Joe",
    "ProductDisplayName" : "ADV-AB-BeforeReconfirm",
    "ProductType" : "ADV",
    "ReferenceNumber" : "7058774124",
    "ReportAvailable" : true,
    "Status" : "complete"
  }]
}
```

## Learning the query DSL

### Query DSL Comparison Operators
Filter expressions support eq, ne, in, gt, lt, ge, le, startsWith and startsWithIgnoreCase

```eq``` represents equals
```ne``` represents not equals
```in``` represents a list of values that a property can be
```gt``` represents that a property should be greater than the supplied value
```lt``` represents that a property should be less than the supplied value
```ge``` represents that a property should be greater or equal to the supplied value
```le``` represents that a property should be less than or equal to the supplied value
```startsWith``` represents that a property should begin with the supplied value
```startsWithIgnoreCase``` represents that a property should begin with the supplied value and case is not important

### Query DSL Supported Properties
```Completed```: Date and Time the order was completed. Supports comparison operators ```eq, ne, in, gt, ge, lt, le, in```
```Created```: Date and Time the order was created. Supports comparison operators ```eq, ne, in, gt, ge, lt, le, in```
```Deleted```: Boolean value indicating if the order was deleted. Supports comparison operators ```eq, ne, in, gt, ge, lt, le, in```
```DossierGuid```: Unique identifier for the orders container. Supports comparison operators ```eq, ne, in, gt, ge, lt, le, in, startsWith, startsWithIgnoreCase```
```Fulfilled```: Date and Time the order was fulfilled. Supports comparison operators ```eq, ne, in, gt, ge, lt, le, in, startsWith, startsWithIgnoreCase```
```OrderGuid```: Unique identifier for the order. Supports comparison operators ```eq, ne, in, gt, ge, lt, le, in, startsWith, startsWithIgnoreCase```
```OrderSummary```: Summary of the order. Supports comparison operators ```eq, ne, in, gt, ge, lt, le, in, startsWith, startsWithIgnoreCase```
```OwnerCompanyName```: Company name of the order owner. Supports comparison operators ```eq, ne, in, gt, ge, lt, le, in, startsWith, startsWithIgnoreCase```
```OwnerUserContactMechanism```: Contact mechanism of the order owner. Supports comparison operators ```eq, ne, in, gt, ge, lt, le, in, startsWith, startsWithIgnoreCase```
```OwnerUserDisplayName```: Display name of the order owner. Supports comparison operators ```eq, ne, in, gt, ge, lt, le, in, startsWith, startsWithIgnoreCase```
```ProductDisplayName```: Display name of the product that was ordered. Supports comparison operators ```eq, ne, in, gt, ge, lt, le, in, startsWith, startsWithIgnoreCase```
```ProductType```: Type of the product that was ordered. Supports comparison operators ```eq, ne, in, gt, ge, lt, le, in, startsWith, startsWithIgnoreCase```
```ReferenceNumber```: Reference number of the order. Supports comparison operators ```eq, ne, in, gt, ge, lt, le, in, startsWith, startsWithIgnoreCase```
```ReportAvailable```: Boolean value indicating if the report is available. Supports comparison operators ```eq, ne, in, gt, ge, lt, le, in, startsWith, startsWithIgnoreCase```
```Status```: Status of the order. Supports comparison operators ```eq, ne, in, gt, ge, lt, le, in, startsWith, startsWithIgnoreCase```

### Supported Date and Time formats
```yyyy-MM-dd'T'HH:mm:ss.SSS'Z'```
```yyyy-MM-dd'T'HH:mm:ssZZ```
```EEE, dd MMM yyyy HH:mm:ss Z```

It's important to supply the timezone since the api will use the timezone to compare the date and time of the query.

### Example Queries
```json
{"filters":{"ProductType":{"eq":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"ProductType":{"ge":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"ProductType":{"gt":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"ProductType":{"in":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"ProductType":{"le":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"ProductType":{"lt":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"ProductType":{"ne":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"ProductType":{"startsWith":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"ProductType":{"startsWithIgnoreCase":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"Status":{"eq":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"Status":{"ge":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"Status":{"gt":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"Status":{"in":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"Status":{"le":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"Status":{"lt":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"Status":{"ne":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"Status":{"startsWith":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"Status":{"startsWithIgnoreCase":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"ReferenceNumber":{"eq":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"ReferenceNumber":{"ge":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"ReferenceNumber":{"gt":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"ReferenceNumber":{"in":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"ReferenceNumber":{"le":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"ReferenceNumber":{"lt":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"ReferenceNumber":{"ne":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"ReferenceNumber":{"startsWith":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"ReferenceNumber":{"startsWithIgnoreCase":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OrderSummary":{"eq":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OrderSummary":{"ge":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OrderSummary":{"gt":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OrderSummary":{"in":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OrderSummary":{"le":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OrderSummary":{"lt":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OrderSummary":{"ne":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OrderSummary":{"startsWith":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OrderSummary":{"startsWithIgnoreCase":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OrderGuid":{"eq":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OrderGuid":{"ge":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OrderGuid":{"gt":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OrderGuid":{"in":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OrderGuid":{"le":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OrderGuid":{"lt":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OrderGuid":{"ne":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OrderGuid":{"startsWith":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OrderGuid":{"startsWithIgnoreCase":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"DossierGuid":{"eq":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"DossierGuid":{"ge":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"DossierGuid":{"gt":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"DossierGuid":{"in":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"DossierGuid":{"le":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"DossierGuid":{"lt":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"DossierGuid":{"ne":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"DossierGuid":{"startsWith":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"DossierGuid":{"startsWithIgnoreCase":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OwnerUserDisplayName":{"eq":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OwnerUserDisplayName":{"ge":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OwnerUserDisplayName":{"gt":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OwnerUserDisplayName":{"in":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OwnerUserDisplayName":{"le":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OwnerUserDisplayName":{"lt":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OwnerUserDisplayName":{"ne":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OwnerUserDisplayName":{"startsWith":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OwnerUserDisplayName":{"startsWithIgnoreCase":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OwnerUserContactMechanism":{"eq":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OwnerUserContactMechanism":{"ge":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OwnerUserContactMechanism":{"gt":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OwnerUserContactMechanism":{"in":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OwnerUserContactMechanism":{"le":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OwnerUserContactMechanism":{"lt":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OwnerUserContactMechanism":{"ne":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OwnerUserContactMechanism":{"startsWith":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OwnerUserContactMechanism":{"startsWithIgnoreCase":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OwnerCompanyName":{"eq":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OwnerCompanyName":{"ge":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OwnerCompanyName":{"gt":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OwnerCompanyName":{"in":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OwnerCompanyName":{"le":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OwnerCompanyName":{"lt":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OwnerCompanyName":{"ne":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OwnerCompanyName":{"startsWith":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"OwnerCompanyName":{"startsWithIgnoreCase":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"ProductDisplayName":{"eq":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"ProductDisplayName":{"ge":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"ProductDisplayName":{"gt":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"ProductDisplayName":{"in":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"ProductDisplayName":{"le":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"ProductDisplayName":{"lt":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"ProductDisplayName":{"ne":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"ProductDisplayName":{"startsWith":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"ProductDisplayName":{"startsWithIgnoreCase":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"Created":{"eq":"2024-04-09T18:41:20.000Z"}},"limit":5,"marker":"aaa"}

{"filters":{"Created":{"ge":"2024-04-09T18:41:20.000Z"}},"limit":5,"marker":"aaa"}

{"filters":{"Created":{"gt":"2024-04-09T18:41:20.000Z"}},"limit":5,"marker":"aaa"}

{"filters":{"Created":{"in":"2024-04-09T18:41:20.000Z"}},"limit":5,"marker":"aaa"}

{"filters":{"Created":{"le":"2024-04-09T18:41:20.000Z"}},"limit":5,"marker":"aaa"}

{"filters":{"Created":{"lt":"2024-04-09T18:41:20.000Z"}},"limit":5,"marker":"aaa"}

{"filters":{"Created":{"ne":"2024-04-09T18:41:20.000Z"}},"limit":5,"marker":"aaa"}

{"filters":{"Fulfilled":{"eq":"2024-04-09T18:41:20.000Z"}},"limit":5,"marker":"aaa"}

{"filters":{"Fulfilled":{"ge":"2024-04-09T18:41:20.000Z"}},"limit":5,"marker":"aaa"}

{"filters":{"Fulfilled":{"gt":"2024-04-09T18:41:20.000Z"}},"limit":5,"marker":"aaa"}

{"filters":{"Fulfilled":{"in":"2024-04-09T18:41:20.000Z"}},"limit":5,"marker":"aaa"}

{"filters":{"Fulfilled":{"le":"2024-04-09T18:41:20.000Z"}},"limit":5,"marker":"aaa"}

{"filters":{"Fulfilled":{"lt":"2024-04-09T18:41:20.000Z"}},"limit":5,"marker":"aaa"}

{"filters":{"Fulfilled":{"ne":"2024-04-09T18:41:20.000Z"}},"limit":5,"marker":"aaa"}

{"filters":{"Completed":{"eq":"2024-04-09T18:41:20.000Z"}},"limit":5,"marker":"aaa"}

{"filters":{"Completed":{"ge":"2024-04-09T18:41:20.000Z"}},"limit":5,"marker":"aaa"}

{"filters":{"Completed":{"gt":"2024-04-09T18:41:20.000Z"}},"limit":5,"marker":"aaa"}

{"filters":{"Completed":{"in":"2024-04-09T18:41:20.000Z"}},"limit":5,"marker":"aaa"}

{"filters":{"Completed":{"le":"2024-04-09T18:41:20.000Z"}},"limit":5,"marker":"aaa"}

{"filters":{"Completed":{"lt":"2024-04-09T18:41:20.000Z"}},"limit":5,"marker":"aaa"}

{"filters":{"Completed":{"ne":"2024-04-09T18:41:20.000Z"}},"limit":5,"marker":"aaa"}

{"filters":{"SourceAliases":{"eq":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"SourceAliases":{"ge":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"SourceAliases":{"gt":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"SourceAliases":{"in":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"SourceAliases":{"le":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"SourceAliases":{"lt":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"SourceAliases":{"ne":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"SourceAliases":{"startsWith":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"SourceAliases":{"startsWithIgnoreCase":"value"}},"limit":5,"marker":"aaa"}

{"filters":{"ReportAvailable":{"eq":"true"}},"limit":5,"marker":"aaa"}

{"filters":{"ReportAvailable":{"in":"true"}},"limit":5,"marker":"aaa"}

{"filters":{"ReportAvailable":{"ne":"true"}},"limit":5,"marker":"aaa"}

{"filters":{"Deleted":{"eq":"true"}},"limit":5,"marker":"aaa"}

{"filters":{"Deleted":{"in":"true"}},"limit":5,"marker":"aaa"}

{"filters":{"Deleted":{"ne":"true"}},"limit":5,"marker":"aaa"}

// this is a between two dates query that's inclusive
{"filters":{"Created":{"ge":"2024-04-09T18:42:22.705Z","le":"2024-04-10T18:42:22.712Z"}},"limit":5,"marker":"aaa"}

// this is a between two dates query is is not inclusive
{"filters":{"Created":{"gt":"2024-04-09T18:43:04.745Z","lt":"2024-04-10T18:43:04.752Z"}},"limit":5,"marker":"aaa"}
```
