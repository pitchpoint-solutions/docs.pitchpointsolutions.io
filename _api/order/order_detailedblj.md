---
title: DetailedBLJ
layout: api_ref
nav_order: 2
parent: Order
datatable: true
---
Stable
{: .label .label-green }

## DetailedBLJ
{:.no_toc}

Ordering a DetailedBLJ product. 

{: .before_starting }
Get the URL from your Pitchpoint Account Representative of where you should be submitting your orders to. 

* TOC
{: toc}


### Sample
```bash
url="https://api.pointservices.com/riskinsight-services-ws/resources/v1/sami/DetailedBLJ/PDF-001"
curl -X POST "${url}"
-H "Authorization: Bearer your_access_token_here" 
-H "Content-Type: application/xml" 
-d '<Sami>
    <CorrelationID>alpha_numeric_string_to_help_identify_this_order</CorrelationID>
   <Terms>
     <Term>
        <Person>
            <FirstName>Janet</FirstName>
            <LastName>Elahi</LastName>
            <SSN>111111111</SSN>
        </Person>
     </Term>
   </Terms>
</Sami>'
```

#### Header Properties
{% include_relative api_includes/header_properties_xml.md %}

#### Request Data Properties
<div class="datatable-begin"></div>

| Property      | Description                                                                                           | Type   | Default |
|---------------|-------------------------------------------------------------------------------------------------------|--------|---------|
| CorrelationID | (mandatory) A user-defined reference number to help identify the order.  Does not need to be unique.  | string |         |
| Terms         | (mandatory) See section below for more details.                                                       | object |         |

<div class="datatable-end"></div>


##### Terms.Term.Person
<div class="datatable-begin"></div>

| Property         | Description                                                                               | Type    |
|------------------|-------------------------------------------------------------------------------------------|---------|
| FirstName        | (mandatory) First name of the person                                                      | string  |
| MiddleName       | (optional) Middle name of the person                                                      | string  |
| LastName         | (mandatory) Last name of the person                                                       | string  |
| DOB              | (optional) Date of Birth -- in the `MM/DD/YYYY` format                                    | string  |
| HomePhone        | (optional) Home phone number                                                              | string  |
| SSN              | (mandatory) Social Security Number of the person                                          | string  |

<div class="datatable-end"></div>



### Responses

200
{: .label .label-green }

The input data is echo-ed back with following additional fields: 

<div class="datatable-begin"></div>

| Property      | Description                                                                                                                                             | Type   |
|---------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|--------|
| TransactionID | A Pitchpoint generated identifying string to uniquely identify this loan                                                                                | string |  
|               | Messages.Message -- A list of messages regarding the result of the order                                                                                |        |
| Category      | Typically one of `Info` or `Fault` to indicate whether or not this is an information message or an error message.                                       | string |
| Code          | Unique code for the `Message`.  Typically, this is `I001` when a report has been generated.  A value starting with `EXXX` generally indicates an error. | string |
| Description   | Description of the message                                                                                                                              | string |
| Ref           | The url to query to retrieve the results of the order                                                                                                   | string | 
|               | Status -- An object that states the current status of the order as related to billing matters.                                                          |        | 
| Code          | The status code.  This will typically be `S001`                                                                                                         | string |
| Description   | A description of the code                                                                                                                               | string |
|               | Attachments -- A list of report(s).  Depending on your particular product configuration, you may get one or two reports.                                |        |
| Attachments   | See section below for more details                                                                                                                      | object |

<div class="datatable-end"></div>

#### Attachments.Attachment

<div class="datatable-begin"></div>

| Property                    | Description                                                    | Type   |
|-----------------------------|----------------------------------------------------------------|--------|
| Document                    | A base64 encoded file                                          | string | 
| Classifier                  | Indicates what type of Attachment this is.  Typically `report` | string |
| ContentDisposition          | Metadata about the file                                        | string | 
| ContentType                 | The mimetype of the file                                       | string |
|                             |                                                                |        | 
| Extension                   |                                                                |        |
| Extension.DetailedBLJReport | an xml representation of the report                            | object |


<div class="datatable-end"></div>

Example:
```xml
<Sami>
    <TransactionID>0000000000001689431</TransactionID>
    <CorrelationID>alpha_numeric_string_to_help_identify_this_order</CorrelationID>
    <Terms>
        <Term>
            <Person>
                <FirstName>Janet</FirstName>
                <LastName>Elahi</LastName>
                <SSN>111111111</SSN>
            </Person>
        </Term>
    </Terms>
    <Attachments>
        <Attachment>
            <Document>JVBERi0xLjUKJd/++LIKMSAwIG9iago8PC9UeXBlL0NhdGFsb2cvUGFnZXMgMyA...</Document>
            <Classifier>report</Classifier>
            <ContentDisposition>inline; filename="PersonDetailedBLJ"; creation-date="Thu, 19 Apr 2018 08:56:29 EDT"; modification-date="Thu, 19 Apr 2018 08:56:29 EDT"; read-date="Thu, 19 Apr 2018 08:56:29 EDT"; size=3766</ContentDisposition>
            <ContentType>application/pdf</ContentType>
        </Attachment>
        <Attachment>
            <Extension>
                <DetailedBLJReport>
                    <Filters>
                        <ExcludeBankruptcies>false</ExcludeBankruptcies>
                        <SkipNameValidation>false</SkipNameValidation>
                        <FilterVerbiage>Judgment satisfied = 7 years; Judgment unsatisfied = 7 years; Lien released = 7 years; Lien unreleased = 10 years; Bankruptcy chapter 7 or 11 dismissed or discharged = 10 years; Bankruptcy chapter 7 or 11 not dismissed or discharged = 10 years; Bankruptcy chapter 13 dismissed or discharged = 7 years; Bankruptcy chapter 13 not dismissed or discharged = 10 years</FilterVerbiage>
                    </Filters>
                    <BankruptciesFound>0</BankruptciesFound>
                    <JudgmentsFound>0</JudgmentsFound>
                    <LiensFound>0</LiensFound>
                    <Messages>
                        <Message>
                            <Code>0</Code>
                            <Text>No Errors</Text>
                        </Message>
                    </Messages>
                </DetailedBLJReport>
            </Extension>
            <ContentType>text/xml</ContentType>
        </Attachment>
    </Attachments>
    <Messages>
        <Message>
            <Category>Info</Category>
            <Code>I001</Code>
            <Description>Success</Description>
        </Message>
    </Messages>
    <Status>
        <Code>S001</Code>
        <Description>Serviceable</Description>
    </Status>
    <Ref>https://api.pointservices.com/riskinsight-services-ws/resources/v1/sami/0000000000001689431</Ref>
</Sami>
```
