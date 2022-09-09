# USPS API
The USPS Developer Portal is for software developers who would like to integrate Postal Service functionality into their ecommerce, shipping, or logistics systems. Access and use USPS tools and resources conveniently within your site or application. You have the ability to try out the APIs in the our Test environment.

## Getting Started in Test Environment
To use the USPS APIs in the Test Environment uou must be a registered user.
1. Apply for a free USPS.com account.
2. Access the [Test Business Customer Gateway](https://gateway-cat.usps.com/eAdmin/view/signin) and follow the prompts to create a USPS.com business account.
3.	Using your USPS account, access the [USPS Test Developer Portal](htps://developer-cat.usps.com) by clicking the Sign-Up button. 
4.	Registering your application and retrieve your consumer key and consumer secret from the credential section.
5.	Additionally you will need to have a Customer ID (CRID) and Mailer ID (MID)

## Getting Started in Production
To use the USPS APIs you must be a registered user.
1. Apply for a free USPS.com account.
2. Access the [Business Customer Gateway](https://gateway.usps.com/eAdmin/view/signin) and follow the prompts to create a USPS.com business account.
3.	Using your USPS account, access the [USPS Developer Portal](htps://developer.usps.com) by clicking the Sign-Up button. 
4.	Registering your application and retrieve your consumer key and consumer secret from the credential section.
5.	Additionally you will need to have a Customer ID (CRID) and Mailer ID (MID)

## Using the APIs in the USPS Test Environment
All USPS APIs listed below are Production URL.  To use the API in Test Environment you need to change the URL from https://api.usps.com to https://api-cat.usps.com.

## OAuth
All USPS APIs require an OAuth 2.0 access token to be conveyed in the Authorization header, using the Bearer token scheme.  To retrieve your Bearer Token you will need the consumer key(client_id), consumer secret(client_secret), customer_registration_id(CRID), and mailer_id(MID).

Prepare your credentials for use in the following examples:
```sh
CLIENT_ID=XXXX
CLIENT_SECRET=XXXX
CUSTOMER_REGISTRATION_ID=XXXX
MAILER_ID=XXXX
```

### Example OAuth token request:
```sh
curl "https://api.usps.com/oauth2/v1/token" \
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

###  Inspect the values associated with the OAuth access token profile:
```sh
curl "https://api.usps.com/oauth2/v1/introspect" \
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

## Addresses

###  Address
This API is to be used to proactively verify and fix addresses early in the shipment process so that deliveries are as accurate as possible. This API provides resources to lookup ZIP codes, the city and state represented by a ZIP code, and to verify and standardize an address prior to shipment.
```sh
curl --get "https://api.usps.com/addresses/v1/address" \
     -H "accept: application/json"  \
     -H "authorization: Bearer $TOKEN" \
     --data-urlencode "streetAddress=4120 BINGHAM AVE" \
     --data-urlencode "city=Saint Louis" \
     --data-urlencode "state=MO" \
     --data-urlencode "ZIPCode=63116"
```
```json
{
    "firm": null,
    "address": {
        "streetAddress": "4120 BINGHAM AVE",
        "streetAddressAbbreviation": null,
        "secondaryAddress": null,
        "city": "SAINT LOUIS",
        "cityAbbreviation": null,
        "state": "MO",
        "postalCode": null,
        "province": null,
        "ZIPCode": "63116",
        "ZIPPlus4": "2520",
        "urbanization": null,
        "country": null,
        "countryISOCode": null
    },
    "addressAdditionalInfo": {
        "deliveryPoint": "20",
        "carrierRoute": "C014",
        "DPVConfirmation": "Y",
        "DPVCMRA": "N",
        "business": "N",
        "centralDeliveryPoint": "N",
        "vacant": "Y"
    },
    "addressCorrections": null,
    "addressMatches": null
}
```
###  City and State
Returns city and state corresponding to a given ZIP code.
```sh
curl --get "https://api.usps.com/addresses/v1/city-state" \
     -H "accept: application/json"  \
     -H "authorization: Bearer $TOKEN" \
     --data-urlencode "ZIPCode=63116"
```
```json
{
    "city": "SAINT LOUIS",
    "state": "MO",
} 
```
###  ZIPCode
Returns ZIPCode and ZIPPlus4 corresponding to a given address, city, and state
```sh
curl --get "https://api.usps.com/addresses/v1/zipcode" \
     -H "accept: application/json"  \
     -H "authorization: Bearer $TOKEN" \
     --data-urlencode "streetAddress=520 MARYVILLE CENTRE DR"\
     --data-urlencode "city=Saint Louis" \
     --data-urlencode "state=MO" 
```
```json
{
    "firm": null,
    "address": {
        "streetAddress": "520 MARYVILLE CENTRE DR",
        "streetAddressAbbreviation": null,
        "secondaryAddress": null,
        "city": "SAINT LOUIS",
        "cityAbbreviation": null,
        "state": "MO",
        "postalCode": null,
        "province": null,
        "ZIPCode": "63141",
        "ZIPPlus4": "5820",
        "urbanization": null,
        "country": null,
        "countryISOCode": null
    }
}
```

## Location
This API supports returning destination entry facilities (i.e., drop off locations) for a given ZIP Code to enable Parcel Select Destination Entry, USPS Connect, and Parcel Select Lightweight. This API also provides additional information about working hours, delivery type, mail class, shape, palletization, and facility type.
```sh
curl "https://api.usps.com/locations/v1/dropoff-locations?destinationZIPCode=63116&mailClass=USPS_CONNECT_LOCAL" \
     -H "accept: application/json" \
     -H "Authorization: Bearer $TOKEN"
```
Response:
[dropoff-location-response.json](https://github.com/USPS/api-examples/blob/main/dropoff-location-response.json)


## Domestic Prices
This API provides Domestic USPS product pricing based on the characteristics of what is being shipped for Parcel Select, Parcel Select Lightweight, Priority Mail, Priority Mail Express, First Class Package Services, Library Mail, Media Mail, and Bound Printed Matter.  Given inputs, such as origin and destination ZIP Codes, package weight and dimensions, processing category, destination rate indicator, price type, and desired Mail Class and Extra Services, this API will return the price of postage.  

### Domestic Prices - Base Rates Request
```sh
curl "https://api.usps.com/prices/v1/base-rates/search" \
     -H "Content-Type: application/json" \
     -H "Accept: application/json" \
     -H "Authorization: Bearer $TOKEN" \
     --data-raw '{
		"searchType":"SSF",
		"originZIPCode":"22407",
		"destinationZIPCode":"63118",
		"weight":"1.2",
		"length":"1",
		"width":"1",
		"height":"2",
		"mailClass":"PARCEL_SELECT",
		"processingCategory":"MACHINABLE",
		"destinationEntryFacilityType":"NONE",
		"rateIndicator":"SP",
		"parcelRoutingBarcode":"0",
		"isPoundBased":"N",
		"priceType":"COMMERCIAL",
		"mailingDate":"2022-08-01"
		}'
```
```json
[
    {
        "rateId": null,
        "SKU": "DVXP0XXXXC05020",
        "contractType": "CB",
        "price": 8.22,
        "maxWeight": 2,
        "fees": [],
        "startDate": "",
        "endDate": ""
    }
]
```
### Domestic Prices - Extra Services Rates Request
```sh
curl "https://api.usps.com/prices/v1/extra-service-rates/search" \
     -H "Content-Type: application/json" \
     -H "Accept: application/json" \
     -H "Authorization: Bearer $TOKEN" \
	--data-raw '{
		"extraService":"930",
		"mailClass":"PARCEL_SELECT",
		"priceType":"COMMERCIAL",
		"weight":1.0,
		"mailingDate":"2022-08-01",
		"itemValue":99
	}'
```
```json
[
    {
        "rateId": "",
        "SKU": "DXIX0XXXXCX0100",
        "price": 3.35,
        "contractType": "CB"
    }
]
```
## International Prices
The International Prices API provides International USPS product pricing based on the characteristics of what is being shipped for Global Express Guaranteed, Priority Mail Express International, Priority Mail International, and First-Class Package International. Given inputs, such as origin and destination ZIP Codes, package weight and dimensions, processing category, destination rate indicator, price type, and desired Mail Class and Extra Services, this API will return the price of postage and Extra Services.

### International Prices - Base Rates Request
```sh
curl "https://api.usps.com/international-prices/v1/base-rates/search" \
     -H "Content-Type: application/json" \
     -H "Accept: application/json" \
     -H "Authorization: Bearer $TOKEN" \
     --data-raw '{
		"searchType":"SSF",
		"originZIP":"22407",
		"weight":1.2,
		"length":10,
		"width":6,
		"height":4,
		"mailClass":"PRIORITY_MAIL_INTERNATIONAL",
		"processingCategory":"MACHINABLE",
		"destinationEntryFacilityType":"INTERNATIONAL_SERVICE_CENTER",
		"rateIndicator":"SP",
		"parcelRoutingBarcode":"0",
		"isPoundBased":"N",
		"priceType":"COMMERCIAL",
		"mailingDate":"2022-08-10",
		"foreignPostalCode":"A0Z9Z0",
		"destinationCountryCode":"CA"
		}'
```
```json
[
    {
        "rateId": null,
        "SKU": "CPXX0XXXXB03020",
        "contractType": "CB",
        "price": 40.46,
        "maxWeight": 2,
        "fees": [],
        "startDate": "",
        "endDate": ""
    }
]
```
### International Prices - Extra Services Rates Request
```sh
curl "https://api.usps.com/international-prices/v1/extra-service-rates/search" \
     -H "Content-Type: application/json" \
     -H "Accept: application/json" \
     -H "Authorization: Bearer $TOKEN" \
	--data-raw '{
		"extraService":"931",
		"mailClass":"PRIORITY_MAIL_INTERNATIONAL",
		"priceType":"COMMERCIAL",
		"rateIndicator":"SP",
		"destinationCountryCode":"CA",
		"weight":1.2,
		"mailingDate":"2022-08-20",
		"itemValue":500
	}'
```
```json
[
    {
        "rateId": "",
        "SKU": "IXIP0XXXXCX0500",
        "price": 10.95,
        "contractType": "CB"
    }
]
```

## Label
This API supports creation of domestic shipping labels with Intelligent Mail Package Barcodes (IMpb). Along with label and barcode creation in TIFF, PDF, SVG, or JPG format, this API validates addresses, confirms product availability, calculates postage, and generates the required Shipping Services File in accordance with USPS Publication 199 required for shipping with USPS. This label API currently supports Priority, Priority Express, First-class, Parcel Select Ground, Library, media and BPM classes.  This API requires payment to be added to the Bearer Token

###  Set the payment account for Label:
```sh
curl "https://api.usps.com/labels/v1/payment-account" \
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
Save the example request body to a file: [label-request.json](https://github.com/USPS/api-examples/blob/main/label-request.json)
```sh
curl "https://api.usps.com/labels/v1/label" \
     -H "accept: multipart/mixed" \
     -H "Content-Type: application/json" \
     -H "authorization: Bearer $TOKEN" \
     --data-binary @label-request.json \
     
```
Response: [label-response.json](https://github.com/USPS/api-examples/blob/main/label-response.json)


### Return Label Request
Save the example request body to a file: [returnlabel-request.json](https://github.com/USPS/api-examples/blob/main/returnlabel-request.json)
```sh
curl "https://api.usps.com/labels/v1/return-label" \
     -H "accept: multipart/mixed" \
     -H "Content-Type: application/json" \
     -H "authorization: Bearer $TOKEN" \
     --data-binary @returnlabel-request.json \
     
```
Response: [returnlabel-response.json](https://github.com/USPS/api-examples/blob/main/returnlabel-response.json)

### Cancel Label Request
This endpoint provides the ability to cancel label requests
```sh
curl "https://api.usps.com/labels/v1/label/{Tracking Number}" \
     -H "authorization: Bearer $TOKEN" \
     
```
Response: 200 OK if successful

## Tracking
This API returns tracking status and related details for a given USPS package, including scan events and their date, time, and location. Provides the latest status and delivery expectations.  

### Tracking - Single Request
```sh
curl "https://api.usps.com/tracking/v1/tracking/{Tracking Number}?expand=detail" \ 
     -H "accept: application/json" \
     -H "Content-Type: application/json" \
     -H "Authorization: Bearer $TOKEN" 
```
```json
{
    "TrackResults": {
        "RequestSeqNumber": null,
        "TrackInfo": {
            "@ID": "XXXXXXXXXXXXXXXXXXXXXXXXXX",
            "Class": "Parcel Select Lightweight",
            "ClassOfMailCode": "LW",
            "DestinationCity": "GREENSBORO",
            "DestinationState": "NC",
            "DestinationZip": "27455",
            "EmailEnabled": "true",
            "KahalaIndicator": "false",
            "MailTypeCode": "DM",
            "MPDATE": "2021-11-30 07:43:33.000000",
            "MPSUFFIX": "586939372",
            "OriginCity": "GREENSBORO",
            "OriginState": "NC",
            "OriginZip": "27401",
            "PodEnabled": "false",
            "TPodEnabled": "false",
            "RestoreEnabled": "false",
            "RramEnabled": "false",
            "RreEnabled": "false",
            "Service": "USPS Tracking<SUP>&#174;</SUP>",
            "ServiceTypeCode": "748",
            "Status": "Notice Left (No Secure Location Available)",
            "StatusCategory": "Delivery Attempt",
            "StatusSummary": "We attempted to deliver your item at 1:00 pm on November 30, 2021 in BIRMINGHAM, AL 35215 and a notice was left because no secure delivery location was available. ",
            "TABLECODE": "T",
            "TrackSummary": {
                "EventTime": "1:00 pm",
                "EventDate": "November 30, 2021",
                "Event": "Notice Left (No Secure Location Available)",
                "EventCity": "BIRMINGHAM",
                "EventState": "AL",
                "EventZIPCode": "35215",
                "EventCountry": null,
                "FirmName": null,
                "Name": null,
                "AuthorizedAgent": "false",
                "EventCode": "55",
                "GMT": "2021-11-30 19:00:00.000000",
                "GMTOffset": "-06:00"
            },
            "TrackDetail": {
                "EventTime": null,
                "EventDate": "November 30, 2021",
                "Event": "Pre-Shipment Info Sent to USPS, USPS Awaiting Item",
                "EventCity": null,
                "EventState": null,
                "EventZIPCode": null,
                "EventCountry": null,
                "FirmName": null,
                "Name": null,
                "AuthorizedAgent": "false",
                "EventCode": "MA",
                "GMT": null,
                "GMTOffset": null
            }
        }
    }
}
```
### Tracking - Email Notification Request
```sh
curl "https://api.usps.com/tracking/v1/tracking/{Tracking Number}/notifications" \
     -H "accept: application/json" \
     -H "Content-Type: application/json" \
     -H "Authorization: Bearer $TOKEN" \
     --data-raw '{
	  "PTSEmailRequest": {
		"ApproximateIntakeDate": "",
		"UniqueTrackingID": "",
		"notifyEventTypes": "EMAIL_alert",
		"FirstName": "John",
		"LastName": "Doe",
		"notifications": [
		  "user@example2.com",
		  1112223333
		]
	  }
	}'
```
```json
{
    "NotificationAcknowledgment": {
        "ResultText": "When new tracking activity is available, you'll get notifications based on your selections.",
        "ReturnCode": "0"
    }
}
```

## Service Standards
This API supports service standard for the number of days between the acceptance and delivery of a piece of mail that the Postal Service™ considers to be timely delivery. Service standards are delivery benchmarks for how long customers can expect for the Postal Service to deliver different types of mail from origin to destination — Point A to Point B. Service standards are not necessarily the same as the actual service performance.

### Service Standards - Standards Request
```sh
curl "https://api.usps.com/service-standards/v1/standards?originZIPCode=40504&destinationZIPCode=95823&mailClass=PARCEL_SELECT&destinationType=STREET" \ 
     -H "accept: application/json" \
     -H "Authorization: Bearer $TOKEN"
```
```json
[
    {
        "mailClass": "PARCEL_SELECT",
        "originZIPCode": "40504",
        "destinationZIPCode": "95823",
        "days": "7",
        "effectiveAcceptanceDate": "2022-08-01",
        "scheduledDeliveryDate": "2022-08-08"
    }
]
```
### Service Standards - Estimates Request
```sh
curl "https://api.usps.com/service-standards/v1/estimates?originZIPCode=63011&destinationZIPCode=63118&acceptanceDate=2022-08-01&mailClass=PARCEL_SELECT&destinationType=STREET" \
     -H "accept: application/json" \
     -H "Authorization: Bearer $TOKEN"
```
```json
[
    {
        "mailClass": "PARCEL_SELECT",
        "destinationType": "STREET",
        "acceptanceDateTime": "2022-08-01T12:00:00Z",
        "effectiveAcceptanceDate": "2022-08-01",
        "cutOffTime": "1800",
        "serviceStandard": "2",
        "serviceStandardMessage": "2 Days",
        "acceptanceLocations": [
            {
                "facilityType": null,
                "streetAddress": null,
                "city": null,
                "state": null,
                "ZIPCode": null,
                "ZIPPlus4": null
            }
        ],
        "delivery": {
            "scheduledDeliveryDateTime": "2022-08-03",
            "holdForPickupLocation": [
                {
                    "facilityName": null,
                    "streetAddress": null,
                    "city": null,
                    "state": null,
                    "ZIPCode": null,
                    "ZIPPlus4": null,
                    "closeTimes": {
                        "Monday": null,
                        "Tuesday": null,
                        "Wednesday": null,
                        "Thursday": null,
                        "Friday": null,
                        "Saturday": null,
                        "Sunday": null,
                        "holidays": null
                    }
                }
            ]
        }
    }
]
```
## Carrier Pickup
The API supports customers scheduling a carrier to pick up your packages on the next USPS delivery day (Monday through Saturday, excluding holidays) for free. Carrier Pickup is available for sending packages using Priority Mail Express, Priority Mail, First Class Package Service Commercial, international delivery services, or for returned merchandise.  You are able check availability, schedule, change, cancel and inquire on a carrier pickup.

### Carrier Pickup - Eligibility
```sh
curl "https://api.usps.com/pickup/v1/carrier-pickup/eligibility?streetAddress=407 Pennsylvania Ave&secondaryAddress=Suite 201&city=Joplin&state=MO&ZIPCode=64801" \
     -H "Content-Type: application/json" \
     -H "authorization: Bearer $TOKEN" \
```
```json
{
    "pickupAddress": {
        "firstName": null,
        "lastName": null,
        "firm": null,
        "address": {
            "streetAddress": "string",
            "secondaryAddress": null,
            "city": "string",
            "state": "XX",
            "ZIPCode": "XXXXX",
            "ZIPPlus4": "XXXX",
            "urbanization": null
        },
        "contact": [
            {
                "eMail": null
            },
            {
                "phone": null,
                "extension": null
            }
        ]
    }
}
```
### Carrier Pickup - Create
```sh
curl "https://api.usps.com/pickup/v1/carrier-pickup" \
     -H "accept: application/json" \
     -H "Content-Type: application/json" \
     -H "authorization: Bearer $TOKEN" \
     --data-raw '{
		  "pickupDate": "2022-09-09",
		  "pickupAddress": {
			"firstName": "Bob",
			"lastName": "Fish",
			"firm": "Seafood",
			"address": {
			  "streetAddress": "407 S PENNSYLVANIA AVE",
			  "streetAddressAbbreviation": "",
			  "secondaryAddress": "Suite 201",
			  "city": "Joplin",
			  "cityAbbreviation": "",
			  "state": "MO",
			  "postalCode": "",
			  "province": "",
			  "ZIPCode": "64801",
			  "ZIPPlus4": "2281",
			  "urbanization": ""
			},
			"contact": [
			  {"email":"emails"},
			  {"phone": "3333333333"}
			]
		  },
		  "packages": [
			{
			  "packageType": "FIRST-CLASS_PACKAGE_SERVICE",
			  "packageCount": 1
			}
		  ],
		  "estimatedWeight": 5,
		  "pickupLocation": {
			"packageLocation": "OFFICE",
			"specialInstructions": "Have a nice day"
  }
}' 
```
```json
{
    "confirmationNumber": "{Confirmation Number}",
    "pickupAddress": {
        "firstName": "string",
        "lastName": "string",
        "firm": "string",
        "address": {
            "streetAddress": "string",
            "secondaryAddress": null,
            "city": "string",
            "state": "XX",
            "ZIPCode": "XXXXX",
            "ZIPPlus4": "XXXX",
            "urbanization": null
        },
        "contact": [
            {
                "email": null
            },
            {
                "phone": null,
                "extension": null
            }
        ]
    },
    "packages": [
        {
            "packageType": "FIRST-CLASS_PACKAGE_SERVICE",
            "packageCount": "1"
        },
        {
            "packageType": "PRIORITY_MAIL_EXPRESS",
            "packageCount": "0"
        },
        {
            "packageType": "PRIORITY_MAIL",
            "packageCount": "0"
        },
        {
            "packageType": "RETURNS",
            "packageCount": "0"
        },
        {
            "packageType": "INTERNATIONAL",
            "packageCount": "0"
        },
        {
            "packageType": "OTHER",
            "packageCount": "0"
        }
    ],
    "estimatedWeight": "5",
    "pickupLocation": {
        "packageLocation": "OFFICE",
        "specialInstructions": "HAVE A NICE DAY"
    },
    "pickupDate": "2022-09-10"
}
```
### Carrier Pickup - Get
 ```sh
curl "https://api.usps.com/pickup/v1/carrier-pickup/{Confirmation Number}" \
     -H "accept: application/json" \
     -H "authorization: Bearer $TOKEN" \
```
```json
{
    "confirmationNumber": "{Confirmation Number}",
    "pickupAddress": {
        "firstName": "string",
        "lastName": "string",
        "firm": "string",
        "address": {
            "streetAddress": "string",
            "secondaryAddress": null,
            "city": "string",
            "state": "XX",
            "ZIPCode": "XXXXX",
            "ZIPPlus4": "XXXX",
            "urbanization": null
        },
        "contact": [
            {
                "email": null
            },
            {
                "phone": "nullnullnull",
                "extension": null
            }
        ]
    },
    "packages": [
        {
            "packageType": "FIRST-CLASS_PACKAGE_SERVICE",
            "packageCount": "1"
        },
        {
            "packageType": "PRIORITY_MAIL_EXPRESS",
            "packageCount": "0"
        },
        {
            "packageType": "PRIORITY_MAIL",
            "packageCount": "0"
        },
        {
            "packageType": "RETURNS",
            "packageCount": "0"
        },
        {
            "packageType": "INTERNATIONAL",
            "packageCount": "0"
        },
        {
            "packageType": "OTHER",
            "packageCount": "0"
        }
    ],
    "estimatedWeight": "5",
    "pickupLocation": {
        "packageLocation": "OFFICE",
        "specialInstructions": "HAVE A NICE DAY"
    },
    "pickupDate": "2022-09-10"
}
```
### Carrier Pickup - Update
 ```sh
curl "https://api.usps.com/pickup/v1/carrier-pickup/{Confirmation Number}" \
     -H "accept: application/json" \
     -H "Content-Type: application/json" \
	 -H "if-Match: application/json" \
     -H "authorization: Bearer $TOKEN" \
     --data-raw '{
		  "confirmationNumber": "{Confirmation Number}",
		  "pickupDate": "2022-09-10",
		  "pickupAddress": {
			"firstName": "string",
			"lastName": "string",
			"firm": "string",
			"address": {
			  "streetAddress": "string",
			  "streetAddressAbbreviation": "",
			  "secondaryAddress": "string",
			  "city": "string",
			  "cityAbbreviation": "",
			  "state": "XX",
			  "postalCode": "",
			  "province": "",
			  "ZIPCode": "XXXXX",
			  "ZIPPlus4": "",
			  "urbanization": "",
			  "country": "",
			  "countryISOCode": ""
			},
			"contact": [
			  {"email":"emails"},
			  {"phone": "3333333333"}
			]
		  },
		  "packages": [
			{
			  "packageType": "FIRST-CLASS_PACKAGE_SERVICE",
			  "packageCount": 5
			}
		  ],
		  "estimatedWeight": 15,
		  "pickupLocation": {
			"packageLocation": "OFFICE",
			"specialInstructions": "Have a nice day"
		  }
		}'   
```
```json
{
    "confirmationNumber": "{Confirmation Number}",
    "pickupAddress": {
        "firstName": "string",
        "lastName": "string",
        "firm": "string",
        "address": {
            "streetAddress": "string",
            "secondaryAddress": null,
            "city": "string",
            "state": "XX",
            "ZIPCode": "XXXXX",
            "ZIPPlus4": "XXXX",
            "urbanization": null
        },
        "contact": [
            {
                "email": null
            },
            {
                "phone": "nullnullnull",
                "extension": null
            }
        ]
    },
    "packages": [
        {
            "packageType": "FIRST-CLASS_PACKAGE_SERVICE",
            "packageCount": "1"
        },
        {
            "packageType": "PRIORITY_MAIL_EXPRESS",
            "packageCount": "0"
        },
        {
            "packageType": "PRIORITY_MAIL",
            "packageCount": "0"
        },
        {
            "packageType": "RETURNS",
            "packageCount": "0"
        },
        {
            "packageType": "INTERNATIONAL",
            "packageCount": "0"
        },
        {
            "packageType": "OTHER",
            "packageCount": "0"
        }
    ],
    "estimatedWeight": "5",
    "pickupLocation": {
        "packageLocation": "OFFICE",
        "specialInstructions": "HAVE A NICE DAY"
    },
    "pickupDate": "2022-09-10"
}
```
### Carrier Pickup - Delete
 ```sh
curl "https://api.usps.com/pickup/v1/carrier-pickup/{Confirmation Number}" \
     -H "accept: application/json" \
     -H "Content-Type: application/json" \
     -H "authorization: Bearer $TOKEN" \
```
Response: 200 OK if successful

## Organizations
The API supports registration of new companies to USPS.  This API will create a new customer registration ID and mailer ID if the customer is not known to USPS.  If USPS has a record of the company, the API will not create a new customer registration ID or mailer ID.  
 ```sh
curl "https://api.usps.com/organizations/v1/organizations" \
     -H "accept: application/json" \
     -H "Content-Type: application/json" \
     -H "authorization: Bearer $TOKEN" \
	 --data-raw '{
		  "id": "",
		  "name": "string",
		  "parentCRID": "",
		  "locations": [
			{
			  "firstName": "string",
			  "lastName": "string",
			  "firmName": "string",
			  "streetAddress": "string",
			  "secondaryAddress": "string",
			  "city": "string",
			  "state": "MO",
			  "ZIPCode": "XXXXX",
			  "ZIPPlus4": "",
			  "urbanization": "",
			  "country": "840 UNITED STATES",
			  "countryISOCode": "840"
			}
		  ],
		  "customers": [],
		  "contact": {
			"phoneRecipients": [
			  {
				"type": "BUS",
				"number": "333-333-3333",
				"extension": ""
			  }
			]
		  }
		}'
 ```
```json
{
    "organization": {
        "Id": "XXXXXXXX",
        "name": "string",
        "CRID": "XXXXXXXX",
        "location": {
            "firm": "string",
            "streetAddress": "string",
            "secondaryAddress": null,
            "city": "string",
            "state_province": "MO",
            "postalCode": "XXXXX-XXXX",
            "urbanization": null,
            "country_name": "UNITED STATES",
            "country_iso_code": "840",
            "CRID": "XXXXXXXX",
            "MIDs": {
                "mid": "XXXXXXXXX"
            }
        },
        "contact": {
            "phone": {
                "type": "BUS",
                "number": "333-333-3333"
            }
        }
    }
}
```
## Postman Collection
Here is a Postman Collection of the above curl commands above that you can utilize to help in getting a jump start of using USPS APIs. 

Example postman collection file: [Example postman collection.json](https://github.com/USPS/api-examples/Example-Postman.postman_collection.json)
