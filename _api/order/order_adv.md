---
title: ADV
layout: api_ref
nav_order: 2
parent: Order
datatable: true
---
Stable
{: .label .label-green }

## Order

Ordering an ADV product. 

{: .before_starting }
Get the URL from your Pitchpoint Account Representative of where you should be submitting your orders to. 


```bash
url="https://api.pointservices.com/riskinsight-services-ws/resources/v1/sami/ADV/ADV-120J"
data='{
  "CorrelationID" : "12345",
  "Terms" : {
    "Term" : [ {
      "Person" : {
        "FirstName" : "John",
        "MiddleName" : "A",
        "LastName" : "Doe",
        "NameSuffix" : "Jr.",
        "SSN" : "123-45-6789",
        "DOB" : "01/01/1980",
        "Gender" : "M",
        "HomePhone" : "123-456-7890",
        "Residences" : {
          "Residence" : [ {
            "Address" : {
              "AddressLine1" : "123 Main St",
              "City" : "Anytown",
              "State" : "CA",
              "PostalCode" : "12345",
              "County" : "AnyCounty"
            },
            "CurrentIndicator" : true
          } ]
        },
        "Employers" : {
          "Employer" : [ {
            "Name" : "ABC Corp",
            "Phone" : "987-654-3210",
            "EIN" : "12-3456789",
            "MonthlyIncome" : "5000",
            "MonthsOnJob" : "24",
            "YearsOnJob" : "2",
            "Address" : {
              "AddressLine1" : "123 Main St",
              "City" : "Anytown",
              "State" : "CA",
              "PostalCode" : "12345",
              "County" : "AnyCounty"
            },
            "CurrentIndicator" : true,
            "SelfEmployedIndicator" : false
          } ]
        },
        "Declarations" : {
          "AlimonyChildSupportObligationIndicator" : false,
          "BorrowedDownPaymentIndicator" : false,
          "CitizenshipResidencyType" : "PermanentResidentAlien",
          "CoMakerEndorserOfNoteIndicator" : false,
          "HomeownerPastThreeYearsIndicator" : true,
          "IntentToOccupyIndicator" : true,
          "LoanForeclosureOrJudgementIndicator" : false,
          "OutstandingJudgementsIndicator" : false,
          "PartyToLawsuitIndicator" : false,
          "PresentlyDelinquentIndicator" : false,
          "PriorPropertyTitleType" : "JointWithSpouse",
          "PriorPropertyUsageType" : "PrimaryResidence",
          "PriorPropertyUsageTypeOtherDescription" : "N/A",
          "PropertyForeclosedPastSevenYearsIndicator" : false,
          "BorrowerFirstTimeHomebuyerIndicator" : true
        }
      }
    }, {
      "Property" : {
        "Address" : {
          "AddressLine1" : "123 Apple St",
          "City" : "Bearsville",
          "State" : "CA",
          "PostalCode" : "12345",
          "County" : "Carrot County"
        },
        "SupplementaryId" : "SUPP123",
        "AssessorsParcelNumber" : "APN123",
        "CountyFIPSCode" : "12345",
        "SaleDate" : "01/01/2020",
        "SaleAmount" : "300000",
        "AssessmentDate" : "01/01/2021",
        "AssessmentAmount" : "350000",
        "PropertyUsage" : "PrimaryResidence",
        "PropertyUsageOtherDescription" : "N/A",
        "PropertyType" : "DetachedCondominium",
        "PropertyTypeOtherDescription" : "N/A",
        "NumberOfUnits" : "1",
        "Valuations" : {
          "Valuation" : [ {
            "ValuationType" : "Appraisal",
            "ValuationTypeOtherDescription" : "N/A",
            "ValuationAmount" : "350000"
          } ]
        },
        "OwnerName" : "John Doe"
      }
    }, {
      "Participant" : {
        "FirstName" : "Jane",
        "MiddleName" : "B",
        "LastName" : "Smith",
        "CompanyName" : "XYZ Corp",
        "ParticipantType" : "Appraiser",
        "ContactPoint" : {
          "RoleType" : "Mobile",
          "Type" : "Phone",
          "Value" : "1234567890",
          "PreferenceIndicator" : true
        },
        "Address" : {
          "AddressLine1" : "123 Dunkin St",
          "City" : "Frisco",
          "State" : "CA",
          "PostalCode" : "12345",
          "County" : "Eagle Ridge County"
        },
        "Licenses" : {
          "License" : [ {
            "LicenseNumber" : "LIC123",
            "LicenseType" : "Appraiser License",
            "LicenseState" : "CA"
          } ]
        }
      }
    } ]
  }
}'

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

| Property      | Description                                                                                          | Type   |
|---------------|------------------------------------------------------------------------------------------------------|--------|
| TransactionID | Only mandatory when refreshing a previously ordered ADV                                              | string |
| CorrelationID | (mandatory) A user-defined reference number to help identify the order.  Does not need to be unique. | string |
| Terms         | (mandatory) See section below for more details.                                                      | object |

<div class="datatable-end"></div>

##### Terms.Term.personOrPropertyOrParticipant
<div class="datatable-begin"></div>

| Property           | Description                                                              | Type   |
|--------------------|--------------------------------------------------------------------------|--------|
| Person             | (mandatory) aka a `Borrower`. See section below for more details.        | object |
| Participant        | (optional) aka a `Loan Participant`. See section below for more details. | object |
| Property           | (optional) See section below for more details.                           | object |

<div class="datatable-end"></div>

##### Terms.Term.personOrPropertyOrParticipant.Person
<div class="datatable-begin"></div>

| Property           | Description                                                                                                                                                                                                                                                                                                                 | Type    |
|--------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------|
| FirstName          | (mandatory) First name of the person                                                                                                                                                                                                                                                                                        | string  |
| MiddleName         | (optional) Middle name of the person                                                                                                                                                                                                                                                                                        | string  |
| LastName           | (mandatory) Last name of the person                                                                                                                                                                                                                                                                                         | string  |
| HomePhone          | (mandatory) Home phone number -- in the `NNN-NNN-NNNN` or `(NNN) NNN-NNNN` or `NNNNNNNNNN` format                                                                                                                                                                                                                           | string  |
| DOB                | (mandatory) Date of Birth -- in the `MM/DD/YYYY` format                                                                                                                                                                                                                                                                     | string  |
| SSN                | (mandatory) Social Security Number of the person -- in the `NNN-NN-NNNN` or `NNNNNNNNN` format                                                                                                                                                                                                                              | string  |
|                    | (mandatory) Residences.Residence                                                                                                                                                                                                                                                                                            |         |
| CurrentIndicator   | (mandatory) Indicates whether the address is the current place of residence.                                                                                                                                                                                                                                                | boolean |
|                    | (mandatory) Residences.Residence.Address                                                                                                                                                                                                                                                                                    |         |
| AddressLine1       | (mandatory) Line1 of the residence of a person                                                                                                                                                                                                                                                                              | string  |
| City               | (mandatory) City of the residence of a person                                                                                                                                                                                                                                                                               | string  |
| State              | (mandatory) State of the residence of a person.  2-character code indicating which state.                                                                                                                                                                                                                                   | string  |
| PostalCode         | (mandatory) 5 or 9 digit zip code of the residence.                                                                                                                                                                                                                                                                         | string  |
|                    | (optional) Employers.Employer -- this section is optional, but if provided the following becomes mandatory:                                                                                                                                                                                                                 |         |
| Name               | (mandatory) Name of the Employer.  If the Employer object is added, then a `Name` is mandatory. For special cases like `Housewife` or `Retired` where there are no associated Employer Address, then the Address and Phone number become optional.  See [Employer](/api/order/enums/types_employer) for more special names. | string  |
| Phone              | (mandatory) in the `NNN-NNN-NNNN` or `(NNN) NNN-NNNN` or `NNNNNNNNNN` format                                                                                                                                                                                                                                                | string  |
|                    | (mandatory) Employers.Employer.Address                                                                                                                                                                                                                                                                                      |         |
| AddressLine1       | (mandatory) Line1 of the address                                                                                                                                                                                                                                                                                            | string  |
| City               | (mandatory) City of the address                                                                                                                                                                                                                                                                                             | string  |
| State              | (mandatory) State of the address.  2-character code indicating which state.                                                                                                                                                                                                                                                 | string  |
| PostalCode         | (mandatory) 5 or 9 digit zip code of the address.                                                                                                                                                                                                                                                                           | string  |
| _all other fields_ | (optional) other fields are optional                                                                                                                                                                                                                                                                                        |         |

<div class="datatable-end"></div>

##### Terms.Term.personOrPropertyOrParticipant.Participant
<div class="datatable-begin"></div>

| Property         | Description                                                                                                                                          | Type    |
|------------------|------------------------------------------------------------------------------------------------------------------------------------------------------|---------|
| ParticipantType  | (mandatory) The type of Participant, depending on the type set, (person vs company), various fields will become mandatory while others are optional. | string  |

<div class="datatable-end"></div>

See [Loan Participant Type](/api/order/enums/types_loanparticipant/) for a list of possible `ParticipantType` values.


##### Terms.Term.personOrPropertyOrParticipant.Property
<div class="datatable-begin"></div>

| Property           | Description                                                                               | Type   |
|--------------------|-------------------------------------------------------------------------------------------|--------|
|                    | The entire Property section is optional, but if included, the following becomes mandatory |        |
|                    | (mandatory) Address                                                                       | object |
| AddressLine1       | (mandatory) Line1 of the propery of a person                                              | string |
| City               | (mandatory) City of the property of a person                                              | string |
| State              | (mandatory) State of the property of a person.  2-character code indicating which state.  | string |
| PostalCode         | (mandatory) 5 or 9 digit zip code of the property.                                        | string |
| PropertyUsage      | (optional) See enums at: [Property Type](/api/order/enums/types_property/)                | string |
| PropertyType       | (optional) See enums at: [Property Type](/api/order/enums/types_property/)                | string |
| _all other fields_ | (optional) other fields are optional                                                      | string |

<div class="datatable-end"></div>

See [Property Type](/api/order/enums/types_property/) for a list of possible enum values.



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
  "(echoed input data)": ...,
  "Attachments" : {
    "Attachment" : [ {
      "Document" : "RW5jb2RlZCBEb2N1bWVudCBoZXJl",
      "Classifier" : "report",
      "ContentDisposition" : "inline; filename=\"ADV120\"; creation-date=\"Wed, 05 Mar 2014 14:52:07 EST\"; modification-date=\"Wed, 05 Mar 2014 14:52:07 EST\"; read-date=\"Wed, 05 Mar 2014 14:52:07 EST\"; size=131536",
      "ContentType" : "application/pdf"
    } ]
  },
  "Messages" : {
    "Message" : [ {
      "Category" : "Info",
      "Code" : "I001",
      "Description" : "Success"
    } ]
  },
  "Status" : {
    "Code" : "S001",
    "Description" : "Serviceable"
  },
  "Ref" : "https://api.pointservices.com/riskinsight-services-ws/resources/v1/sami/0000000000000511164"
}
```

For a complete list of possible [Message](/api/order/enums/messagedef/) 



