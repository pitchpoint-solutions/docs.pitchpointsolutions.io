---
title: IRS8821 Transcripts
layout: dev_guide
parent: Orders
nav_order: 1
---
Beta
{: .label .label-yellow }
# IRS 8821 Transcript Ordering Guide

## Overview
Your system is ordering an IRS Transcript through the 8821 form.

## Obtaining Authentication Tokens

To test your query, first obtain an access token for the PointServices platform.

## Preferences
Set the following order parameters via the preferences field.

<div class="datatable-begin"></div>

| Key                          | Description                                                                           | Type                                                                                                                                                                                                                                                                                                                                                                                            | Default |
|------------------------------|---------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------|
| IRS8821TaxClassificationType | Tax classification of the order                                                       | Individual,Company                                                                                                                                                                                                                                                                                                                                                                              |         |
| IRS8821TaxYearsRequested     | comma separated list years requested. must be a set of this year and the past 3 years | csv                                                                                                                                                                                                                                                                                                                                                                                             |         |
| IRS8821HasIRSAccount         | Attempt to use the IRS account                                                        | boolean                                                                                                                                                                                                                                                                                                                                                                                         |         |
| IRS8821FormRequested         | Form requested                                                                        | 1040 - Record of Account<br/>1040 - Return Transcript<br/>W-2 - Employee Earnings<br/>1099 - Self-Employed Earnings<br/>1065 - Partnership Return Transcript<br/>1065 - Partnership Record of Account<br/>1120 - Corporate Return Transcript<br/>1120 - Corporate Record of Account<br/>1120S - S Corporation Return Transcript<br/>1120S - S Corporation Record of Account<br/>All Income Data |         |

<div class="datatable-end"></div>



### Step 1: Authenticate and Receive Tokens
Refer to the [Authentication Guide](/developer_guides/authentication/access_token/) for detailed steps on authentication and token retrieval.

### Step 2: Place an order.

This request submits the order details along with a PDF that includes a completed 8821 form and the consent form.

Include the access token in the `Authorization` header following the `Bearer` keyword. Replace `your_access_token_here` with the actual token you obtained during the authentication process.

#### Example Individual request:
The following example orders a W-2 - Employee Earnings transcript for an individual for the years 2023, 2022, and 2021.

```bash
curl -X POST https://api.pointservices.com/riskinsight-services-ws/resources/v1/IRS8821Transcript/PDF-001 
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
        "personOrPropertyOrParticipant": {
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
            "SSN": "111223333"
          }
        }
      }
    ]
  }
}'
```






#### Example response:

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
        "personOrPropertyOrParticipant": {
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
            "SSN": "111223333"
          }
        }
      }
    ]
  },
  "TransactionID": "0000000000000000000"
}
```

#### Example Company request:
The following example demonstrates how to order a W-2 - Employee Earnings transcript for a company for the years 2023 and 2022.

```bash
curl -X POST https://api.pointservices.com/riskinsight-services-ws/resources/v1/IRS8821Transcript/PDF-001 
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
        "personOrPropertyOrParticipant": {
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
      }
    ]
  }
}
'
```

#### Example Response:
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
        "personOrPropertyOrParticipant": {
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
      }
    ]
  },
  "TransactionID": "0000000000000134068"
}

```

#### Example invalid request:
This would order a W-2 - Employee Earnings transcript for a Company for the years 2023, and 2022

```bash
curl -X POST https://api.pointservices.com/riskinsight-services-ws/resources/v1/IRS8821Transcript/PDF-001 
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
        "personOrPropertyOrParticipant": {
          "Person": {
            "DOB": "01/15/1970",
            "FirstName": "Melvin",
            "HomePhone": "212-555-1212",
            "LastName": "Frost",
            "MiddleName": "Antonio",
            "SSN": "111223333"
          }
        }
      }
    ]
  }
}

'
```

#### Example Response

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
        "personOrPropertyOrParticipant": {
          "Person": {
            "DOB": "01/28/1970",
            "FirstName": "Melvin",
            "HomePhone": "601-602-2673",
            "LastName": "Frost",
            "MiddleName": "Antonio",
            "SSN": "111223333"
          }
        }
      }
    ]
  },
  "TransactionID": "0000000000000000000"
}

```

#### Example Invalid request:

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
        "Code": "E001",
        "Description": "1980 is not a valid IRS8821TaxYearsRequested value. Valid values are 2024,2023,2022,2021."
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
        "Value": "2023,2022,2021,1980"
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
  "Ref": "http://localhost:8080/riskinsight-services-ws/resources/v1/sami/0000000000000134076",
  "Status": {
    "Code": "U001",
    "Description": "Unserviceable"
  },
  "Terms": {
    "Term": [
      {
        "personOrPropertyOrParticipant": {
          "Person": {
            "DOB": "01/28/1970",
            "FirstName": "Melvin",
            "HomePhone": "601-602-2673",
            "LastName": "Frost",
            "MiddleName": "Antonio",
            "SSN": "111223333"
          }
        }
      }
    ]
  },
  "TransactionID": "0000000000000134076"
}


```


### Step 3: Check the response for a result using the URL provided in the Ref field.



```bash
curl -X GET https://api.pointservices.com/riskinsight-services-ws/resources/v1/sami/0000000000000000000 
-H "Authorization: Bearer your_access_token_here" 
-H "Content-Type: application/json" 

```

#### Example response:


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
 
