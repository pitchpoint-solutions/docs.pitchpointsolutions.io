---
title: SSAForm89 ECBSV 
layout: api_ref
nav_order: 30
parent: Order
datatable: true
---
Stable
{: .label .label-green }

## Order

Ordering an SSAForm89ECBSV product. 

{: .before_starting }
Get the URL from your Pitchpoint Account Representative of where you should be submitting your orders to. 


```bash
url="https://api.pointservices.com/riskinsight-services-ws/resources/v1/sami/SSAForm89ECBSV/PDF-001"
# use the `base64` program to encode the pdf file in base64 
file=$( base64 -i test-pdf.pdf )
data='{
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
        "Document": "%%FILE_DATA%%"
      }
    ]
  }
}

'
# Replace the place holder %%FILE_DATA%% with the actual base64 encoded bytes 
data=$(echo "$data" | sed "s#%%FILE_DATA%%#$file#g")

curl -X POST "${url}"
-H "Authorization: Bearer your_access_token_here" 
-H "Content-Type: application/json" 
-H "Accept: application/json" 
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
| Classifier  | (mandatory) Must be the string `ssaForm89ecbsv`          | string |
| ContentType | (mandatory) The mime-type for the input file.  Typically this will be `application/pdf` | string |
| Document    | (mandatory) The base64 encoded bytes of the file                                        | string |

<div class="datatable-end"></div>

##### Preferences.Preference
<div class="datatable-begin"></div>

| Key           | Description                                                                       | Type   |
|---------------|-----------------------------------------------------------------------------------|--------|
| SignatureType | (mandatory) Signature type.  Must be one of `w` for wet-ink or `e` for electronic | string |
| EIN           | (optional) Employer Identification Number                                         | string |


<div class="datatable-end"></div>

##### Terms.Term.personOrPropertyOrParticipant
<div class="datatable-begin"></div>

| Property    | Description                                                                                                                        | Type   |
|-------------|------------------------------------------------------------------------------------------------------------------------------------|--------|
| Person      | One of `Person` or `Participant` must be defined.  See section below for more details.                                             | object |

<div class="datatable-end"></div>

##### Terms.Term.personOrPropertyOrParticipant.Person
<div class="datatable-begin"></div>

| Property         | Description                                             | Type    |
|------------------|---------------------------------------------------------|---------|
| DOB              | (mandatory) Date of Birth -- in the `MM/DD/YYYY` format | string  |
| FirstName        | (mandatory) First name of the person                    | string  |
| MiddleName       | (optional) Middle name of the person                    | string  |
| LastName         | (mandatory) Last name of the person                     | string  |
| SSN              | (mandatory) Social Security Number of the person        | string  |

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
        "Category": "Info",
        "Code": "I004",
        "Description": "Evaluation was delayed."
      }
    ]
  },
  "Ref": "https://api.pointservices.com/riskinsight-services-ws/resources/v1/sami/0000000000000134076",
  "Status": {
    "Code": "U001",
    "Description": "Unserviceable"
  },
  "(echoed input data)": ...,
}
```

For a complete list of possible [Message](/api/order/messagedef/) 



