# USPS API
The USPS Developer Portal is for software developers who would like to integrate Postal Service functionality into their ecommerce, shipping, or logistics systems. Access and use USPS tools and resources conveniently within your site or application.

## Getting Started
To use the USPS APIs you must be a registered user.
1. Apply for a free USPS.com account.
2. Access the [Business Customer Gateway](https://gateway.usps.com/eAdmin/view/signin) and follow the prompts to create a USPS.com business account.
3.	Using your USPS account, access the [USPS Developer Portal](htps://developer.usps.com) by clicking the Sign-Up button. 
4.	Registering your application and retrieve your consumer key and consumer secret from the credential section.
5.	Additionally you will need to have a Customer ID (CRID) and Mailer ID (MID)


## OAuth
All USPS APIs require an OAuth 2.0 access token to be conveyed in the Authorization header, using the Bearer token scheme.  To retrieve your Bearer Token you will need the consumer key(client_id), consumer secret(client_secret), customer_registration_id(CRID), and mailer_id(MID).

Prepare your credentials for use in the following examples:
```sh
CLIENT_ID=XXXX
CLIENT_SECRET=XXXX
CUSTOMER_REGISTRATION_ID=XXXX
MAILER_ID=XXXX
```

Example OAuth token request:
```sh
curl "https://api.usps.com/minimum-oauth-implementation/token" \
     -H 'Content-Type:application/x-www-form-urlencoded;charset=utf-8' \
     --data-urlencode "grant_type=client_credentials" \
     --data-urlencode "client_id=$CLIENT_ID" \
     --data-urlencode "client_secret=$CLIENT_SECRET" \
     --data-urlencode "customer_registration_id=$CUSTOMER_REGISTRATION_ID" \
     --data-urlencode "mailer_id=$MAILER_ID"
```
```json
{
    "access_token": "XXXXXXXXXXXXXXXXX",
    "token_type": "Bearer",
    "issued_at": "111111111111111",
    "expires_in": "111",
    "status": "approved",
    "scope": "addresses service-delivery-standards  locations prices tracking labels",
    "issuer": "usps.com",
    "client_id": "XXXXXXXXXXXXXXXXXXXXXXXXXX",
    "application_name": "XXXXXX-XXXX-XXXX-XXX-XXXXXXXXXXXX",
    "api_products": "[XXXXXXX]",
    "customer_registration_id": "XXXXXXXX",
    "mailer_id": "XXXXXXXXX",
    "user_id": "XXXXXXXXXXXX"
}
```

Set your access token to an environment variable for use in subsequent requests.
```sh
export $TOKEN=<access_token>
```

Inspect the values associated with the OAuth access token profile:
```sh
curl "https://api.usps.com/minimum-oauth-implementation/introspect" \
     -H 'Content-Type:application/x-www-form-urlencoded;charset=utf-8' \
     -u "$CLIENT_ID:$CLIENT_SECRET" \
     --data-urlencode "token_type_hint=accesstoken" \
     --data-urlencode "token=$TOKEN"
```
```json
{
    "access_token": "XXXXXXXXXXXXXXXXX",
    "token_type": "Bearer",
    "issued_at": "111111111111111",
    "expires_in": "111",
    "status": "approved",
    "scope": "addresses service-delivery-standards  locations prices tracking labels",
    "issuer": "usps.com",
    "client_id": "XXXXXXXXXXXXXXXXXXXXXXXXXX",
    "application_name": "XXXXXX-XXXX-XXXX-XXX-XXXXXXXXXXXX",
    "api_products": "[XXXXXXX]",
    "customer_registration_id": "XXXXXXXX",
    "mailer_id": "XXXXXXXXX",
    "user_id": "XXXXXXXXXXXX"
    "account_type": "EPS",
    "account_number": "XXXXXXXXXX",
    "permit_zip": "",
    "permit_number": ""
}
```

## Address
This API is to be used to proactively verify and fix addresses early in the shipment process so that deliveries are as accurate as possible. This API provides resources to lookup ZIP codes, the city and state represented by a ZIP code, and to verify and standardize an address prior to shipment.
```sh
curl --get "https://api.usps.com/addresses/v1/address" \
     -H "accept: application/json"  \
     -H "authorization: Bearer $TOKEN" \
     --data-urlencode "Address1=29851 Aventura" \
     --data-urlencode "AddressLine2=SUITE K" \
     --data-urlencode "State=CA" \
     --data-urlencode "Zip5=92688"
```
```json
{
    "address": {
        "Address1": "4120 BINGHAM AVE",
        "City": "SAINT LOUIS",
        "State": "MO",
        "Zip5": "63116",
        "Zip4": "2520",
        "ZipCode": "63116-2520"
    },
    "addressAdditionalInfo": {
        "DeliveryPoint": "20",
        "CarrierRoute": "C014",
        "DPVConfirmation": "Y",
        "DPVCMRA": "N",
        "Business": "N",
        "CentralDeliveryPoint": "N",
        "Vacant": "Y"
    },
    "addressCorrections": null,
    "addressMatches": null
}
```

## Location
This API supports returning destination entry facilities (i.e., drop off locations) for a given ZIP Code to enable Parcel Select Destination Entry, USPS Connect, and Parcel Select Lightweight. This API also provides additional information about working hours, delivery type, mail class, shape, palletization, and facility type.
```sh
curl "https://api.usps.com/locations/v1/dropOffLocations?destinationZIP=52228" \
     -H "accept: application/json" \
     -H "Authorization: Bearer $TOKEN"
```
Response:
[dropoff-location-response.json](dropoff-location-response.json)


## Prices
This API provides USPS product pricing based on the characteristics of what is being shipped. Given inputs, such as origin and destination ZIP Codes, package weight and dimensions, processing category, destination rate indicator, price type, and desired Mail Class and Extra Services, this API will return the price of postage. 

### Base Rates Request

Save the example request body to a file: [prices-rate-request.json](https://github.com/USPS/api-examples/blob/main/price-rate-request.json)
```sh
curl "https://api.usps.com/prices/external/v1/baseRates/search" \
     -H "Content-Type: application/json" \
     -H "Accept: application/json" \
     -H "Authorization: Bearer $TOKEN" \
     --data-binary @prices-rate-request.json \
```
```json
{
    "rates": [
        {
            "rate": {
                "rateId": null,
                "sku": "DVXP0XXXXC01020",
                "contractType": "CB",
                "rate": 7.28,
                "gla": "41419",
                "aic": "211",
                "maxWeight": 2,
                "fees": [],
                "startDate": "2022-01-09",
                "endDate": ""
            },
            "rateRef": https://api.usps.com/prices/v1/baseRates/DVXP0XXXXC01020,
            "skuRef": https://api.usps.com/prices/v1/baseSkus/DVXP0XXXXC01020
        }
    ]
}
```
### Extra Services Rates Request
Save the example request body to a file: [prices-extra-services-request.json](https://github.com/USPS/api-examples/blob/main/prices-extra-services-request.json)

```sh
curl "https://api.usps.com/prices/external/v1/extraServiceRates/search" \
     -H "Content-Type: application/json" \
     -H "Accept: application/json" \
     -H "Authorization: Bearer $TOKEN" \
     --data-binary @prices-extra-services-request.json
```
```json
{
    "sku": "DXRX0XXXXCX0000",
    "rate": 3.05,
    "contractType": "CB",
    "extraServiceCode": "955",
    "description": "Return Receipt"
}
```

## Label
This API supports creation of domestic shipping labels with Intelligent Mail Package Barcodes (IMpb). Along with label and barcode creation in both PDF and JPEG format, validates addresses, confirms product availability, calculates postage, and generates the Shipping Services File in accordance with USPS Publication 199 required for shipping with USPS. This label API currently supports Parcel Select, Parcel Select Lightweight, Connect Local, Connect Regional, Priority Mail, Priority Mail Express, First Class Package Services, Bound Printed Matter, Library Mail, and Media Mail mail classes.  This API requires payment to be added to the Bearer Token

Set the payment account for Label:
```sh
curl "https://api.usps.com/labels/v1/payment" \
     -H "Content-Type: application/json" \
     -d "{\"accountType\":\"EPS\",\"accountNumber\":\"XXXXXXXXX\"}" \
     -H "authorization: Bearer $TOKEN"
```
```json
{
    "account_type": "EPS",
    "account_number": "xxxxxxxxxx",
    "permit_number": "",
    "permit_zip": ""
} 
```

### Label Request
Save the example request body to a file: [label-request.json](https://github.com/USPS/api-examples/label-request.json)
```
curl "https://api.usps.com/labels/v1/label" \
     -H "accept: application/json" \
     -H "Content-Type: application/json" \
     -H "authorization: Bearer $TOKEN" \
     --data-binary @label-request.json \
     
```
Response: [label-response.json](https://github.com/USPS/api-examples/label-response.json)


## Tracking
This API returns tracking status and related details for a given USPS package, including scan events and their date, time, and location. Provides the latest status and delivery expectations.  

### Single Tracking Request
```sh
curl "https://api.usps.com/tracking/v1/tracking/XXXXXXXXXXXX?expand=detail" \ 
     -H "accept: application/json" \
     -H "Content-Type: application/json" \
     -H "Authorization: Bearer $TOKEN" 
```
```json
{"TrackResults":
		{"RequestSeqNumber":null,
		"TrackInfo":{"@ID":"9305510923000967979600",
		"Class":"Priority Mail<SUP>&reg;<\/SUP>",
		"ClassOfMailCode":"PM",
		"DestinationCity":"CLARENCE",
		"DestinationState":"NY",
		"DestinationZip":"14031",
		"EmailEnabled":"true",
		"KahalaIndicator":"false",
		"MailTypeCode":"DM",
		"MPDATE":"2022-01-07 14:19:57.000000",
		"MPSUFFIX":"725949550",
		"OriginCity":"PORTLAND",
		"OriginState":"OR",
		"OriginZip":"97215",
		"PodEnabled":"false",
		"TPodEnabled":"false",
		"RestoreEnabled":"false",
		"RramEnabled":"false",
		"RreEnabled":"false",
		"Service":["USPS Tracking<SUP>&#174;<\/SUP>", "Up to $100 insurance included"],
		"ServiceTypeCode":"055",
		"Status":"USPS in possession of item",
		"StatusCategory":"Accepted",
		"StatusSummary":"USPS is now in possession of your item as of 12:19 pm on January 7, 2022 in PORTLAND, OR 97215.",
		"TABLECODE":"T",
		"TrackSummary":{"EventTime":"12:19 pm",
		"EventDate":"January 7, 2022",
		"Event":"USPS in possession of item",
		"EventCity":"PORTLAND",
		"EventState":"OR",
		"EventZIPCode":"97215",
		"EventCountry":null,
		"FirmName":null,
		"Name":null,
		"AuthorizedAgent":"false",
		"EventCode":"03"}}}
}
```
### Email Notification Request
Save the example request body to a file: [tracking.txt](https://github.com/USPS/api-examples/blob/main/tracking.txt)
```sh
curl "https://api.usps.com/tracking/v1/tracking/XXXXXXXXXXXX/notifications" \
     -H "accept: application/json" \
     -H "Content-Type: application/json" \
     --data-binary "@tracking.txt"\
     -H "Authorization: Bearer $TOKEN" 
     ```
```json
{
	"PTSEMAILRESULT":{
	"ResultText":"When new tracking activity is available, 	you'll get notifications based on your selections.",
	"ReturnCode":"0"}
}
```

## Service Standards
This API supports service standard for the number of days between the acceptance and delivery of a piece of mail that the Postal Service™ considers to be timely delivery. Service standards are delivery benchmarks for how long customers can expect for the Postal Service to deliver different types of mail from origin to destination — Point A to Point B. Service standards are not necessarily the same as the actual service performance.

### Service Standards Request
```sh
curl "https://api.usps.com/sdc-services/v1/standards?originZipCode=40504&destinationZipCode=95823&mailClass=PRIORITY_MAIL&destType=HOLD_FOR_PICKUP" \ 
     -H "accept: application/json" \
     -H "Authorization: Bearer $TOKEN"
```
```json
{
    "standards": {
        "mailClass": "PRIORITY_MAIL",
        "originZipCode": "40504",
        "destinationZipCode": "95823",
        "days": "2",
        "effectiveAcceptanceDate": "2022-05-10",
        "scheduledDeliveryDate": "2022-05-12"
    }
}
```
### Service Delivery Calculator
```sh
curl "https://api.usps.com/sdc-services/v1/estimates?originZipCode=40504&destinationZipCode=95823&acceptDate=2022-05-11&mailClass=PRIORITY_MAIL&destType=HOLD_FOR_PICKUP" \
     -H "accept: application/json" \
     -H "Authorization: Bearer $TOKEN"
```
```json
{
    "standards": {
        "mailClass": "PRIORITY_MAIL",
        "destType": "HOLD_FOR_PICKUP",
        "acceptanceDateTime": "2022-05-11",
        "effectiveAcceptanceDate": "2022-05-11",
        "cutOffTime": "1700",
        "serviceStandard": "2",
        "serviceStandardMessage": "2-Day",
        "#text": "-->\n            \n            ",
        "acceptanceLocations": {
            "facilityType": "POST OFFICE",
            "addressLine1": "1729 ALEXANDRIA DR",
            "city": "LEXINGTON",
            "state": "KY",
            "zip5": "40504",
            "zip4": "9998"
        },
        "delivery": {
            "scheduledDeliveryDateTime": "2022-05-13",
            "holdForPickupLocation": {
                "facilityName": "PARKWAY",
                "addressLine1": "4301 BROOKFIELD DR",
                "city": "SACRAMENTO",
                "state": "CA",
                "zip5": "95823",
                "zip4": "9998",
                "closeTimes": {
                    "m": "1700",
                    "tu": "1700",
                    "w": "1700",
                    "th": "1700",
                    "f": "1700",
                    "sa": "1500",
                    "su": "0000",
                    "hol": "0000"
                }
            }
        }
    }
}
```
## Postman Collection
Here is a Postman Collection of the above curl commands above that you can utilize to help in getting a jump start of using USPS APIs. 

Example postman collection file: [Example postman collection.json](https://github.com/USPS/api-examples/Example-Postman.postman_collection.json)
