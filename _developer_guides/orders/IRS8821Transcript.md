---
title: IRS8821 Transcripts
layout: dev_guide
parent: Orders
nav_order: 2
---
Beta
{: .label .label-yellow }
# IRS 8821 Transcript Ordering Guide
{:.no_toc}

* TOC
{: toc}



## Overview
Your system is ordering an IRS Transcript through the 8821 form.


{: .before_starting }
Get the URL from your Pitchpoint Account Representative of where you should be submitting your orders to.


### Step 1: Authenticate and Receive Tokens
Refer to the [Authentication Guide](/developer_guides/authentication/access_token/) for detailed steps on authentication and token retrieval.


### Step 2: Place an order

This request submits the order details along with a PDF that includes a completed 8821 form and the consent form.

Include the access token in the `Authorization` header following the `Bearer` keyword. Replace `your_access_token_here` with the actual token you obtained during the authentication process.

{: .api_ref }
For further explanation of required mandatory fields and their meanings, refer to [IRS8821 Transcript API Reference](/api/order/order_irs8821transcript)


#### Example Individual request
The following example orders a W-2 - Employee Earnings transcript for an individual for the years 2023, 2022, and 2021.



```bash
curl -X POST https://api.pointservices.com/riskinsight-services-ws/resources/v1/sami/IRS8821Transcript/PDF-001 
-H "Authorization: Bearer your_access_token_here" 
-H "Content-Type: application/json" 
-d '{
  "Attachments": {
    "Attachment": [
      {
        "Classifier": "irs8821TranscriptAuthorizationConsentCombined",
        "ContentType": "application/pdf",
        "Document": "#BASE64PDF"
      }
    ]
  },
  "CorrelationID": "IndividualIRS8821W2",
  "Preferences": {
    "Preference": [
      {
        "Key": "IRS8821TaxClassificationType",
        "Value": "Individual"
      },
      {
        "Key": "IRS8821TaxYearsRequested",
        "Value": "2023,2022,2021"
      },
      {
        "Key": "IRS8821HasIRSAccount",
        "Value": "false"
      },
      {
        "Key": "IRS8821FormRequested",
        "Value": "W-2 - Employee Earnings"
      }
    ]
  },
  "Terms": {
    "Term": [
      {
        "Person": {
          "DOB": "01/15/1970",
          "FirstName": "Melvin",
          "HomePhone": "212-555-1234",
          "LastName": "Frost",
          "MiddleName": "Antonio",
          "Residences": {
            "Residence": [
              {
                "Address": {
                  "AddressLine1": "12 Any St",
                  "City": "New York",
                  "PostalCode": "21200",
                  "State": "NY"
                },
                "CurrentIndicator": true
              }
            ]
          },
          "SSN": "111223333",
          "ConsentTokens":{
            "ConsentToken": [
              {
                "Type":"ClearID",
                "Value":"7e2bb052-5fea-11ef-8e7e-0242ac110002"
              }
            ]
          }
          
        }
      }
    ]
  }
}'
```






#### Example response

```json
{
  "Attachments": {
    "Attachment": [
      {
        "Classifier": "irs8821TranscriptAuthorizationConsentCombined",
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
        "Code": "I004",
        "Description": "Evaluation was delayed."
      }
    ]
  },
  "Preferences": {
    "Preference": [
      {
        "Key": "IRS8821TaxClassificationType",
        "Value": "Individual"
      },
      {
        "Key": "IRS8821TaxYearsRequested",
        "Value": "2023,2022,2021"
      },
      {
        "Key": "IRS8821HasIRSAccount",
        "Value": "false"
      },
      {
        "Key": "IRS8821FormRequested",
        "Value": "W-2 - Employee Earnings"
      }
    ]
  },
  "Ref": "https://api.pointservices.com/riskinsight-services-ws/resources/v1/sami/0000000000000000000",
  "Status": {
    "Code": "U001",
    "Description": "Unserviceable"
  },
  "Terms": {
    "Term": [
      {
        
        "Person": {
          "DOB": "01/15/1970",
          "FirstName": "Melvin",
          "HomePhone": "212-555-1234",
          "LastName": "Frost",
          "MiddleName": "Antonio",
          "Residences": {
            "Residence": [
              {
                "Address": {
                  "AddressLine1": "12 Any St",
                  "City": "New York",
                  "PostalCode": "21200",
                  "State": "NY"
                },
                "CurrentIndicator": true
              }
            ]
          },
          "SSN": "111223333",
          "ConsentTokens":{
            "ConsentToken": [
              { 
                "Type":"ClearID",
                "Value":"7e2bb052-5fea-11ef-8e7e-0242ac110002"
              }
            ]
          }
        }
        
      }
    ]
  },
  "TransactionID": "0000000000000000000"
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
### Company Request
The following example demonstrates how to order a W-2 - Employee Earnings transcript for a company for the years 2023 and 2022.

```bash
curl -X POST https://api.pointservices.com/riskinsight-services-ws/resources/v1/sami/IRS8821Transcript/PDF-001
-H "Authorization: Bearer your_access_token_here" 
-H "Content-Type: application/json" 
-d '{
  "Attachments": {
    "Attachment": [
      {
        "Classifier": "irs8821TranscriptAuthorizationConsentCombined",
        "ContentType": "application/pdf",
        "Document": "#BASE64PDF"
      }
    ]
  },
  "CorrelationID": "CompanyIRS8821",
  "Preferences": {
    "Preference": [
      {
        "Key": "IRS8821TaxClassificationType",
        "Value": "Company"
      },
      {
        "Key": "IRS8821TaxYearsRequested",
        "Value": "2023,2022"
      },
      {
        "Key": "IRS8821HasIRSAccount",
        "Value": "false"
      },
      {
        "Key": "IRS8821FormRequested",
        "Value": "W-2 - Employee Earnings"
      }
    ]
  },
  "Terms": {
    "Term": [
      {
        
          "Participant": {
            "Address": {
              "AddressLine1": "123 any st",
              "City": "New York",
              "PostalCode": "21200",
              "State": "NY"
            },
            "CompanyName": "Company!",
            "Items": {
              "ParticipantItem": [
                {
                  "Key": "TaxIdentificationEIN",
                  "Value": "100001234"
                }
              ]
            }
          }
        
      }
    ]
  }
}
'
```

#### Example Response
{:.no_toc}

```json
{
  "Attachments": {
    "Attachment": [
      {
        "Classifier": "irs8821TranscriptAuthorizationConsentCombined",
        "ContentType": "application/pdf",
        "Document": "#BASE64PDF"
      }
    ]
  },
  "CorrelationID": "CompanyIRS8821",
  "Messages": {
    "Message": [
      {
        "Category": "Info",
        "Code": "I004",
        "Description": "Evaluation was delayed."
      }
    ]
  },
  "Preferences": {
    "Preference": [
      {
        "Key": "IRS8821TaxClassificationType",
        "Value": "Company"
      },
      {
        "Key": "IRS8821TaxYearsRequested",
        "Value": "2023,2022"
      },
      {
        "Key": "IRS8821HasIRSAccount",
        "Value": "false"
      },
      {
        "Key": "IRS8821FormRequested",
        "Value": "W-2 - Employee Earnings"
      }
    ]
  },
  "Ref": "https://api.pointservices.com/riskinsight-services-ws/resources/v1/sami/0000000000000134068",
  "Status": {
    "Code": "U001",
    "Description": "Unserviceable"
  },
  "Terms": {
    "Term": [
      {
       
          "Participant": {
            "Address": {
              "AddressLine1": "123 any st",
              "City": "New York",
              "PostalCode": "21200",
              "State": "NY"
            },
            "CompanyName": "Company!",
            "Items": {
              "ParticipantItem": [
                {
                  "Key": "TaxIdentificationEIN",
                  "Value": "100001234"
                }
              ]
            }
          }
        
      }
    ]
  },
  "TransactionID": "0000000000000134068"
}

```

### Invalid Request
This would order a W-2 - Employee Earnings transcript for a Company for the years 2023, and 2022

```bash
curl -X POST https://api.pointservices.com/riskinsight-services-ws/resources/v1/sami/IRS8821Transcript/PDF-001 
-H "Authorization: Bearer your_access_token_here" 
-H "Content-Type: application/json" 
-d '{
  "Attachments": {
    "Attachment": [
      {
        "Classifier": "irs8821TranscriptAuthorizationConsentCombined",
        "ContentType": "application/pdf",
        "Document": "#BASE64PDF"
      }
    ]
  },
  "CorrelationID": "IndividualIRS8821W2",
  "Preferences": {
    "Preference": [
      {
        "Key": "IRS8821TaxClassificationType",
        "Value": "Individual"
      },
      {
        "Key": "IRS8821TaxYearsRequested",
        "Value": "2023,2022,2021"
      },
      {
        "Key": "IRS8821HasIRSAccount",
        "Value": "false"
      },
      {
        "Key": "IRS8821FormRequested",
        "Value": "W-2 - Employee Earnings"
      }
    ]
  },
  "Terms": {
    "Term": [
      {
           "Person": {
            "DOB": "01/15/1970",
            "FirstName": "Melvin",
            "HomePhone": "212-555-1212",
            "LastName": "Frost",
            "MiddleName": "Antonio",
            "SSN": "111223333"
          }
        
      }
    ]
  }
}

'
```


#### Example Response
{:.no_toc}

In this response, we see that `Messagges.Message.Category = Fault` so we know that the submission of this order failed.  
Check the `Messages.Message.Description` for more details on how to correct.  

```json
{
  "Attachments": {
    "Attachment": [
      {
        "Classifier": "irs8821TranscriptAuthorizationConsentCombined",
        "ContentType": "application/pdf",
        "Document": "#BASE64PDF"
      }
    ]
  },
  "CorrelationID": "IndividualIRS8821W2",
  "Messages": {
    "Message": [
      {
        "Category": "Fault",
        "Code": "E004",
        "Description": "/Inputs[1]/Input[1]/Loan[1]/Borrower[1]/Residence is required."
      }
    ]
  },
  "Preferences": {
    "Preference": [
      {
        "Key": "IRS8821TaxClassificationType",
        "Value": "Individual"
      },
      {
        "Key": "IRS8821TaxYearsRequested",
        "Value": "2023,2022,2021"
      },
      {
        "Key": "IRS8821HasIRSAccount",
        "Value": "false"
      },
      {
        "Key": "IRS8821FormRequested",
        "Value": "W-2 - Employee Earnings"
      }
    ]
  },
  "Ref": "https://api.pointservices.com/riskinsight-services-ws/resources/v1/sami/0000000000000000000",
  "Status": {
    "Code": "U001",
    "Description": "Unserviceable"
  },
  "Terms": {
    "Term": [
      {
          "Person": {
            "DOB": "01/28/1970",
            "FirstName": "Melvin",
            "HomePhone": "601-602-2673",
            "LastName": "Frost",
            "MiddleName": "Antonio",
            "SSN": "111223333"
          }
        }
      
    ]
  },
  "TransactionID": "0000000000000000000"
}

```


