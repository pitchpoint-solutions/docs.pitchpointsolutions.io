---
title: IRS4506 Transcript
layout: api_ref
nav_order: 2
parent: Order
datatable: true
---
Beta
{: .label .label-yellow }

## Order

Ordering an IRS8821Transcript product. 

{: .before_starting }
Get the URL from your Pitchpoint Account Representative of where you should be submitting your orders to. 


```bash
url="https://api.pointservices.com/riskinsight-services-ws/resources/v1/IRS8821Transcript/PDF-001"
# use the `base64` program to encode the pdf file in base64 
file=$( base64 test-pdf.pdf )
data='{
  "Attachments": {
    "Attachment": [
      {
        "Classifier": "irs8821TranscriptAuthorizationConsentCombined",
        "ContentType": "application/pdf",
        "Document": "%%FILE_DATA%%"
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
# Replace the place holder %%FILE_DATA%% with the actual base64 encoded bytes 
data=$(echo "$data" | sed "s/%%FILE_DATA%%/$file/g")

curl -X POST "${url}"
-H "Authorization: Bearer your_access_token_here" 
-H "Content-Type: application/json" 
-d "${data}"
```

#### Header Properties
<div class="datatable-begin"></div>

| Property      | Value            | Required? |
|-------------- |------------------|-----------|
| Content-Type  | application/json | true      |
| Accept        | application/json | false     |

<div class="datatable-end"></div>


#### Request Data Properties
<div class="datatable-begin"></div>

| Property      | Description                                                                                           | Type   | Default |
|---------------|-------------------------------------------------------------------------------------------------------|--------|---------|
| Attachments   | (mandatory) See section below for more details.                                                       | object |         |
| CorrelationID | (mandatory) A user-defined reference number to help identify the order.  Does not need to be unique.  | string |         |
| Preferences   | (mandatory) See section below for more details.                                                       | object |         |
| Terms         | (mandatory) See section below for more details.                                                       | object |         |

<div class="datatable-end"></div>

##### Attachments.Attachment
<div class="datatable-begin"></div>

| Property    | Description                                                                             | Type   |
|-------------|-----------------------------------------------------------------------------------------|--------|
| Classifier  | (mandatory) Must be the string `irs8821TranscriptAuthorizationConsentCombined`          | string |
| ContentType | (mandatory) The mime-type for the input file.  Typically this will be `application/pdf` | string |
| Document    | (mandatory) The base64 encoded bytes of the file                                        | string |

<div class="datatable-end"></div>

##### Preferences.Preference
<div class="datatable-begin"></div>

| Key                          | Description                                                                                                                                                                                                                                                                                                                                                                                                                                 | Type    |
|------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------|
| IRS8821TaxClassificationType | (mandatory) Tax classification of the order.  Must be one of `Individual` or `Company`                                                                                                                                                                                                                                                                                                                                                      | string  |
| IRS8821TaxYearsRequested     | (mandatory) A comma separated list years requested.  Valid years are current year and up to 4 years ago.  E.g. This year is 2024.  Therefore valid years are: 2024,2023,2022,2021,2020                                                                                                                                                                                                                                                      | string  |
| IRS8821HasIRSAccount         | (mandatory) Boolean indicating whether or not to attempt to use the IRS account                                                                                                                                                                                                                                                                                                                                                             | boolean |
| IRS8821FormRequested         | (mandatory) Form requested type.  Must be one of `1040 - Record of Account`, `1040 - Return Transcript`, `W-2 - Employee Earnings`, `1099 - Self-Employed Earnings`, `1065 - Partnership Return Transcript`, `1065 - Partnership Record of Account`, `1120 - Corporate Return Transcript`, `1120 - Corporate Record of Account`, `1120S - S Corporation Return Transcript`, `1120S - S Corporation Record of Account`, or `All Income Data` | string  |

<div class="datatable-end"></div>

##### Terms.Term.personOrPropertyOrParticipant
<div class="datatable-begin"></div>

| Property    | Description                                                                                                                        | Type   |
|-------------|------------------------------------------------------------------------------------------------------------------------------------|--------|
| Person      | One of `Person` or `Participant` must be defined.  See section below for more details.                                             | object |
| Participant | One of `Person` or `Participant` must be defined.  See section below for more details.                                             | object |

<div class="datatable-end"></div>

##### Terms.Term.personOrPropertyOrParticipant.Person
<div class="datatable-begin"></div>

| Property         | Description                                                                                                      | Type    |
|------------------|------------------------------------------------------------------------------------------------------------------|---------|
| DOB              | (optional) Date of Birth -- in the `MM/DD/YYYY` format                                                           | string  |
| FirstName        | (mandatory) First name of the person                                                                             | string  |
| MiddleName       | (optional) Middle name of the person                                                                             | string  |
| LastName         | (mandatory) Last name of the person                                                                              | string  |
| HomePhone        | (optional) Home phone number                                                                                     | string  |
| SSN              | (mandatory) Social Security Number of the person                                                                 | string  |
|                  | (mandatory) Residences.Residence                                                                                 |         |
| CurrentIndicator | Only mandatory when a residence has been added. Indicates whether the address is the current place of residence. | boolean |
| Address          | Only mandatory when a residence has been added                                                                   | object  | 
|                  | (mandatory) Residences.Residence.Address                                                                         |         |
| AddressLine1     | (mandatory) Line1 of the residence of a person                                                                   | string  |
| City             | (mandatory) City of the residence of a person                                                                    | string  |
| State            | (mandatory) State of the residence of a person.  2-character code indicating which state.                        | string  |
| PostalCode       | (mandatory) 5 digit zip code of the residence.                        | string  |

<div class="datatable-end"></div>

##### Terms.Term.personOrPropertyOrParticipant.Participant
<div class="datatable-begin"></div>

| Property     | Description                                                                                     | Type         |
|--------------|-------------------------------------------------------------------------------------------------|--------------|
| CompanyName  | (mandatory) Company name                                                                        | string       |
|              | (mandatory) Items.ParticipantItem                                                               | object/array |
| Key          | (mandatory) Must be the value `TaxIdentificationEIN`                                            | string       | 
| Value        | (mandatory) Employer Identification Number                                                      | string       |
|              | (mandatory) Address                                                                             | object       |
| AddressLine1 | (mandatory) Line1 of the company address                                                        | string       | 
| City         | (mandatory) City of the company address                                                         | string       | 
| State        | (mandatory) 2-character code of the companys' state                                             | string       | 
| PostalCode   | (mandatory) 5 digit zip code of the company address. | string       | 

<div class="datatable-end"></div>




### Responses

200
{: .label .label-green }

The input data is echo-ed back with following additional fields: 

<div class="datatable-begin"></div>

| Property      | Description                                                                                                                                                                                                                 | Type   |
|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------|
| TransactionID | A Pitchpoint generated identifying string to uniquely identify this loan                                                                                                                                                    | string |  
|               | Messages.Message -- A list of messages regarding the result of the order                                                                                                                                                    |        |
| Category      | Typically one of `Info` or `Fault` to indicate whether or not this is an information message or an error message.                                                                                                           | string |
| Code          | An error code.                                                                                                                                                                                                              | string |
| Description   | Description of the message                                                                                                                                                                                                  | string |
| Ref           | The url to query to retrieve the results of the order                                                                                                                                                                       | string | 
|               | Status -- An object that states the current status of the order as related to billing matters.                                                                                                                              |        | 
| Code          | The status code.  This will typically be `U001` during the ordering phase as Pitchpoint has not completed generating the reports. However once the report has been successfully generated this will typically become `S001` | string |
| Description   | A description of the code                                                                                                                                                                                                   | string |


<div class="datatable-end"></div>

Example:
```json
{
  "TransactionID": "0000000000000134076",
  "Messages": {
    "Message": [
      {
        "Category": "Fault",
        "Code": "E001",
        "Description": "1980 is not a valid IRS8821TaxYearsRequested value. Valid values are 2024,2023,2022,2021."
      }
    ]
  },
  "Ref": "http://localhost:8080/riskinsight-services-ws/resources/v1/sami/0000000000000134076",
  "Status": {
    "Code": "U001",
    "Description": "Unserviceable"
  },
  "(echoed input data)": ...,
}
```
