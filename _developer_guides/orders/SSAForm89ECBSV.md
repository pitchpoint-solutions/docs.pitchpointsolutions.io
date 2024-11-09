---
title: SSAForm89 ECBSV
layout: dev_guide
parent: Orders
nav_order: 30
---
Stable
{: .label .label-yellow }
# SSAForm89 ECBSV Ordering Guide
{:.no_toc}

* TOC
{: toc}



## Overview
Your system is ordering a SSAForm89ECBSV through the form.


{: .before_starting }
Get the URL from your Pitchpoint Account Representative of where you should be submitting your orders to.


### Step 1: Authenticate and Receive Tokens
Refer to the [Authentication Guide](/developer_guides/authentication/access_token/) for detailed steps on authentication and token retrieval.

### Step 1-2: EIN Configuration
If you so choose, you can let Pitchpoint know your EIN number and we can configure it on the product.  
This way, you will not need to send it in on each request.  
If you do not configure it, you will need to send it each time a new order is placed.  
 


### Step 2: Place an order

This request submits the order details along with a PDF that includes a completed 8821 form and the consent form.

Include the access token in the `Authorization` header following the `Bearer` keyword. Replace `your_access_token_here` with the actual token you obtained during the authentication process.

{: .api_ref }
For further explanation of required mandatory fields and their meanings, refer to [IRS8821 Transcript API Reference](/api/order/order_irs8821transcript)


#### Example EIN has been pre-configured 
The following example assumes that the EIN has already been configured on the product.     



```bash
curl -X POST https://api.pointservices.com/riskinsight-services-ws/resources/v1/sami/IRS8821Transcript/PDF-001 
-H "Authorization: Bearer your_access_token_here" 
-H "Content-Type: application/json" 
-H "Accept: application/json" 
-d '{
  "CorrelationID": "no-ein-sent-already-configured-on-product",
  "Preferences": {
    "Preference": [
      {
        "Key": "SignatureType",
        "Value": "w"
      }
    ]
  },
  "Terms": {
    "Term": [
      {
        
        "Person": {
          "FirstName": "Homer",
          "MiddleName": "Jay",
          "LastName": "Simpson",
          "DOB": "05/12/1956",
          "SSN": "111223333"
        }
      }
    ]
  },
  "Attachments": {
    "Attachment": [
      {
        "Classifier": "ssaForm89ecbsv",
        "ContentType": "application/pdf",
        "Document": "#BASE64PDF"
      }
    ]
  }
}'
```






#### Example response

```json
{
  "TransactionID": "0000000000011038392",
  "CorrelationID": "signatureType-wet-ink",
  "Meta": null,
  "Preferences": {
    "Preference": [
      {
        "Key": "SignatureType",
        "Value": "w"
      }
    ]
  },
  "Terms": {
    "Term": [
      {
        "Person": {
          "FirstName": "Homer",
          "MiddleName": "Jay",
          "LastName": "Simpson",
          "NameSuffix": null,
          "SSN": "111223333",
          "DOB": "05/12/1956",
          "YOB": null,
          "Gender": null,
          "HomePhone": null,
          "Residences": null,
          "Employers": null,
          "EthnicGroup": null,
          "EyeColor": null,
          "BankAccounts": null,
          "Declarations": null,
          "CreditReportIdentifier": null,
          "CourtDetails": null,
          "Email": null,
          "ConsentTokens": null
        }
      }
    ]
  },
  "Attachments": {
    "Attachment": [
      {
        "Properties": null,
        "Extension": null,
        "Document": "#BASE64PDF",
        "Classifier": "ssaForm89ecbsv",
        "ContentDisposition": null,
        "ContentType": "application/pdf"
      }
    ]
  },
  "Messages": {
    "Message": [
      {
        "Category": "Info",
        "Code": "I004",
        "Description": "Evaluation was delayed."
      }
    ]
  },
  "Status": {
    "Code": "U001",
    "Description": "Unserviceable"
  },
  "Ref": "https://api.pointservices.com/riskinsight-services-ws/resources/v1/sami/0000000000011038392"
}
```


### Step 3: Check the response for a result using the URL provided in the Ref field
As you can see from the above response, it may take some time for the final report to be generated.  
(`Messages.Message.Code = I004` -- The order was successfully submitted, but the PPS system has yet to complete the order.)  
Using the url returned in the `Ref`, poll (we suggest every 10 minutes) until you receive the report.  


```bash
curl -X GET https://api.pointservices.com/riskinsight-services-ws/resources/v1/sami/0000000000000000000 
-H "Authorization: Bearer your_access_token_here" 
-H "Content-Type: application/json" 

```


#### Example response


```json
{
  "Attachments": {
    "Attachment": [
      {
        "Classifier": "report",
        "ContentDisposition": "inline; filename=\"W-2 - Employee Earnings.2023.pdf\"; creation-date=\"Thu, 30 May 2024 14:35:52 EDT\"; modification-date=\"Thu, 30 May 2024 14:35:52 EDT\"; read-date=\"Thu, 30 May 2024 14:35:52 EDT\"; size=253797",
        "ContentType": "application/pdf",
        "Document": "#BASE64PDF"
      }
    ]
  },
  "CorrelationID": "IndividualIRS8821W2",
  "Messages": {
    "Message": [
      {
        "Category": "Info",
        "Code": "I001",
        "Description": "Success"
      }
    ]
  },
  "Preferences": {
    "Preference": [
      {
        "Key": "IncludeServicedOutputs",
        "Value": "true"
      }
    ]
  },
  "Status": {
    "Code": "S001",
    "Description": "Serviceable"
  },
  "TransactionID": "0000000000000134063"
}

```

The response came back with `Messages.Message.Code = I001` which indicates that the order has successfully completed.  The PDF bytes are in the 
`Document` field, encoded as base64.  Decode to get the pdf.   

Assuming the response json was saved into the file called `file.json`

```bash
# Using the json (bash) program called `jq`, select the object in the Attachments with classifier = report 
# and use the (bash) program called `base64` to decode the file into `mypdf.pdf`

jq -r '.Attachments.Attachment[] | select(.Classifier == "report") | .Document' file.json | base64 --decode > mypdf.pdf    
```` 

## Other Examples
### Sending the EIN on the request
The following example demonstrates ordering by providing the EIN on the request. 

```bash
curl -X POST https://api.pointservices.com/riskinsight-services-ws/resources/v1/sami/IRS8821Transcript/PDF-001
-H "Authorization: Bearer your_access_token_here" 
-H "Content-Type: application/json" 
-d '{
  "CorrelationID": "ein-sent-as-part-of-request",
  "Preferences": {
    "Preference": [
      {
        "Key": "SignatureType",
        "Value": "e"
      },
      {
        "Key": "EIN",
        "Value": "111223333"
      }

    ]
  },
  "Terms": {
    "Term": [
      {
        
        "Person": {
          "FirstName": "Homer",
          "MiddleName": "Jay",
          "LastName": "Simpson",
          "DOB": "05/12/1956",
          "SSN": "111223333"
        }
      }
    ]
  },
  "Attachments": {
    "Attachment": [
      {
        "Classifier": "ssaForm89ecbsv",
        "ContentType": "application/pdf",
        "Document": "{{pps_base64_file}}"
      }
    ]
  }
}
'
```



