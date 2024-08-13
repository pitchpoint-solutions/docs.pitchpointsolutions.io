---
title: IRS8821 Transcript
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
{% include_relative api_includes/header_properties_json.md %}


#### Request Data Properties
<div class="datatable-begin"></div>

| Property      | Description                                                                                           | Type   |
|---------------|-------------------------------------------------------------------------------------------------------|--------|
| Attachments   | (mandatory) See section below for more details.                                                       | object |
| CorrelationID | (mandatory) A user-defined reference number to help identify the order.  Does not need to be unique.  | string |
| Preferences   | (mandatory) See section below for more details.                                                       | object |
| Terms         | (mandatory) See section below for more details.                                                       | object |

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

| Key                          | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        | Type    |
|------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------|
| IRS8821TaxClassificationType | (mandatory) Tax classification of the order.  Must be one of `Individual` or `Company`                                                                                                                                                                                                                                                                                                                                                                                                             | string  |
| IRS8821TaxYearsRequested     | (mandatory) A comma separated list years requested.  Valid years are current year and up to 4 years ago.  E.g. This year is 2024.  Therefore valid years are: 2024,2023,2022,2021,2020                                                                                                                                                                                                                                                                                                             | string  |
| IRS8821HasIRSAccount         | (mandatory) Boolean indicating whether or not to attempt to use the IRS account                                                                                                                                                                                                                                                                                                                                                                                                                    | boolean |
| IRS8821FormRequested         | (mandatory) Form requested type.  Must be one of <br/>`1040 - Record of Account`,<br/> `1040 - Return Transcript`, <br/>`W-2 - Employee Earnings`, <br/>`1099 - Self-Employed Earnings`, <br/>`1065 - Partnership Return Transcript`, <br/>`1065 - Partnership Record of Account`, <br/>`1120 - Corporate Return Transcript`, <br/>`1120 - Corporate Record of Account`, <br/>`1120S - S Corporation Return Transcript`, <br/>`1120S - S Corporation Record of Account`, <br/>or `All Income Data` | string  |
| IRS8821TestMode              | (optional) Enable test mode.  Valid test mode values are <br/>`normal` - demonstrates regular behaviour,<br/> `ready` - returns a report immediately, <br/>`invalid` - returns an error report indicating the borrower's ssn was invalid<br /><br />Test mode note: Orders in test mode will return immediately, unlike live requests which will return a pending response on successful orders.                                                                                                   | string  |


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

| Property         | Description                                                                               | Type    |
|------------------|-------------------------------------------------------------------------------------------|---------|
| DOB              | (optional) Date of Birth -- in the `MM/DD/YYYY` format                                    | string  |
| FirstName        | (mandatory) First name of the person                                                      | string  |
| MiddleName       | (optional) Middle name of the person                                                      | string  |
| LastName         | (mandatory) Last name of the person                                                       | string  |
| HomePhone        | (optional) Home phone number                                                              | string  |
| SSN              | (mandatory) Social Security Number of the person                                          | string  |
|                  | (mandatory) Residences.Residence                                                          |         |
| CurrentIndicator | (mandatory) Indicates whether the address is the current place of residence.              | boolean |
|                  | (mandatory) Residences.Residence.Address                                                  |         |
| AddressLine1     | (mandatory) Line1 of the residence of a person                                            | string  |
| City             | (mandatory) City of the residence of a person                                             | string  |
| State            | (mandatory) State of the residence of a person.  2-character code indicating which state. | string  |
| PostalCode       | (mandatory) 5 digit zip code of the residence.                                            | string  |

<div class="datatable-end"></div>

##### Terms.Term.personOrPropertyOrParticipant.Participant
<div class="datatable-begin"></div>

| Property     | Description                                          | Type         |
|--------------|------------------------------------------------------|--------------|
| CompanyName  | (mandatory) Company name                             | string       |
|              | (mandatory) Items.ParticipantItem                    | object/array |
| Key          | (mandatory) Must be the value `TaxIdentificationEIN` | string       | 
| Value        | (mandatory) Employer Identification Number           | string       |
|              | (mandatory) Address                                  | object       |
| AddressLine1 | (mandatory) Line1 of the company address             | string       | 
| City         | (mandatory) City of the company address              | string       | 
| State        | (mandatory) 2-character code of the company's state  | string       | 
| PostalCode   | (mandatory) 5 digit zip code of the company address. | string       | 

<div class="datatable-end"></div>




### Responses

200
{: .label .label-green }

The input data is echo-ed back with following additional fields: 

<div class="datatable-begin"></div>

| Property      | Description                                                                                                                                                                                                                                                                                 | Type   |
|---------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------|
| TransactionID | A Pitchpoint generated identifying string to uniquely identify this loan                                                                                                                                                                                                                    | string |  
|               | Messages.Message -- A list of messages regarding the result of the order                                                                                                                                                                                                                    |        |
| Category      | Typically one of `Info` or `Fault` to indicate whether or not this is an information message or an error message.                                                                                                                                                                           | string |
| Code          | Unique code for the `Message`.  Typically, this is `I004` when an order has initially been submitted, as PPS is still processing the order.  When the report has successfully been generated, you should see a value of `I001`.  A value starting with `EXXX` generally indicates an error. | string |
| Description   | Description of the message                                                                                                                                                                                                                                                                  | string |
| Ref           | The url to query to retrieve the results of the order                                                                                                                                                                                                                                       | string | 
|               | Status -- An object that states the current status of the order as related to billing matters.                                                                                                                                                                                              |        | 
| Code          | The status code.  This will typically be `U001` during the ordering phase as Pitchpoint has not completed generating the reports. However once the report has been successfully generated this will typically become `S001`                                                                 | string |
| Description   | A description of the code                                                                                                                                                                                                                                                                   | string |


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

For a complete list of possible [Message](/api/order/messagedef/) 





#### Error Codes 

This section will continue to be updated (and examples added) as more information becomes available.  


<div class="datatable-begin"></div>

| Code    | Description                                                                                                                                                                               | 
|---------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------| 
| ACT0101 | Correct the borrower's last name then resubmit the order.                                                                                                                                 |
| ACT0102 | Correct the business name then resubmit the order.                                                                                                                                        |
| ACT0203 | If the SSN on the signed 8821 is correct, just edit the SSN on the order then resubmit. \nIf the SSN on the order is correct, have the authorized user sign a new 8821.                   |
| ACT0204 | If the EIN (Employer ID Numbers) on signed 8821 is correct, just edit the EIN on the order then resubmit. \nIf the EIN on the order is correct, have the authorized user sign a new 8821. |
| ACT0305 | Address or TIN (Tax Identification Number) incorrect. Please verify with borrower then resubmit.                                                                                          |
| ACT0406 | Attach an 8821 and Consent Form, then resubmit.                                                                                                                                           |
| ACT0407 | Attach an 8821 then resubmit.                                                                                                                                                             |
| ACT0408 | Attach a Consent Form then resubmit.                                                                                                                                                      |
| ACT0509 | Add tax year to 8821, obtain signature, then resubmit.                                                                                                                                    |
| ACT0610 | Attach signed forms, then resubmit.                                                                                                                                                       |
| ACT0611 | Attach signed 8821, then resubmit.                                                                                                                                                        |
| ACT0612 | Attach signed Consent Form, then resubmit.                                                                                                                                                |
| ACT0713 | Attach an 8821 then resubmit.                                                                                                                                                             |
| ACT0814 | Change column (b) on 8821 to 1120, 1120S, or 1065, get signed, and resubmit                                                                                                               |
| ACT0915 | Complete section 1 \"Taxpayer information\" on 8821, get signed, and resubmit                                                                                                             |
| ACT0115 | request does not exist                                                                                                                                                                    |
| ACT0116 | Borrower does not exist                                                                                                                                                                   |
| ACT0117 | Invalid or blank TIN                                                                                                                                                                      |
| ACT0118 | Invalid or blank first name                                                                                                                                                               |
| ACT0119 | Invalid or blank last name                                                                                                                                                                |

<div class="datatable-end"></div>
