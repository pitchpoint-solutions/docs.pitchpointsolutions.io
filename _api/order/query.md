---
title: Query Orders
layout: api_ref
nav_order: 2
parent: Order
datatable: true
---
Beta
{: .label .label-yellow }

## Query

Query your orders based on the provided criteria.

```bash
curl -X POST https://api.pointservices.com/riskinsight-services-ws/resources/v1/orders/query 
-H "Authorization: Bearer your_access_token_here" 
-H "Content-Type: application/json" 
-d '{
  "limit": 100,
  "marker": "",
  "filters": {
    "Created": {"ge":"2024-04-09T18:42:22.705Z","le":"2024-04-10T18:42:22.712Z"}
  }
}'
```

#### Header Properties
<div class="datatable-begin"></div>

| Property     | Value            | Required? |
|--------------|------------------|-----------|
| Content-Type | application/json | false     |
| Accept       | application/json | false     |

<div class="datatable-end"></div>


#### Request Data Properties
<div class="datatable-begin"></div>

| Property | Description                                                                                                                                        | Type   | Default |
|----------|----------------------------------------------------------------------------------------------------------------------------------------------------|--------|---------|
| limit    | (optional) Max number of results to return in the response.  Limit will be restricted to 100 if the value given is larger than 100 or less than 1. | number | 100     |
| marker   | (optional) From where to start returning the next sequence of searches.                                                                            | string |         |
| filters  | (optional) Filters to apply to the query. See below for the DSL.                                                                                   | object |         |

<div class="datatable-end"></div>

#### Filter Data DSL
### Query DSL Comparison Operators
Filter expressions support `eq, ne, in, gt, lt, ge, le, startsWith and startsWithIgnoreCase`

- `eq` represents equals
- `ne` represents not equals
- `in` represents a list of values that a property can be
- `gt` represents that a property should be greater than the supplied value
- `lt` represents that a property should be less than the supplied value
- `ge` represents that a property should be greater or equal to the supplied value
- `le` represents that a property should be less than or equal to the supplied value
- `startsWith` represents that a property should begin with the supplied value
- `startsWithIgnoreCase` represents that a property should begin with the supplied value and case is not important

### Query DSL Supported Properties
- `Completed`: Date and Time the order was completed. Supports comparison operators `eq, ne, in, gt, ge, lt, le, in`
- `Created`: Date and Time the order was created. Supports comparison operators `eq, ne, in, gt, ge, lt, le, in`
- `Fulfilled`: Date and Time the order was fulfilled. Supports comparison operators `eq, ne, in, gt, ge, lt, le, in, startsWith, startsWithIgnoreCase`
- `OrderGuid`: Unique identifier for the order. Supports comparison operators `eq, ne, in, gt, ge, lt, le, in, startsWith, startsWithIgnoreCase`
- `OrderSummary`: Summary of the order. Supports comparison operators `eq, ne, in, gt, ge, lt, le, in, startsWith, startsWithIgnoreCase`
- `OwnerCompanyName`: Company name of the order owner. Supports comparison operators `eq, ne, in, gt, ge, lt, le, in, startsWith, startsWithIgnoreCase`
- `OwnerUserContactMechanism`: Contact mechanism of the order owner. Supports comparison operators `eq, ne, in, gt, ge, lt, le, in, startsWith, startsWithIgnoreCase`
- `OwnerUserDisplayName`: Display name of the order owner. Supports comparison operators `eq, ne, in, gt, ge, lt, le, in, startsWith, startsWithIgnoreCase`
- `ProductDisplayName`: Display name of the product that was ordered. Supports comparison operators `eq, ne, in, gt, ge, lt, le, in, startsWith, startsWithIgnoreCase`
- `ProductType`: Type of the product that was ordered. Supports comparison operators `eq, ne, in, gt, ge, lt, le, in, startsWith, startsWithIgnoreCase`
- `ReferenceNumber`: Reference number of the order. Supports comparison operators `eq, ne, in, gt, ge, lt, le, in, startsWith, startsWithIgnoreCase`
- `ReportAvailable`: Boolean value indicating if the report is available. Supports comparison operators `eq, ne, in, gt, ge, lt, le, in, startsWith, startsWithIgnoreCase`
- `Status`: Status of the order. Supports comparison operators `eq, ne, in, gt, ge, lt, le, in, startsWith, startsWithIgnoreCase`

### Supported Date and Time formats
- `yyyy-MM-dd'T'HH:mm:ss.SSS'Z'`  e.g. `2022-04-11T10:30:00.000Z` 
- `yyyy-MM-dd'T'HH:mm:ssZZ` e.g. `2022-04-11T14:30:00+0200`
- `EEE, dd MMM yyyy HH:mm:ss Z` e.g. `Mon, 11 Apr 2022 10:30:00 -0400`

It's important to supply the timezone since the api will use the timezone to compare the date and time of the query.


| Code | Description                                                  |
|------|--------------------------------------------------------------|
| yyyy | four digit year, e.g. 2024                                   | 
| MM | two digit month, e.g. 04                                     | 
| dd | two digit day, e.g. 09                                       | 
| 'T' | seperator between the date and time.  Should always be [ T ] |
| HH | two digit hour in 24-time format, e.g. 14 (for 2pm)          | 
| mm | two digit minute, e.g. 04                                    |
| ss | two digit second, e.g. 05                                    |
| SSS | three digit nano seconds, e.g. 000                           |
| 'Z' | exactly UTC (Zulu) time zone.  Should always be [ Z ]       |
| Z or ZZ | UTC offset, e.g -0400                                        |
| EEE | three char code for the day name of the week, e.g. Mon       |


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

```

The following is an example of a search between two dates query that's inclusive

```json
{"filters":{"Created":{"ge":"2024-04-09T18:42:22.705Z","le":"2024-04-10T18:42:22.712Z"}},"limit":5,"marker":"aaa"}
```

The following is an example of a search between two dates query that's exclusive

```json
{"filters":{"Created":{"gt":"2024-04-09T18:42:22.705Z","lt":"2024-04-10T18:42:22.712Z"}},"limit":5,"marker":"aaa"}
```


### Responses

200
{: .label .label-green }

<div class="datatable-begin"></div>

| Property   | Description                                                                                                                                                                                                                                                                            | Type   | Default |
|------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------|-----|
| count      | Total number of results returned in this request.  If the `count` is less than `limit` there are no more results to return                                                                                                                                                             | number | 100 |
| limit      | Echoed from the request or 100.  If `count` is less than `limit`, then there are no more results to return                                                                                                                                                                             | number | 100 |
| nextMarker | The marker to use in the next request to get the next set of results.  If this property is absent, than that means that there is definitively no more results to return.  However, if this value is present, than there is a possibility that there maybe more results to be returned. | string | |
| results    | The list of orders returned.                                                                                                                                                                                                                                                           | array  | |


<div class="datatable-end"></div>

Example:
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

400
{: .label .label-red }

<div class="datatable-begin"></div>

| Property | Description                           | Type | Default |
|----------|---------------------------------------|------|---------|
| message  | String description of what went wrong | string | |

<div class="datatable-end"></div>

Examples:
```json
{
  "message" : "Unsupported field: InvalidField"
}

{
  "message" : "Unsupported operator: NotARealOperator for field: ReferenceNumber"
}
```

