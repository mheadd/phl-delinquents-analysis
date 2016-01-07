# PHL Delinquent Analysis

An analysis of tax delinquent properties in Philadelphia that were issued permits by the Department of Licenses & Inspections (L&I).

## Overview

With the recently released data on [property tax balances](https://www.opendataphilly.org/dataset/property-tax-balances) from the City of Philadelphia, it is now possible to examine - using open data APIs from the city - instances where properties with an outstanding property tax balance are issued permits from L&I.

This analysis complements [an earlier study done by the City of Philadelphia itself](https://github.com/mheadd/licensing-delinquents) which suggests that problems exist with issuing various permits and licenses from L&I for properties with an outstanding property tax balance.

## Methodology

This analysis looks at a subset of records on property tax balances available from the City of Philadelphia. Specifically, this analysis looks at properties with a tax balance record in the open data file for 2014 (the year when the city began requiring a [manual tax clearance process](https://secure.phila.gov/revenue/TaxCompliance/) for permit applications) that were issued a permit from L&I in 2014 or 2015.

Within this subset of properties (totaling 67,475), an analysis was conducted for only those properties that had an active lien for 2014 taxes and a total outstanding balance (principal, interest, penalty & other) of greater than $1,000. 

The final number of properties examined in this analysis was 20,403. Two City of Philadelphia APIs were utilized for this analysis:

* [ULRS API](http://api.phila.gov/ULRS311/): This API is used to convert the raw address from the property tax balance file to an L&I Address Key.
* [L&I Permit API](http://phlapi.com/licenseapi.html): This API is used to look up permits for a location based on an L&I Address Key.

## Findings and Caveats

This analysis found 783 properties with an active lien and an outstanding property tax balance in 2014 of more than $1,000 that had been issued one or more permits from L&I between January 1, 2014 and December 31, 2015.

Of these properies, the majority (546) received only 1 permit. 96 percent of properties were issued 5 or fewer permits during the time period examined. [One property](https://data.phila.gov/resource/y5ti-svsu.json?location=1801%20JOHN%20F%20KENNEDY%20BLVD), with a total outstanding tax liability for 2014 of **$126,301.55** was issued 105 permits. <sup>1</sup>

Of the 20,403 delinquent properties examined, the 783 properties that were issued permits represents a rate of roughly 4 percent. However, it is likely that this analysis is undercounting instances where permits are issued for properties with outstanding tax balances.

* This analysis does not include properties with an outstanding tax balance in 2014 of less than $1,000.
* This analysis does not include properties with an outstanding tax balance from years prior to 2014 that may have been issued a permit.
* It appears that L&I’s system may - in some instances - use [more than one Location ID for a single property](https://groups.google.com/forum/?fromgroups#!topic/opendataphilly/UdXIzwz7FQI), so the methodology used in this analysis may undercount the number of permits issued.
* This analysis does not include other types of issuances from L&I, like [rental licenses](http://www.phila.gov/li/pages/tenantlandlord.aspx).

With these caveats in mind, a conservative estimate of the actual occurance of properties with an outstanding tax balance being issued a license or permit from L&I is probably somewhere between 5-10 percent.

## Description of Data

```2014-Philadelphia-Property-Tax-Liabilities.csv```: The initial file of 67,475 records with an entry in the Property Tax Balance Data Set for 2014. The layout of this file matches what can be downloaded from OpenDataPhilly.org. This file is a subset of the complete file released by the city.  

```2014-Philadelphia-Property-Tax-Liabilities-With-Permits.csv```: File showing properties with an active lien and an outstanding 2014 property tax balance of more than $1,000 that were issued 1 or more permits between 2014 and 2015.

| Field name| Description |   
|---|---|
| Parcel Number | The property identification number, also known as the OPA or BRT number | 
| Owner |  The "Owner 1" field from the OPA Property Assessments dataset |  
| Location |  The address of the property, joined from the OPA Property Assessments dataset | 
| Official Address | The address returned by the ULRS L&I Key lookup | 
| TopicID |  The L&I Address Key | 
| Permit Summary | A JSON array with basic details of permits issued for this property (additional details below) | 
| Number of permits | A count of the number of permits issued for this property between January 1, 2014 and December 31, 2015 | 
| Unit |  Unit number of the property, if exists | 
| Tax Period | The tax year examined - 2014 in all cases  | 
| Principal | The principal amount of the balance  | 
| Interest | The interest amount of the balance  | 
| Penalty | The penalty amount of the balance  | 
| Other | Other charges associated with the balance  | 
| Total | A sum of the principal, interest, penalty, and other fields on the record reflecting the total owed for that tax period on that parcel  | 
| Lien Number | Lien number or docket number  | 
| Coordinates |  Latitude and longitude for the property | 

An example of the ```Permit Summary``` field can be seen below.

```json
[{
	"permit_number": "531363",
	"permit_type_code": "EZELEC",
	"permit_type_name": "ELE-EZ ELECTRICAL",
	"status": "COMPLETED",
	"issued_datetime": "/Date(1398084060000)/",
	"updated_datetime": "/Date(1402590790000)/"
}, {
	"permit_number": "563261",
	"permit_type_code": "EZPLUM",
	"permit_type_name": "PLU-EZ PLUMBING",
	"status": "COMPLETED",
	"issued_datetime": "/Date(1411459399000)/",
	"updated_datetime": "/Date(1415006169000)/"
}]
```

This information is a [subset of the response from the L&I API](http://services.phila.gov/PhillyApi/Data/v1.0/locations(669010)/permits?$filter=(issued_datetime%20gt%20datetime%272014-01-01%27)%20and%20(issued_datetime%20lt%20datetime%272015-12-31%27)&$format=json&$expand=locations) for this specific property. 

1. Note - total balances may change slightly from day to day as interest and penalties accumulate. The figure used here was the outstanding liability at the time of this analysis and is reflected in the datasets. The link to the Property Tax Balance API may show a different amount.

