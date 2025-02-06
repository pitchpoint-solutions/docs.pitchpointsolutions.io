---
title: ADV
layout: dev_guide
parent: Orders
nav_order: 5
---
Beta
{: .label .label-yellow }
# ADV Ordering Guide
{:.no_toc}

* TOC
{: toc}



## Overview
Your system is ordering an ADV.


{: .before_starting }
Get the URL from your Pitchpoint Account Representative of where you should be submitting your orders to.


### Step 1: Authenticate and Receive Tokens
Refer to the [Authentication Guide](/developer_guides/authentication/access_token/) for detailed steps on authentication and token retrieval.


### Step 2: Place an order

Include the access token in the `Authorization` header following the `Bearer` keyword. Replace `your_access_token_here` with the actual token you obtained during the authentication process.

{: .api_ref }
For further explanation of required mandatory fields and their meanings, refer to [ADV API Reference](/api/order/order_adv)


#### Example 2 Borrowers Request
Ordering an ADV with two borrowers.


```bash
curl -X POST https://api.pointservices.com/riskinsight-services-ws/resources/v1/sami/ADV/ADV-120 
-H "Authorization: Bearer your_access_token_here" 
-H "Content-Type: application/json" 
-H "Accept: application/json" 
-d '{
  "CorrelationID" : "SAMI_ADV120_1534192619544",
  "Terms" : {
    "Term" : [ {
      "Person" : {
        "FirstName" : "Melvin",
        "LastName" : "Frost",
        "SSN" : "123334444",
        "DOB" : "11/11/1977",
        "HomePhone" : "6128883322",
        "Residences" : {
          "Residence" : [ {
            "Address" : {
              "AddressLine1" : "2824 Pumpkin Avenue",
              "City" : "Minneapolis",
              "State" : "mn",
              "PostalCode" : "55408"
            },
            "CurrentIndicator" : true
          } ]
        },
        "Employers" : {
          "Employer" : [ {
            "Name" : "Google",
            "Phone" : "650 253 0000",
            "Address" : {
              "AddressLine1" : "1600 Amphitheatre Parkway",
              "City" : "Mountain View",
              "State" : "CA",
              "PostalCode" : "94043"
            },
            "CurrentIndicator" : true
          } ]
        }
      }
    }, {
      "Person" : {
        "FirstName" : "JETHRO",
        "LastName" : "CCLAMPETT",
        "SSN" : "222014141",
        "DOB" : "10/25/1966",
        "HomePhone" : "5556663333",
        "Residences" : {
          "Residence" : [ {
            "Address" : {
              "AddressLine1" : "12 Some Address",
              "City" : "SomeCity",
              "State" : "AL",
              "PostalCode" : "55408"
            },
            "CurrentIndicator" : true
          } ]
        },
        "Employers" : {
          "Employer" : [ {
            "Name" : "Thornton & Thornton",
            "Phone" : "612 861 8619",
            "Address" : {
              "AddressLine1" : "7128 James Ave S",
              "City" : "Minneapolis",
              "State" : "MN",
              "PostalCode" : "55423"
            },
            "CurrentIndicator" : true
          } ]
        }
      }
    }, {
      "Property" : {
        "Address" : {
          "AddressLine1" : "210 PeachTree Rd",
          "City" : "Oakboro",
          "State" : "nc",
          "PostalCode" : "28129"
        }
      }
    } ]
  }
}'
```


If you plan on ordering a `Refresh` later, it is recommended that you keep a copy of the original request (or ensure you're able to generate it again).  



#### Example response

```json
{
  "TransactionID" : "0000000000001857105",
  "CorrelationID" : "SAMI_ADV120_1534193229528",
  "Terms" : {
    "Term" : [ { /* trimmed for brevity */ } ]
  },
  "Attachments" : {
    "Attachment" : [ {
      "Extension" : {
        "any" : "<?xml version=\"1.0\" encoding=\"UTF-16\"?>\n<IDInvestigationReport><ProcessDate>2018-08-13T16:47:12.573-04:00</ProcessDate><ReferenceNumber>SAMI_ADV120_1534193229528</ReferenceNumber><InputData><Name>Melvin Frost</Name><AddressLine1>2824 Pumpkin Avenue</AddressLine1><AddressLine2/><City>Minneapolis</City><State>MINNESOTA</State><Zip>55408</Zip><SSN>123-33-4444</SSN><DateOfBirth>11/11/1977</DateOfBirth><Phone>(612) 888-3322(home) (650) 253-0000(business)</Phone>\n\t\t\t\t\t</InputData><Score><ValidationScore>99</ValidationScore><KeyDrivers><KeyDriver><Rank>1</Rank><Description>Phone and Zip code mismatch.</Description>\n\t\t\t\t\t\t\t</KeyDriver><KeyDriver><Rank>2</Rank><Description>Date Of Birth could not be verified</Description>\n\t\t\t\t\t\t\t</KeyDriver><KeyDriver><Rank>3</Rank><Description>Name not reported at Address</Description>\n\t\t\t\t\t\t\t</KeyDriver><KeyDriver><Rank>4</Rank><Description>Phone Number could not be verified</Description>\n\t\t\t\t\t\t\t</KeyDriver><KeyDriver><Rank>5</Rank><Description>Neighborhood risk interaction.</Description>\n\t\t\t\t\t\t\t</KeyDriver>\n\t\t\t\t\t\t</KeyDrivers>\n\t\t\t\t\t</Score><Results><FirstName><Input>Melvin</Input><Result>Failed</Result>\n\t\t\t\t\t\t</FirstName><LastName><Input>Frost</Input><Result>Failed</Result>\n\t\t\t\t\t\t</LastName><Address><Input>2824 Pumpkin Avenue</Input><Result>Failed</Result><Messages><Message>Address is invalid.</Message><Message>Name not reported at Address</Message><Message>Neighborhood risk interaction.</Message>\n\t\t\t\t\t\t\t</Messages>\n\t\t\t\t\t\t</Address><Phone><Input>(612) 888-3322</Input><Result>Failed</Result><Messages><Message>No Name or Address reported with Phone</Message><Message>Phone and Zip code mismatch.</Message><Message>Phone Number could not be verified</Message>\n\t\t\t\t\t\t\t</Messages>\n\t\t\t\t\t\t</Phone><SSN><Input>123-33-4444</Input><Result>Failed</Result>\n\t\t\t\t\t\t</SSN><DOB><Input>1977-11-11</Input><Result>Failed</Result><Messages><Message>Date Of Birth could not be verified</Message>\n\t\t\t\t\t\t\t</Messages>\n\t\t\t\t\t\t</DOB><Zip><Input>55408</Input><Result>Failed</Result><Messages><Message>No match to zip+4 file</Message>\n\t\t\t\t\t\t\t</Messages>\n\t\t\t\t\t\t</Zip>\n\t\t\t\t\t</Results><Checks><NotOnOFACList><Result>Pass</Result>\n\t\t\t\t\t\t</NotOnOFACList><NotOnSuspectAddressList><Result>Pass</Result>\n\t\t\t\t\t\t</NotOnSuspectAddressList><NotOnSSADeathMasterFile><Result>Pass</Result>\n\t\t\t\t\t\t</NotOnSSADeathMasterFile><ValidUSPSAddress><Result>Fail</Result>\n\t\t\t\t\t\t</ValidUSPSAddress><DwellingType><Messages><Message>n/a</Message>\n\t\t\t\t\t\t\t</Messages>\n\t\t\t\t\t\t</DwellingType><ResidentialOrBusiness><Messages><Message>n/a</Message>\n\t\t\t\t\t\t\t</Messages>\n\t\t\t\t\t\t</ResidentialOrBusiness><LandUse><Messages><Message>n/a</Message>\n\t\t\t\t\t\t\t</Messages>\n\t\t\t\t\t\t</LandUse><HouseholdIncomeEstimate><Messages><Message>n/a</Message>\n\t\t\t\t\t\t\t</Messages>\n\t\t\t\t\t\t</HouseholdIncomeEstimate><NumberOfChildren><Messages><Message>n/a</Message>\n\t\t\t\t\t\t\t</Messages>\n\t\t\t\t\t\t</NumberOfChildren>\n\t\t\t\t\t</Checks><VelocityInformation><Phone><NumberOfTimesSeen>0</NumberOfTimesSeen>\n\t\t\t\t\t\t</Phone>\n\t\t\t\t\t</VelocityInformation>\n\t\t\t\t</IDInvestigationReport>"
      },
      "ContentType" : "text/xml"
    }, {
      "Extension" : {
        "any" : "<?xml version=\"1.0\" encoding=\"UTF-16\"?>\n<MERSReport><Input><SSN>123334444</SSN>\n\t\t\t\t\t</Input>\n\t\t\t\t</MERSReport>"
      },
      "ContentType" : "text/xml"
    }, {
      "Extension" : {
        "any" : "<?xml version=\"1.0\" encoding=\"UTF-16\"?>\n<IDInvestigationReport><ProcessDate>2018-08-13T16:47:14.215-04:00</ProcessDate><ReferenceNumber>SAMI_ADV120_1534193229528</ReferenceNumber><InputData><Name>JETHRO CCLAMPETT</Name><AddressLine1>12 Some Address</AddressLine1><AddressLine2/><City>SomeCity</City><State>ALABAMA</State><Zip>55408</Zip><SSN>222-01-4141</SSN><DateOfBirth>10/25/1966</DateOfBirth><Phone>(555) 666-3333(home) (612) 861-8619(business)</Phone>\n\t\t\t\t\t</InputData><Score><ValidationScore>99</ValidationScore><KeyDrivers><KeyDriver><Rank>1</Rank><Description>Social Issued Before DOB</Description>\n\t\t\t\t\t\t\t</KeyDriver><KeyDriver><Rank>2</Rank><Description>Social belongs to a deceased individual</Description>\n\t\t\t\t\t\t\t</KeyDriver><KeyDriver><Rank>3</Rank><Description>Phone reported only with different name(s) or Address(es)</Description>\n\t\t\t\t\t\t\t</KeyDriver><KeyDriver><Rank>4</Rank><Description>Name not reported at Address</Description>\n\t\t\t\t\t\t\t</KeyDriver><KeyDriver><Rank>5</Rank><Description>Phone Number could not be verified</Description>\n\t\t\t\t\t\t\t</KeyDriver>\n\t\t\t\t\t\t</KeyDrivers>\n\t\t\t\t\t</Score><Results><FirstName><Input>JETHRO</Input><Result>Pass</Result><DateSeen>08/01/2018</DateSeen>\n\t\t\t\t\t\t</FirstName><LastName><Input>CCLAMPETT</Input><Result>Pass</Result><DateSeen>07/28/2018</DateSeen>\n\t\t\t\t\t\t</LastName><Address><Input>12 Some Address</Input><Result>Failed</Result><Messages><Message>Address is invalid.</Message><Message>Name not reported at Address</Message>\n\t\t\t\t\t\t\t</Messages><DateSeen>07/28/2018</DateSeen><Alternate><Input>123 BEVERLY HILLS DR</Input><Result>Alternate</Result>\n\t\t\t\t\t\t\t</Alternate>\n\t\t\t\t\t\t</Address><Phone><Input>(555) 666-3333</Input><Result>Failed</Result><Messages><Message>Phone reported only with different First name(s)</Message><Message>Phone reported only with different Last name(s)</Message><Message>Phone reported only with different Address(es)</Message><Message>Phone reported only with different name(s) or Address(es)</Message><Message>Phone Number could not be verified</Message>\n\t\t\t\t\t\t\t</Messages><DateSeen>08/01/2018</DateSeen>\n\t\t\t\t\t\t</Phone><SSN><Input>222-01-4141</Input><Result>Pass</Result><Messages><Message>Deceased Identity Found with No Name Match</Message><Message>Social Issued Before DOB</Message><Message>Social belongs to a deceased individual</Message>\n\t\t\t\t\t\t\t</Messages><DateSeen>08/01/2018</DateSeen>\n\t\t\t\t\t\t</SSN><DOB><Input>1966-10-25</Input><Result>Close Match</Result><DateSeen>08/01/2018</DateSeen>\n\t\t\t\t\t\t</DOB><Zip><Input>55408</Input><Result>Failed</Result><Messages><Message>No match to zip+4 file</Message>\n\t\t\t\t\t\t\t</Messages>\n\t\t\t\t\t\t</Zip>\n\t\t\t\t\t</Results><Checks><NotOnOFACList><Result>Pass</Result>\n\t\t\t\t\t\t</NotOnOFACList><NotOnSuspectAddressList><Result>Pass</Result>\n\t\t\t\t\t\t</NotOnSuspectAddressList><NotOnSSADeathMasterFile><Result>Fail</Result>\n\t\t\t\t\t\t</NotOnSSADeathMasterFile><ValidUSPSAddress><Result>Fail</Result>\n\t\t\t\t\t\t</ValidUSPSAddress><DwellingType><Messages><Message>n/a</Message>\n\t\t\t\t\t\t\t</Messages>\n\t\t\t\t\t\t</DwellingType><ResidentialOrBusiness><Messages><Message>n/a</Message>\n\t\t\t\t\t\t\t</Messages>\n\t\t\t\t\t\t</ResidentialOrBusiness><LandUse><Messages><Message>n/a</Message>\n\t\t\t\t\t\t\t</Messages>\n\t\t\t\t\t\t</LandUse><HouseholdIncomeEstimate><Messages><Message>n/a</Message>\n\t\t\t\t\t\t\t</Messages>\n\t\t\t\t\t\t</HouseholdIncomeEstimate><NumberOfChildren><Messages><Message>n/a</Message>\n\t\t\t\t\t\t\t</Messages>\n\t\t\t\t\t\t</NumberOfChildren>\n\t\t\t\t\t</Checks><VelocityInformation><Phone><NumberOfTimesSeen>0</NumberOfTimesSeen>\n\t\t\t\t\t\t</Phone>\n\t\t\t\t\t</VelocityInformation>\n\t\t\t\t</IDInvestigationReport>"
      },
      "ContentType" : "text/xml"
    }, {
      "Extension" : {
        "any" : "<?xml version=\"1.0\" encoding=\"UTF-16\"?>\n<ForeclosureInvestigationReport><Input><Name><FirstName>Melvin</FirstName><LastName>Frost</LastName>\n\t\t\t\t\t\t</Name><SSN>123334444</SSN><DateOfBirth><day>11</day><month>11</month><year>1977</year>\n\t\t\t\t\t\t</DateOfBirth>\n\t\t\t\t\t</Input><Errors><Error><Message>The Social Security Number supplied is incorrect. Please verify and resubmit your order.</Message>\n\t\t\t\t\t\t</Error>\n\t\t\t\t\t</Errors>\n\t\t\t\t</ForeclosureInvestigationReport>"
      },
      "ContentType" : "text/xml"
    }, {
      "Extension" : {
        "any" : "<?xml version=\"1.0\" encoding=\"UTF-16\"?>\n<SsnVerification><RequestDetail><ProcessDate><Year>2018</Year><Month>8</Month><DayOfMonth>13</DayOfMonth><Hour>20</Hour><Minute>47</Minute><Second>13</Second><Millisecond>690</Millisecond>\n\t\t\t\t\t\t</ProcessDate>\n\t\t\t\t\t</RequestDetail><Input><Person><DisplayName>Melvin Frost</DisplayName><FirstName>Melvin</FirstName><MiddleName/><LastName>Frost</LastName><Ssn>123334444</Ssn><DateOfBirth><Year>1977</Year><Month>11</Month><DayOfMonth>11</DayOfMonth>\n\t\t\t\t\t\t\t</DateOfBirth><Address>2824 Pumpkin Avenue Minneapolis, MN 55408</Address><Phone>6128883322</Phone><AddressParsed><AddressLine1>2824 Pumpkin Avenue</AddressLine1><AddressLine2/><City>Minneapolis</City><State>MN</State><PostalCode>55408</PostalCode>\n\t\t\t\t\t\t\t</AddressParsed>\n\t\t\t\t\t\t</Person>\n\t\t\t\t\t</Input><Evaluation><Summary><SSNMiskeyDetected>Yes</SSNMiskeyDetected><AlternateSsnUsed>8</AlternateSsnUsed><SsvValidAndAssociatedWithName>No</SsvValidAndAssociatedWithName><SsnValid>Yes</SsnValid><SsnNotOnDeathMasterList>Yes</SsnNotOnDeathMasterList><SsnIssuedAfterDaterOfBirth>Yes</SsnIssuedAfterDaterOfBirth><SsnIssuedDate>Unknown Result</SsnIssuedDate><DateOfBirthMatch>No match</DateOfBirthMatch><FirstNameMatch>No</FirstNameMatch><MiddleInitialMatch>No</MiddleInitialMatch><LastNameMatch>No</LastNameMatch><AddressMatch>Not Performed</AddressMatch><PhoneMatch>No Input Data</PhoneMatch><MostRecentAlternatePhone/><NumberOfAddressesSeenLast90Days>0</NumberOfAddressesSeenLast90Days>\n\t\t\t\t\t\t</Summary><HistoricalUsage><UsageItem><DisplayName/><Address/><AddressDetail><HouseNumber/><PreDir/><StreetName/><StreetSuffix/><PostDir/><Apt/><City/><State/><County/><Zip/><PlusFour/>\n\t\t\t\t\t\t\t\t</AddressDetail><Phone/><MatchCode>0</MatchCode>\n\t\t\t\t\t\t\t</UsageItem>\n\t\t\t\t\t\t</HistoricalUsage>\n\t\t\t\t\t</Evaluation>\n\t\t\t\t</SsnVerification>"
      },
      "ContentType" : "text/xml"
    }, {
      "Extension" : {
        "any" : "<?xml version=\"1.0\" encoding=\"UTF-16\"?>\n<MERSReport><Input><SSN>222014141</SSN>\n\t\t\t\t\t</Input><Results><Record><Status>Active</Status><Id>999990200004000023</Id><Address><AddressLine1>5398 41WK VW DSW 6D</AddressLine1><City>ASTORIA</City><State>NY</State><PostalCode>11105</PostalCode><CountyFIPSCode>999</CountyFIPSCode><StateFIPSCode>36</StateFIPSCode><HouseNumber>5398</HouseNumber><StreetName>41WK VW DSW 6D</StreetName><StreetDirection/><StreetSuffix/><Unit/>\n\t\t\t\t\t\t\t</Address><NoteAmount>$400,000</NoteAmount><NoteDate><day>3</day><month>4</month><year>2009</year>\n\t\t\t\t\t\t\t</NoteDate><Name><FirstName>JETHRO</FirstName><LastName>CCLAMPETT</LastName>\n\t\t\t\t\t\t\t</Name><MOMIndicator>Y</MOMIndicator><RegistrationDate><day>1</day><month>3</month><year>2010</year>\n\t\t\t\t\t\t\t</RegistrationDate><Servicer>PNC Bank, N.A.</Servicer><ServicerOrgID>1002320</ServicerOrgID><MINArchivedIndicator>N</MINArchivedIndicator><IRegistrationIndicator>N</IRegistrationIndicator>\n\t\t\t\t\t\t</Record>\n\t\t\t\t\t</Results>\n\t\t\t\t</MERSReport>"
      },
      "ContentType" : "text/xml"
    }, { /* trimmed for brevity */ }, 
      { 
      "Document" : "JVBERi0xLjUKJd/++LIKMSAwIG9iago8PC9UeXBlL0NhdGFsb2cvUGFnZXMgMyAwIFIvTGFuZyhlbi1VUykvTmFtZXMgNCAwIFIvTWV0YWRhdGEgNSAwIFI+PgplbmRvYmoKNSAwIG9iago8PC9UeXBlL01ldGFkYXRhL1N1YnR5cGUvWE1ML0xlbmd0aCAxMDc2Pj5zdHJlYW0NCjw/eHBhY2tldCBiZWdpbj0n77u/JyBpZD0nVzVNME1wQ2VoaUh6cmVTek5UY3prYzlkJz8+Cjw/eHBhY2tldCBiZWdpbj0n77u/JyBpZD0nVzVNME1wQ2VoaUh6cmVTek5UY3prYzlkJz8+PHg6eG1wbWV0YSB4bWxuczp4PSJhZG9iZTpuczptZXRhLyIgeDp4bXB0az0iQkZPWE1QIDEuMS42Mi1yMjYyMzIiPjxyZGY6UkRGIHhtbG5zOnJkZj0iaHR0cDovL3d3dy53My5vcmcvMTk5OS8wMi8yMi1yZGYtc3lu.....",
      "Classifier" : "report",
      "ContentDisposition" : "inline; filename=\"ADV120Test\"; creation-date=\"Mon, 13 Aug 2018 16:47:28 EDT\"; modification-date=\"Mon, 13 Aug 2018 16:47:28 EDT\"; read-date=\"Mon, 13 Aug 2018 16:47:29 EDT\"; size=182184",
      "ContentType" : "application/pdf" }
    ]
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
  "Ref" : "https://api.pointservices.com/riskinsight-services-ws/resources/v1/sami/0000000000001857105"
}
```

After submitting, you may have to wait a few minutes for the request to finish processing.  When the request has returned, the
results of the submission will be in the `Messages.Message.Code` section.  A `Status.Code` of `S001` indicates that the
order was a billable transaction.  


You can use the url provided in the `Ref` field to retrieve the order results again.



Make note of the `TransactionID` that was returned.  Especially if you plan to order a refresh later.



### Step 3: Ordering a Refresh 

{: .before_starting }
Get the REFRESH URL from your Pitchpoint Account Representative.

Before you're able to order the refresh, you must first get the `OrderGuid` of the transaction that you just ordered.  
Refer to [Querying](/api/order/query) for more information.  A sample query might be:   

```bash
curl -X POST https://api.pointservices.com/riskinsight-services-ws/resources/v1/orders/query 
-H "Authorization: Bearer your_access_token_here" 
-H "Content-Type: application/json" 

{
    "filters": {
        "ReferenceNumber": {
            "eq": "SAMI_ADV120_1534193229528"
        }
    }
}

```

Use the `CorrelationID` from the previous request to populate the `ReferenceNumber` field.  


#### Example response


```json
{
  "count": 1,
  "filters": {
    "ReferenceNumber": {
      "ge": "SAMI_ADV120_1534193229528"
    }
  },
  "limit": 100,
  "results": [
    {
      "Completed": "2024-04-09T18:50:06.000Z",
      "Created": "2024-04-09T18:50:03.000Z",
      "Deleted": false,
      "Fulfilled": "2024-04-09T18:50:03.000Z",
      "OrderGuid": "0000000000014623826",
      "OrderSummary": "Melvin Frost & Jethro Cclamptett",
      "OwnerCompanyName": "My Company",
      "OwnerUserContactMechanism": "sample.user@user.com",
      "OwnerUserDisplayName": "User, Sample",
      "ProductDisplayName": "ADV Enterprise",
      "ProductType": "ADV",
      "ReferenceNumber": "SAMI_ADV120_1534193229528",
      "ReportAvailable": true,
      "Status": "complete"
    }
  ]
}

```

If the `ReferenceNumber` was not unique, you may get multiple results.  Select the `OrderGuid` from the result that you want to refresh.


Add the `TranasactionID` to the original request and make any additional modifications to the request as required.  

Using the `OrderGuid` retrieved from the previous request, order the refresh using the modified original request.   

```bash
# Note: We've added the `TransactionID` to the request, since we are telling the system that we wish to target the 
# refresh on the `0000000000001857105` loan.   
# Note: We've added a middle name to Melvin Frost
# Note: All other data is the same as the original request.  Any data that is not submitted, is considered _removed_.
# Note: `OrderGuid` is used in the URL as the last part of the path.

curl -X POST https://api.pointservices.com/riskinsight-services-ws/resources/v1/sami/ADV/refresh/0000000000014623826
-H "Authorization: Bearer your_access_token_here" 
-H "Content-Type: application/json" 
-H "Accept: application/json" 
-d '{
  "TransactionID" : "0000000000001857105",
  "CorrelationID" : "SAMI_ADV120_1534192619544",
  "Terms" : {
    "Term" : [ {
      "Person" : {
        "FirstName" : "Melvin",
        "MiddleName" : "A",
        "LastName" : "Frost",
        "SSN" : "123334444",
        "DOB" : "11/11/1977",
        "HomePhone" : "6128883322",
        "Residences" : {
          "Residence" : [ {
            "Address" : {
              "AddressLine1" : "2824 Pumpkin Avenue",
              "City" : "Minneapolis",
              "State" : "mn",
              "PostalCode" : "55408"
            },
            "CurrentIndicator" : true
          } ]
        },
        "Employers" : {
          "Employer" : [ {
            "Name" : "Google",
            "Phone" : "650 253 0000",
            "Address" : {
              "AddressLine1" : "1600 Amphitheatre Parkway",
              "City" : "Mountain View",
              "State" : "CA",
              "PostalCode" : "94043"
            },
            "CurrentIndicator" : true
          } ]
        }
      }
    }, {
      "Person" : {
        "FirstName" : "JETHRO",
        "LastName" : "CCLAMPETT",
        "SSN" : "222014141",
        "DOB" : "10/25/1966",
        "HomePhone" : "5556663333",
        "Residences" : {
          "Residence" : [ {
            "Address" : {
              "AddressLine1" : "12 Some Address",
              "City" : "SomeCity",
              "State" : "AL",
              "PostalCode" : "55408"
            },
            "CurrentIndicator" : true
          } ]
        },
        "Employers" : {
          "Employer" : [ {
            "Name" : "Thornton & Thornton",
            "Phone" : "612 861 8619",
            "Address" : {
              "AddressLine1" : "7128 James Ave S",
              "City" : "Minneapolis",
              "State" : "MN",
              "PostalCode" : "55423"
            },
            "CurrentIndicator" : true
          } ]
        }
      }
    }, {
      "Property" : {
        "Address" : {
          "AddressLine1" : "210 PeachTree Rd",
          "City" : "Oakboro",
          "State" : "nc",
          "PostalCode" : "28129"
        }
      }
    } ]
  }
}

    
```` 

## Other Examples
### An example of everything available to be set in a request


```bash
curl -X POST https://api.pointservices.com/riskinsight-services-ws/resources/v1/sami/ADV/ADV-120J
-H "Authorization: Bearer your_access_token_here" 
-H "Content-Type: application/json" 
-d '{
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
}
'
```




