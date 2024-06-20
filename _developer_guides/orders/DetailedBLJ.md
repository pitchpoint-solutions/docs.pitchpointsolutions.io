---
title: DetailedBLJ
layout: dev_guide
parent: Orders
nav_order: 2
---
Stable
{: .label .label-green }
# DetailedBLJ Ordering Guide
{:.no_toc}


* TOC
{: toc}


## Overview
Your system is ordering a Detailed BLJ.


{: .before_starting }
Get the URL from your Pitchpoint Account Representative of where you should be submitting your orders to.

### Step 1: Authenticate and Receive Tokens
Refer to the [Authentication Guide](/developer_guides/authentication/access_token/) for detailed steps on authentication and token retrieval.


### Step 2: Place an order

This request submits the order details.

Include the access token in the `Authorization` header following the `Bearer` keyword. Replace `your_access_token_here` with the actual token you obtained during the authentication process.

{: .api_ref }
For further explanation of required mandatory fields and their meanings, refer to [DetailedBLJ API Reference](/api/order/order_detailedblj)


#### Example request
The following example orders a DetailedBLJ report.  The configured product has been configured to return a PDF report.  



```bash
curl -X POST https://api.pointservices.com/riskinsight-services-ws/resources/v1/sami/DetailedBLJ/PDF-001 
-H "Authorization: Bearer your_access_token_here" 
-H "Content-Type: application/xml" 
-d '<Sami>
   <CorrelationID>SAMI_BLJ_1524141772750</CorrelationID>
   <Terms>
      <Term>
         <Person>
            <FirstName>Kelly</FirstName>
            <LastName>russell</LastName>
            <SSN>000111000</SSN>
         </Person>
      </Term>
   </Terms>
</Sami>'
```





#### Example response

The following example response is from a product that was configured with only the PDF report to be returned. 

```xml
<Sami>
    <TransactionID>0000000000001689427</TransactionID>
    <CorrelationID>SAMI_BLJ_1524141772750</CorrelationID>
    <Terms>
        <Term>
            <Person>
                <FirstName>Kelly</FirstName>
                <LastName>russell</LastName>
                <SSN>000111000</SSN>
            </Person>
        </Term>
    </Terms>
    <Attachments>
        <Attachment>
            <Document>JVBERi0xLjUKJd/++LIKMSAwIG9iago8PC9UeXBlL0NhdGFsb2cvUGFnZXMgMyAwIFIvTG=====...</Document>
            <Classifier>report</Classifier>
            <ContentDisposition>inline; filename="PersonDetailedBLJPDF"; creation-date="Thu, 19 Apr 2018 08:43:07 EDT"; modification-date="Thu, 19 Apr 2018 08:43:07 EDT"; read-date="Thu, 19 Apr 2018 08:43:07 EDT"; size=6674</ContentDisposition>
            <ContentType>application/pdf</ContentType>
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
    <Ref>https://intg.pointservices.com/riskinsight-services-ws/resources/v1/sami/0000000000001689427</Ref>
</Sami>
```


## Other Examples
### Two Reports Returned in Response
The following example orders a DetailedBLJ report.  The product has been configured to return both a PDF and an XML report. 

```bash
curl -X POST https://api.pointservices.com/riskinsight-services-ws/resources/v1/sami/DetailedBLJ/PDF-001
-H "Authorization: Bearer your_access_token_here" 
-H "Content-Type: application/xml" 
-d '<Sami>
    <CorrelationID>SAMI_BLJ_1524142585040</CorrelationID>
    <Terms>
        <Term>
            <Person>
                <FirstName>Janet</FirstName>
                <LastName>Elahi</LastName>
                <SSN>111000111</SSN>
            </Person>
        </Term>
    </Terms>
</Sami>
'
```

#### Example Response
{:.no_toc}

Notice in the `Attachments` section there are two `Attachment`.  One for the base64 encode PDF file, the other is the XML report.  


```xml
<Sami>
    <TransactionID>0000000000001689431</TransactionID>
    <CorrelationID>SAMI_BLJ_1524142585040</CorrelationID>
    <Terms>
        <Term>
            <Person>
                <FirstName>Janet</FirstName>
                <LastName>Elahi</LastName>
                <SSN>111000111</SSN>
            </Person>
        </Term>
    </Terms>
    <Attachments>
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
        <Attachment>
            <Document>JVBERi0xLjUKJd/++LIKMSAwIG9iago8PC9UeXBlL0NhdGFsb2cvUGFnZXMgMyA...</Document>
            <Classifier>report</Classifier>
            <ContentDisposition>inline; filename="PersonDetailedBLJ"; creation-date="Thu, 19 Apr 2018 08:56:29 EDT"; modification-date="Thu, 19 Apr 2018 08:56:29 EDT"; read-date="Thu, 19 Apr 2018 08:56:29 EDT"; size=3766</ContentDisposition>
            <ContentType>application/pdf</ContentType>
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
    <Ref>https://intg.pointservices.com/riskinsight-services-ws/resources/sami/001/0000000000011689431</Ref>
</Sami>

```

### Missing Input Data 
First name is missing. 

```bash
curl -X POST https://api.pointservices.com/riskinsight-services-ws/resources/v1/sami/DetailedBLJ/PDF-001 
-H "Authorization: Bearer your_access_token_here" 
-H "Content-Type: application/xml" 
-d '<Sami>
   <CorrelationID>first_name_missing</CorrelationID>
    <Terms>
     <Term>
         <Person>
            <FirstName></FirstName>
            <LastName>THOMAS</LastName>
            <SSN>111111111</SSN>
         </Person>
      </Term>
   </Terms>
</Sami>
'
```


#### Example Response
{:.no_toc}

In this response, we see that `Messagges.Message.Category = Fault` so we know that the submission of this order failed.  
Check the `Messages.Message.Description` for more details on how to correct.  

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<Sami>
    <TransactionID>0000000000010129045</TransactionID>
    <CorrelationID>first_name_missing</CorrelationID>
    <Terms>
        <Term>
            <Person>
                <FirstName></FirstName>
                <LastName>THOMAS</LastName>
                <SSN>111111111</SSN>
            </Person>
        </Term>
    </Terms>
    <Messages>
        <Message>
            <Category>Fault</Category>
            <Code>E004</Code>
            <Description>/Inputs[1]/Input[1]/Loan[1]/Borrower[1]/Name[1]/First is required.</Description>
        </Message>
    </Messages>
    <Status>
        <Code>U001</Code>
        <Description>Unserviceable</Description>
    </Status>
    <Ref>https://qa-api.pointservices.com/riskinsight-services-ws/resources/v1/sami/0000000000010129045</Ref>
</Sami>

```

### Error Report 
In this sample response, the product was configured to only return an XML report.  The request succeed, but there was a mismatch between name and SSN. 

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<Sami>
    <TransactionID>0000000000000208639</TransactionID>
    <CorrelationID>SAMI_BLJ_1524143057520</CorrelationID>
    <Terms>
        <Term>
            <Person>
                <FirstName>Michael</FirstName>
                <MiddleName>D</MiddleName>
                <LastName>Henson</LastName>
                <SSN>111222333</SSN>
            </Person>
        </Term>
    </Terms>
    <Attachments>
        <Attachment>
            <Extension>
                <DetailedBLJReport xmlns="">
                    <Filters>
                        <ExcludeBankruptcies>false</ExcludeBankruptcies>
                        <SkipNameValidation>false</SkipNameValidation>
                        <FilterVerbiage>Judgment satisfied = 7 years; Judgment unsatisfied = 7 years; Lien released = 7 years; Lien unreleased = 10 years; Bankruptcy chapter 7 or 11 dismissed or discharged = 10 years; Bankruptcy chapter 7 or 11 not dismissed or discharged = 10 years; Bankruptcy chapter 13 dismissed or discharged = 7 years; Bankruptcy chapter 13 not dismissed or discharged = 10 years</FilterVerbiage>
                    </Filters>
                    <Errors>
                        <Error>
                            <Code>4</Code>
                            <Message>SSN/Name mismatch; check input, if correct contact support@pitchpointsolutions.com</Message>
                        </Error>
                    </Errors>
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
    <Ref>https://intg.pointservices.com/riskinsight-services-ws/resources/v1/sami/0000000000001208639</Ref>
</Sami>
```

