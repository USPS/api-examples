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

## OAuth Token
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
curl	--location 'https://api-cat.usps.gov/addresses/v1/address?streetAddress=3120%20M%20St&secondarayAddress=NW&city=Washington&state=DC&ZIPCode=20027&ZIPPlus4=3704' \
		--header 'accept: application/json' \
		--header 'x-user-id: XXXXXXXXXXXX' \
		--header 'authorization: Bearer $TOKEN' \
		--header 'Cookie: XXXXXXXXXXXXXXXXXXXX'
```
```json
{
    "firm": null,
    "address": {
        "streetAddress": "3120 M ST NW",
        "streetAddressAbbreviation": null,
        "secondaryAddress": null,
        "city": "WASHINGTON",
        "cityAbbreviation": null,
        "state": "DC",
        "postalCode": null,
        "province": null,
        "ZIPCode": "20007",
        "ZIPPlus4": "3704",
        "urbanization": null,
        "country": null,
        "countryISOCode": null
    },
    "addressAdditionalInfo": {
        "deliveryPoint": "20",
        "carrierRoute": "C036",
        "DPVConfirmation": "Y",
        "DPVCMRA": "N",
        "business": "Y",
        "centralDeliveryPoint": "N",
        "vacant": "N"
    },
    "addressCorrections": null,
    "addressMatches": null
}
```
###  City and State
Returns city and state corresponding to a given ZIP code.
```sh
curl	--location 'https://api-cat.usps.gov/addresses/v1/city-state?ZIPCode=30022' \
		--header 'accept: application/json' \
		--header 'X-User-Id: XXXXXXXXXXX' \
		--header 'Authorization: Bearer $TOKEN' \
		--header 'Cookie: XXXXXXXXXXXXXXXXXXXX'
```
```json
{
    "city": "ALPHARETTA",
    "state": "GA"
} 
```
###  ZIPCode
Returns ZIPCode and ZIPPlus4 corresponding to a given address, city, and state
```sh
curl	--location 'https://api-cat.usps.gov/addresses/v1/zipcode?streetAddress=1273%20Pale%20San%20Vitores%20RD&city=Tamuning&state=GU' \
		--header 'accept: application/json' \
		--header 'x-user-id: XXXXXXXXXXXX' \
		--header 'authorization: Bearer $TOKEN' \
		--header 'Cookie: XXXXXXXXXXXXXXXXXXXX' 
```
```json
{
    "firm": null,
    "address": {
        "streetAddress": "1273 PALE SAN VITORES RD",
        "streetAddressAbbreviation": null,
        "secondaryAddress": null,
        "city": "TAMUNING",
        "cityAbbreviation": null,
        "state": "GU",
        "postalCode": null,
        "province": null,
        "ZIPCode": "96913",
        "ZIPPlus4": "4208",
        "urbanization": null,
        "country": null,
        "countryISOCode": null
    }
}
```

## Carrier Pickup
The API supports customers scheduling a carrier to pick up your packages on the next USPS delivery day (Monday through Saturday, excluding holidays) for free. Carrier Pickup is available for sending packages using Priority Mail Express, Priority Mail, First Class Package Service Commercial, international delivery services, or for returned merchandise.  You are able check availability, schedule, change, cancel and inquire on a carrier pickup.

### Carrier Pickup - Eligibility
```sh
curl	--location 'https://api-cat.usps.gov/pickup/v1/carrier-pickup/eligibility?streetAddress=4120%20Bingham%20Ave&city=Saint%20Louis&state=MO&ZIPCode=63116' \
		--header 'Accept: appplication/json' \
		--header 'Authorization: Bearer $TOKEN' \
		--header 'Cookie: XXXXXXXXXXXXXXXXXXXX' \
		--data ''
```
```json
{
    "pickupAddress": {
        "firstName": null,
        "lastName": null,
        "firm": null,
        "address": {
            "streetAddress": "4120 BINGHAM AVE",
            "secondaryAddress": null,
            "city": "SAINT LOUIS",
            "state": "MO",
            "ZIPCode": "63116",
            "ZIPPlus4": "2520",
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
curl	--location 'https://api-cat.usps.gov/pickup/v1/carrier-pickup' \
		--header 'Accept: appplication/json' \
		--header 'Content-Type: application/json' \
		--header 'Authorization: Bearer $TOKEN' \
		--header 'Cookie: XXXXXXXXXXXXXXXXXXXX' \
		--data-raw '{
		"pickupDate": "2023-04-20",
		"pickupAddress": {
			"firstName": "John",
			"lastName": "Smith",
			"firm": "FIRM",
			"address": {
			"streetAddress": "4120 Bingham",
			"streetAddressAbbreviation": "Ave",
			"secondaryAddress": null,
			"city": "Saint Louis",
			"cityAbbreviation": "",
			"state": "MO",
			"postalCode": "63116",
			"province": "",
			"ZIPCode": "63116",
			"ZIPPlus4": "2520",
			"urbanization": ""
			},
			"contact": [
			{"email":"john.smith@firm.com"},
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
        "firstName": "JOHN",
        "lastName": "SMITH",
        "firm": "FIRM",
        "address": {
            "streetAddress": "4120 BINGHAM AVE",
            "secondaryAddress": null,
            "city": "SAINT LOUIS",
            "state": "MO",
            "ZIPCode": "63116",
            "ZIPPlus4": "2520",
            "urbanization": null
        },
        "contact": [
            {
                "email": "JOHN.SMITH@FIRM.COM"
            },
            {
                "phone": "3333333333",
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
    "pickupDate": "2023-04-20"
}
```

### Carrier Pickup - Get
 ```sh
curl	--location 'https://api-cat.usps.gov/pickup/v1/carrier-pickup/{Confirmation Number}' \
		--header 'Accept: appplication/json' \
		--header 'Authorization: Bearer $TOKEN' \
		--header 'Cookie: XXXXXXXXXXXXXXXXXXXX'
```
```json
{
    "confirmationNumber": "{Confirmation Number}",
    "pickupAddress": {
        "firstName": "JOHN",
        "lastName": "SMITH",
        "firm": "FIRM",
        "address": {
            "streetAddress": "4120 BINGHAM AVE",
            "secondaryAddress": null,
            "city": "SAINT LOUIS",
            "state": "MO",
            "ZIPCode": "63116",
            "ZIPPlus4": "2520",
            "urbanization": null
        },
        "contact": [
            {
                "email": "JOHN.SMITH@FIRM.COM"
            },
            {
                "phone": "3333333333",
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
    "pickupDate": "2023-04-20"
}
```

### Carrier Pickup - Update
 ```sh
curl	--location --request PUT 'https://api-cat.usps.gov/pickup/v1/carrier-pickup/{Confirmation Number}' \
		--header 'Accept: application/json' \
		--header 'Content-Type: application/json' \
		--header 'If-Match: XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX' \
		--header 'Authorization: Bearer $TOKEN' \
		--header 'Cookie: XXXXXXXXXXXXXXXXXXXX' \
		--data-raw '{
		"confirmationNumber": "{Confirmation Number}",
		"pickupDate": "2023-04-22",
		"pickupAddress": {
			"firstName": "Smith",
			"lastName": "John",
			"firm": "FIRM",
			"address": {
			"streetAddress": "4120 Bingham",
			"streetAddressAbbreviation": "Ave",
			"secondaryAddress": null,
			"city": "Saint Louis",
			"cityAbbreviation": "",
			"state": "MO",
			"postalCode": "63116",
			"province": "",
			"ZIPCode": "63116",
			"ZIPPlus4": "",
			"urbanization": ""
			},
			"contact": [
			{"email":"smith.john@firm.com"},
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
			"specialInstructions": "Have a nice day after carrier pickup updated 2"
		}
		}'  
```
```json
{
    "confirmationNumber": "{Confirmation Number}",
    "pickupAddress": {
        "firstName": "SMITH",
        "lastName": "JOHN",
        "firm": "FIRM",
        "address": {
            "streetAdress": "4120 BINGHAM AVE",
            "secondaryAddress": null,
            "city": "SAINT LOUIS",
            "state": "MO",
            "ZIPCode": "63116",
            "ZIPLus4": "2520",
            "urbanization": null
        },
        "contact": [
            {
                "email": "SMITH.JOHN@FIRM.COM"
            },
            {
                "phone": "3333333333",
                "extension": null
            }
        ]
    },
    "packages": [
        {
            "packageType": "FIRST-CLASS_PACKAGE_SERVICE",
            "packageCount": "5"
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
    "estimatedWeight": "15",
    "pickupLocation": {
        "packageLocation": "OFFICE",
        "specialInstructions": "HAVE A NICE DAY AFTER CARRIER PICKUP UPDATED 2"
    },
    "pickupDate": "2023-04-22"
}
```

### Carrier Pickup - Delete
 ```sh
curl --location --request DELETE 'https://api-cat.usps.gov/pickup/v1/carrier-pickup/{Confirmation Number}' \
--header 'Accept: appplication/json' \
--header 'If-Match: XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX' \
--header 'Authorization: Bearer $TOKEN' \
--header 'Cookie: XXXXXXXXXXXXXXXXXXXX' \
--data ''
```
Response: 200 OK if successful

## Domestic Label
This API supports creation of domestic shipping labels with Intelligent Mail Package Barcodes (IMpb). Along with label and barcode creation in TIFF, PDF, SVG, or JPG format, this API validates addresses, confirms product availability, calculates postage, and generates the required Shipping Services File in accordance with USPS Publication 199 required for shipping with USPS. This label API currently supports Priority, Priority Express, First-class, Parcel Select Ground, Library, media and BPM classes.  This API requires payment to be added to the Bearer Token

###  Set the payment account for Domestic Label:
```sh
curl 	--location 'https://api-cat.usps.com/payments/v2/payment-authorization' \
		--header 'Content-Type: application/json' \
		--header 'Authorization: Bearer $TOKEN' \
		--header 'Cookie: XXXXXXXXXXXXXXXXXXXX \
		--data '{
			"roles": [
				{
					"roleName": "PAYER",
					"CRID": "XXXXXXXX",
					"MID": "XXXXXXXXX",
					"manifestMID": "XXXXXXXXX",
					"accountType": "EPS",
					"accountNumber": "XXXXXXXXXX"
				},
				{
					"roleName": "LABEL_OWNER",
					"CRID": "XXXXXXXX",
					"MID": "XXXXXXXXX",
					"manifestMID": "XXXXXXXXX",
					"accountType": "EPS",
					"accountNumber": "XXXXXXXXXX"
				}
			]
		}'
```
```json
{
    "roles": [
        {
            "roleName": "PAYER",
            "CRID": "XXXXXXXX",
            "MID": "XXXXXXXXX",
            "manifestMID": "XXXXXXXXX",
            "accountType": "EPS",
            "accountNumber": "XXXXXXXXXX"
        },
        {
            "roleName": "LABEL_OWNER",
            "CRID": "XXXXXXXX",
            "MID": "XXXXXXXXX",
            "manifestMID": "XXXXXXXXX",
            "accountType": "EPS",
            "accountNumber": "XXXXXXXXXX"
        }
    ]
} 
```

### Domestic Label Request
Save the example request body to a file: [label-request.json](https://github.com/USPS/api-examples/blob/main/domesticlabel-request.json)
```sh
curl 	--location 'https://api-cat.usps.gov/labels/v2/label' \
		--header 'X-Payment-Authorization-Token: $PAYMENTTOKEN'\ (Follow up with Steve)
		--header 'Content-Type: application/json' \
		--header 'Authorization: Bearer $TOKEN' \
		--header 'Cookie: XXXXXXXXXXXXXXXXXXX' \
		--data @domesticlabel-request \
     
```
Response: [domesticlabel-response.json](https://github.com/USPS/api-examples/blob/main/domesticlabel-response.json)


## Domestic Prices
This API provides Domestic USPS product pricing based on the characteristics of what is being shipped for Parcel Select, Parcel Select Lightweight, Priority Mail, Priority Mail Express, First Class Package Services, Library Mail, Media Mail, and Bound Printed Matter.  Given inputs, such as origin and destination ZIP Codes, package weight and dimensions, processing category, destination rate indicator, price type, and desired Mail Class and Extra Services, this API will return the price of postage.  

### Domestic Prices - Base Rates Request
```sh
curl 	--location 'https://api-cat.usps.gov/prices/v1/base-rates/search' \
		--header 'Content-Type: application/json' \
		--header 'Authorization: Bearer $TOKEN' \
		--data '{
			"searchType":"SSF",
			"originZIPCode":"22407",
			"destinationZIPCode":"63118",
			"weight":"0.5",
			"length":"12",
			"width":"2",
			"height":"3",
			"mailClass":"PRIORITY_MAIL_EXPRESS",
			"processingCategory":"MACHINABLE",
			"destinationEntryFacilityType":"NONE",
			"rateIndicator":"DR",
			"parcelRoutingBarcode":"0",
			"isPoundBased":"N",
			"priceType":"RETAIL",
			"mailingDate":"2023-04-19"
}'
```
```json
[
    {
        "rateId": null,
        "SKU": "DEXR0XXXXR05005",
        "description": "Priority Mail Express Machinable Dimensional Rectangular",
        "contractType": "R",
        "price": 35.5,
        "maxWeight": 0.5,
        "fees": [],
        "startDate": "2023-01-22",
        "endDate": ""
    }
]
```
### Domestic Prices - Extra Services Rates Request
```sh
curl 	--location 'https://api-cat.usps.gov/prices/v1/extra-service-rates/search' \
		--header 'Content-Type: application/json' \
		--header 'Authorization: Bearer $TOKEN' \
		--data '{
			"searchType":"SSF",
			"originZIPCode":"78664",
			"destinationZIPCode":"78665",
			"weight":"7",
			"length":"3",
			"width":"5",
			"height":"3",
			"mailClass":"PARCEL_SELECT",
			"processingCategory":"NON_MACHINABLE",
			"destinationEntryFacilityType":"DESTINATION_NETWORK_DISTRIBUTION_CENTER",
			"rateIndicator":"DR",
			"parcelRoutingBarcode":"0",
			"isPoundBased":"N",
			"priceType":"COMMERCIAL",
			"extraService": 924,
			"itemValue": 900,
			"mailingDate":"2023-04-19"
		}'
```
```json
{
    "sku": "DXSV0EJXXCX0000",
    "rate": 9.80,
    "contractType": "CB",
    "extraServiceCode": "924",
    "description": "Signature Confirmation Restricted Delivery",
    "warnings": []
}
```
## International Prices
The International Prices API provides International USPS product pricing based on the characteristics of what is being shipped for Global Express Guaranteed, Priority Mail Express International, Priority Mail International, and First-Class Package International. Given inputs, such as origin and destination ZIP Codes, package weight and dimensions, processing category, destination rate indicator, price type, and desired Mail Class and Extra Services, this API will return the price of postage and Extra Services.

### International Prices - Base Rates Request
```sh
curl 	--location 'https://api-cat.usps.gov/international-prices/v1/base-rates/search' \
		--header 'Content-Type: application/json' \
		--header 'Authorization: Bearer $TOKEN' \
		--data '{
			"searchType": "SSF",
			"originZIP": "22407",
			"foreignPostalCode": "10109",
			"destinationCountryCode": "CA",
			"destinationEntryFacilityType": "NONE",
			"weight": "4",
			"length": "6",
			"width": "0.25",
			"height": "3",
			"mailClass": "PRIORITY_MAIL_INTERNATIONAL",
			"processingCategory": "MACHINABLE",
			"rateIndicator": "SP",
			"parcelRoutingBarcode": "0",
			"isPoundBased": "N",
			"priceType": "RETAIL",
			"mailingDate": "2023-04-19"
		}'
```
```json
[
    {
        "rateId": null,
        "SKU": "IPXX0XXXXR01040",
        "description": "Priority Mail International Machinable Single-piece",
        "contractType": "R",
        "price": 52.2,
        "maxWeight": 4,
        "fees": [],
        "startDate": "2023-01-22",
        "endDate": ""
    }
]
```
### International Prices - Extra Services Rates Request
```sh
curl 	--location 'https://api-cat.usps.gov/international-prices/v1/extra-service-rates/search' \
		--header 'Content-Type: application/json' \
		--header 'Authorization: Bearer $TOKEN' \
		--data '{
			"destinationCountryCode":"CR",
			"extraService":"930",
			"mailClass":"PRIORITY_MAIL_INTERNATIONAL",
			"mailingDate":"2023-04-19",
			"priceType":"RETAIL",
			"rateIndicator":"SP",
			"weight":"4",
			"itemValue": 500
		}'
```
```json
{
    "sku": "IXIP0XXXXCX0500",
    "rate": 19.55,
    "contractType": "CB",
    "extraServiceCode": "930",
    "description": "Insurance <= $500",
    "warnings": []
}
```

## Locations
This API supports returning destination entry facilities (i.e., drop off locations) for a given ZIP Code to enable Parcel Select Destination Entry, USPS Connect, and Parcel Select Lightweight. This API also provides additional information about working hours, delivery type, mail class, shape, palletization, and facility type.
```sh
curl 	--location 'https://api-cat.usps.gov/locations/v1/dropoff-locations?mailClass=USPS_CONNECT_LOCAL&destinationZIPCode=30342' \
		--header 'Authorization: Bearer $TOKEN'
```
Response:
[dropoff-location-response.json](https://github.com/USPS/api-examples/blob/main/dropoff-location-response.json)

## Tracking
This API returns tracking status and related details for a given USPS package, including scan events and their date, time, and location. Provides the latest status and delivery expectations.  

### Tracking - Single Request - Summary
```sh
curl 	--location 'https://api-cat.usps.gov/tracking/v1/tracking/{Tracking Number}?expand=summary' \
		--header 'Authorization: Bearer $TOKEN' \
		--data '' 
```
```json
{
    "TrackResults": {
        "RequestSeqNumber": null,
        "TrackInfo": {
            "@ID": "XXXXXXXXXXXXXXXXXXXX",
            "TrackSummary": "USPS is now in possession of your item as of 7:31 am on February 15, 2023 in RICHMOND, VA 23227."
        }
    }
}
```
### Tracking - Single Request - Detail
```sh
curl 	--location 'https://api-cat.usps.gov/tracking/v1/tracking/{Tracking Number}?expand=detail' \
		--header 'Authorization: Bearer $TOKEN' \
		--data ''
```
```json
{
    "TrackResults": {
        "RequestSeqNumber": null,
        "TrackInfo": {
            "@ID": "XXXXXXXXXXXXXXXXXXXX",
            "Class": "Priority Mail<SUP>&reg;</SUP>",
            "ClassOfMailCode": "PM",
            "DestinationCity": "CEDAR RAPIDS",
            "DestinationState": "IA",
            "DestinationZip": "52404",
            "EmailEnabled": "true",
            "KahalaIndicator": "false",
            "MailTypeCode": "DM",
            "MPDATE": "2023-02-17 10:56:10.000000",
            "MPSUFFIX": "518152467",
            "OriginCity": "RICHMOND",
            "OriginState": "VA",
            "OriginZip": "23227",
            "PodEnabled": "false",
            "TPodEnabled": "false",
            "RestoreEnabled": "false",
            "RramEnabled": "false",
            "RreEnabled": "false",
            "Service": [
                "USPS Tracking<SUP>&#174;</SUP>",
                "Up to $100 insurance included"
            ],
            "ServiceTypeCode": "14",
            "Status": "USPS in possession of item",
            "StatusCategory": "Accepted",
            "StatusSummary": "USPS is now in possession of your item as of 7:31 am on February 15, 2023 in RICHMOND, VA 23227.",
            "TABLECODE": "T",
            "TrackSummary": {
                "EventTime": "7:31 am",
                "EventDate": "February 15, 2023",
                "Event": "USPS in possession of item",
                "EventCity": "RICHMOND",
                "EventState": "VA",
                "EventZIPCode": "23227",
                "EventCountry": null,
                "FirmName": null,
                "Name": null,
                "AuthorizedAgent": "false",
                "EventCode": "03",
                "DeliveryAttributeCode": null,
                "GMT": "12:31:32",
                "GMTOffset": "-05:00"
            }
        }
    }
}
```
### Tracking - Email Notification Request
```sh
curl 	--location 'https://api-cat.usps.gov/tracking/v1/tracking/{Tracking Number}/notifications' \
		--header 'Content-Type: application/json' \
		--header 'Accept: application/json' \
		--header 'Authorization: Bearer $TOKEN' \
		--header 'Cookie: XXXXXXXXXXXXXXXXXXXX' \
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
        "ReturnCode": "2"
    }
}
```

## Shipment Confirmation Acceptance Notice (SCAN) Form
This API supports linking of multiple domestic and international labels through one Electronic File Number (EFN) and physical SCAN Form (PS Form 5630).

### SCAN Form - Label Shipment
Save the example request body to a file: [SCANForm-LabelShipment-request.json](https://github.com/USPS/api-examples/blob/main/SCANForm-LabelShipment-request.json)
```sh
curl 	--location 'https://api-cat.usps.gov/scan-forms/v1/scan-form' \
		--header 'Content-Type: application/json' \
		--header 'Authorization: Bearer $TOKEN' \
		--header 'Cookie: XXXXXXXXXXXXXXXXXXXX' \
		--data @SCANForm-LabelShipment-request.json \
     
```
Response: [SCANForm-LabelShipment-response.json](https://github.com/USPS/api-examples/blob/main/SCANForm-LabelShipment-response.json)

### SCAN Form - MID Shipment
Save the example request body to a file: [SCANForm-MIDShipment-request.json](https://github.com/USPS/api-examples/blob/main/SCANForm-MIDShipment-request.json)
```sh
curl 	--location 'https://api-cat.usps.gov/scan-forms/v1/scan-form' \
		--header 'Content-Type: application/json' \
		--header 'Authorization: Bearer $TOKEN' \
		--header 'Cookie: XXXXXXXXXXXXXXXXXXXX' \
		--data @SCANForm-MIDShipment-request.json \
     
```
Response: [SCANForm-MIDShipment-response.json](https://github.com/USPS/api-examples/blob/main/SCANForm-MIDShipment-response.json)

### SCAN Form - Manifest MID Shipment
Save the example request body to a file: [SCANForm-ManifestMIDShipment-request.json](https://github.com/USPS/api-examples/blob/main/SCANForm-ManifestMIDShipment-request.json)
```sh
curl 	--location 'https://api-cat.usps.gov/scan-forms/v1/scan-form' \
		--header 'Content-Type: application/json' \
		--header 'Authorization: Bearer $TOKEN' \
		--header 'Cookie: XXXXXXXXXXXXXXXXXXXX' \
		--data @SCANForm-ManifestMIDShipment-request.json \
     
```
Response: [SCANForm-ManifestMIDShipment-response.json](https://github.com/USPS/api-examples/blob/main/SCANForm-ManifestMIDShipment-response.json)


## Service Standards
This API supports service standard for the number of days between the acceptance and delivery of a piece of mail that the Postal Service™ considers to be timely delivery. Service standards are delivery benchmarks for how long customers can expect for the Postal Service to deliver different types of mail from origin to destination — Point A to Point B. Service standards are not necessarily the same as the actual service performance.

### Service Standards - Estimates Request
```sh
curl	--location 'https://api-cat.usps.gov/service-standards/v1/estimates?originZIPCode=10018&destinationZIPCode=95823&acceptDate=3%2F5%2F2023&mailClass=PRIORITY_MAIL&destinationType=HOLD_FOR_PICKUP' \
		--header 'Authorization: Bearer $TOKEN' \
		--header 'Cookie: XXXXXXXXXXXXXXXXXXXX'
```
```json
[
    {
        "mailClass": "PRIORITY_MAIL",
        "destinationType": "HOLD_FOR_PICKUP",
        "acceptanceDateTime": "2023-04-19T12:00:00Z",
        "effectiveAcceptanceDate": "2023-04-19",
        "cutOffTime": "1700",
        "serviceStandard": "2",
        "serviceStandardMessage": "2-Day",
        "acceptanceLocations": [
            {
                "facilityType": "POST OFFICE",
                "streetAddress": "223 W 38TH ST",
                "city": "NEW YORK",
                "state": "NY",
                "ZIPCode": "10018",
                "ZIPPlus4": "9998"
            }
        ],
        "delivery": {
            "scheduledDeliveryDateTime": "2023-04-22",
            "holdForPickupLocation": [
                {
                    "facilityName": "PARKWAY",
                    "streetAddress": "4301 BROOKFIELD DR",
                    "city": "SACRAMENTO",
                    "state": "CA",
                    "ZIPCode": "95823",
                    "ZIPPlus4": "9998",
                    "closeTimes": {
                        "Monday": "1700",
                        "Tuesday": "1700",
                        "Wednesday": "1700",
                        "Thursday": "1700",
                        "Friday": "1700",
                        "Saturday": "1500",
                        "Sunday": "0000",
                        "holidays": "0000"
                    }
                }
            ]
        }
    }
]
```
### Service Standards - Standards Request
```sh
curl	--location 'https://api-cat.usps.gov/service-standards/v1/standards?originZIPCode=40504&destinationZIPCode=95823&mailClass=PRIORITY_MAIL&destinationType=Junk' \
		--header 'Authorization: Bearer $TOKEN' \
		--header 'Cookie: NSC_fnbt-bqj-dbu=ffffffff3b449a5145525d5f4f58455e445a4a42378b; NSC_ted1-dbu_443_2dpot=ffffffff3b448c6e45525d5f4f58455e445a4a4212d3'
```
```json
[
    {
        "mailClass": "PRIORITY_MAIL",
        "originZIPCode": "40504",
        "destinationZIPCode": "95823",
        "days": "2",
        "effectiveAcceptanceDate": "2023-04-19",
        "scheduledDeliveryDate": "2023-04-22"
    },
    {
        "mailClass": "PRIORITY_MAIL",
        "originZIPCode": "40504",
        "destinationZIPCode": "95823",
        "days": "2",
        "effectiveAcceptanceDate": "2023-04-19",
        "scheduledDeliveryDate": "2023-04-22"
    },
    {
        "mailClass": "PRIORITY_MAIL",
        "originZIPCode": "40504",
        "destinationZIPCode": "95823",
        "days": "2",
        "effectiveAcceptanceDate": "2023-04-19",
        "scheduledDeliveryDate": "2023-04-22"
    }
]
```

## Errors
Some error handling examples realted to Address and Domestic Labels:

### Address Errors
```errors
Invalid-City:
    description: The city is missing or invalid.
    value:  { apiVersion: "v1", error: {code: "400", message: "The city in the request is missing or invalid.", errors: [] } }
      
Invalid-State-Code:
    description: The two-letter state code is missing or invalid.
    value:  { apiVersion: "v1", error: {code: "400", message: "The state code in the request is missing or invalid.", errors: [] } }
      
Unverifiable-City-and-State:
    description: The city and state are missing or together unverifiable.
    value:  { apiVersion: "v1", error: {code: "400", message: "The city and state are missing or together unverifiable.", errors: [] } }
     
Insufficient-Adddress-Data:
    description: The address information in the request is insufficient to match.
    value: { apiVersion: "v1", error: {code: "404", message: "The address information in the request is insufficient to match.", errors: [] } }
      
Address-Not-Found:
    description: There is no match for the specified address, try adding as much information as possible.
    value: { apiVersion: "v1", error: {code: "404", message: "There is no match for the address requested.", errors: [] } }
      
No-Match:
    description: There is no match for the specified address, try adding as much information as possible.
    value: { apiVersion: "v1", error: {code: "404", message: "There is no match for the address requested.", errors: [] } }    
    
Invalid-Delivery-Address:
    description: The address requested is an invalid delivery address.
    value: { apiVersion: "v1", error: {code: "404", message: "The address requested is an invalid delivery address.", errors: [] } }
      
Multiple-Addresses-Found:
    description: More than one address was found matching the requested address.
    value: { apiVersion: "v1", error: {code: "404", message: "More than one address was found matching the requested address.", errors: [] } }
```

### Domestic Label Error
```errors
Invalid Rate Indicator :
Description: The rate indicator is missing or invalid.
[packageDescription:rateIndicator/mailClass - Rate Indicator value should match [2B, S2, SP] when [FIRST_CLASS] is selected]
```

## Postman Collection

Here is a Postman Collection of the above curl commands above that you can utilize to help in getting a jump start of using USPS APIs. 

Example postman collection file: [Example postman collection.json](https://github.com/USPS/api-examples/Example-Postman.postman_collection.json)
