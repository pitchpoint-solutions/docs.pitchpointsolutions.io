---
title: Querying
layout: dev_guide
parent: Orders
nav_order: 3
---
Beta
{: .label .label-yellow }
# Orders Query Implementation Guide

## Overview
Your system needs to query your orders the point services platform.

## Step 1: Authenticate and Receive Tokens
See the [Authentication Guide](/developer_guides/authentication/access_token/) for more information on how to authenticate and receive your token.

### Step 2: Send your first request.

This request will list the first 100 orders under your account. When an empty body is supplied, just the first 100 orders are returned.

Incorporate the access token in the `Authorization` header after the `Bearer` type as follows, replacing `your_access_token_here` with the actual token you received in the `access_token` field from the authentication process.

```bash
curl -X POST https://api.pointservices.com/riskinsight-services-ws/resources/v1/orders/query 
-H "Authorization: Bearer your_access_token_here" 
-H "Content-Type: application/json" 
-d ''
```

This is a special case where you want all of your orders to be returned.  No DSL (json) is sent for the request body.

{: .api_ref }
[Order Query API Reference](/api/order/query)

    

#### Example response:  

```json
{
  "count" : 100,
  "limit" : 100,
  "nextMarker" : "5705424597053369344",
  "results" : [ {
    "Completed" : "2024-02-05T20:49:44.000Z",
    "Created" : "2024-02-05T20:49:42.000Z",
    "Deleted" : false,
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

Use the `nextMarker` field from the response in Step 2 for the `marker` field in this request. Set the `limit` field to 100 to limit the number of results to 100. Send a test request as follows:

```bash
curl -X POST https://api.pointservices.com/riskinsight-services-ws/resources/v1/orders/query 
-H "Authorization: Bearer your_access_token_here" 
-H "Content-Type: application/json" 
-d '{
  "marker" : "5705424597053369344",
  "limit" : 100
}'
```

#### Example response:

```json
{
  "count" : 100,
  "limit" : 100,
  "nextMarker" : "6705424597053369344",
  "results" : [ {
    "Completed" : "2024-02-06T20:49:44.000Z",
    "Created" : "2024-02-06T20:49:42.000Z",
    "Deleted" : false,
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

Because `count` matches `limit`, it is possible that there are more results to return.  Continue updating the `marker` field with the results of `nextMarker` and send the request again to get the next batch.

### Step 4: Start a new search; Find orders by Reference Number and limit the number of results to 5

```bash
curl -X POST https://api.pointservices.com/riskinsight-services-ws/resources/v1/orders/query 
-H "Authorization: Bearer your_access_token_here" 
-H "Content-Type: application/json" 
-d '{"filters":{"ReferenceNumber":{"eq":"7058774124"}},"limit":5}'
```

Since no `marker` was specified, it will start a new search.

#### Example response:

```json
{
  "count" : 1,
  "limit" : 5,
  "nextMarker" : "6705424597053369344",
  "results" : [ {
    "Completed" : "2024-02-06T20:49:44.000Z",
    "Created" : "2024-02-06T20:49:42.000Z",
    "Deleted" : false,
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

Even though `nextMarker` is present, because `count` (value of `1` in this example) is less than `limit` (value of `5` in this example), there are no more results to return. 


### Step 5: Find orders greater than a specific date and limit the results to 5

```bash
curl -X POST https://api.pointservices.com/riskinsight-services-ws/resources/v1/orders/query 
-H "Authorization: Bearer your_access_token_here" 
-H "Content-Type: application/json" 
-d '{"filters":{"Completed":{"gt":"2024-02-06T20:49:43.000Z"}},"limit":5}
```

#### Example response:

```json
{
  "count" : 5,
  "limit" : 5,
  "nextMarker" : "6705424597053369344",
  "results" : [ {
    "Completed" : "2024-02-06T20:49:44.000Z",
    "Created" : "2024-02-06T20:49:42.000Z",
    "Deleted" : false,
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

### Step 6: Get the next batch of orders

```bash
curl -X POST https://api.pointservices.com/riskinsight-services-ws/resources/v1/orders/query 
-H "Authorization: Bearer your_access_token_here" 
-H "Content-Type: application/json" 
-d '{"filters":{"Completed":{"gt":"2024-02-06T20:49:43.000Z"}},"limit":5, "marker":"6705424597053369343"}
```

Send the same request again, but this time with the `marker` field set to the value of `nextMarker` from the previous response.

#### Example Response: 
```json
{
  "count" : 5,
  "limit" : 5,
  "marker":"6705424597053369343",
  "nextMarker" : "7705424597053369344",
  "results" : [ {
    "Completed" : "2024-02-06T20:49:44.000Z",
    "Created" : "2024-02-06T20:49:42.000Z",
    "Deleted" : false,
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

See: [Filter DSL](/api/order/query/#filter-data-dsl)    
