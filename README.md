# USPS API
The USPS Developer Portal is for software developers who would like to integrate Postal Service functionality into their ecommerce, shipping, or logistics systems. Access and use USPS tools and resources conveniently within your site or application. You have the ability to try out the APIs in our Test environment.

## Getting Started in Test Environment
To use the USPS APIs in the Test Environment you must be a registered user.
1. Apply for a free USPS.com account.
2. Access the [USPS Test (CAT) Business Customer Gateway](https://gateway-cat.usps.com/eAdmin/view/signin) and follow the prompts to create a USPS.com business account.
3. Using your USPS account, access the [USPS Test (CAT) Developer Portal](https://developer-cat.usps.com) by clicking the Sign Up / Login link. 
4. Registering your application and retrieve your consumer key and consumer secret from the credential section.
5. Additionally you will need to have a Customer ID (CRID) and Mailer ID (MID)

## Getting Started in Production
To use the USPS APIs you must be a registered user.
1. Apply for a free USPS.com account.
2. Access the [Business Customer Gateway](https://gateway.usps.com/eAdmin/view/signin) and follow the prompts to create a USPS.com business account.
3. Using your USPS account, access the [USPS Developer Portal](https://developer.usps.com) by clicking the Sign Up / Login link. 
4. Registering your application and retrieve your consumer key and consumer secret from the credential section.
5. Additionally, you will need to have a Customer ID (CRID) and Mailer ID (MID)

## Using the APIs in the USPS Test Environment
All USPS APIs listed below are Production URL.  To use the API in Test Environment you need to change the URL from https://api.usps.com to https://api-cat.usps.com.

## OAuth Token
Your exclusive access to USPS APIs is protected using OAuth 2.0 industry standards. All USPS APIs require an OAuth 2.0 access token to be conveyed in the `Authorization` header, using the `Bearer` token scheme. Once you sign-in to the API developer portal, you may get the OAuth credentials you will need to register your application `Add App`. See "Getting Started" for further information about signing-in, registering your application, and additional USPS account credentials you will need to integrate your application with USPS APIs. You are responsible for securing the application credentials once issued. Please contact USPS if your credentials have been disclosed to external parties and we will issue new credentials.

OAuth 2.0 API

OAuth access tokens are used to grant authorized access to USPS APIs. Access tokens will expire, requiring applications to periodically check the expiration time and get new ones. The OAuth 2.0 client credentials grant type is the only OAuth flow presently supported. Other OAuth flows will become supported in future releases. You will need to add an app in the API Developer Portal to get Consumer Key and Consumer Secret values. You will need a valid customer registration ID (CRID) and mailer ID (MID) in order to get an access token. Contact your USPS Business Customer Gateway Business System Administrator (BSA) for this information.

Prepare your credentials for use in the following examples:
```sh
CLIENT_ID=XXXX
CLIENT_SECRET=XXXX
CUSTOMER_REGISTRATION_ID=XXXX
MAILER_ID=XXXX
```

### Example OAuth Token request (V1):
```sh
curl -X 'POST' "https://api.usps.com/oauth2/v1/token" \
     --header 'Content-Type:application/x-www-form-urlencoded;charset=utf-8' \
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

###  Inspect the values associated with the OAuth access token profile (V1):
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

### Example Authorization Code request (V3):
```sh
curl -X 'POST' "https://api.usps.com/oauth2/v3/authorize' \
	--header 'Cookie: TINTCYALF=$TINTCYALF' \
	--header 'Content-Type: application/json' \
	--header 'Authorization: Basic N0MyejJiS1FodDJUTEJjVTE2VmxlZUplQm1hdExiMjQ6TENtSE85RUFENXk0bUNURA==' \
	--data '{
		"client_id": "$CLIENT_ID",
		"response_type": "code",
		"redirect_uri": "https://mycompany.com/authorize",
		"scope": "prices labels tracking",
		"state": "nonce=abscdefg#"
		}'
```
```json
{
    "code": "hgo6sfHd",
    "state": "nonce=abscdefg#"
}
```

### Example OAuth Client Credentials Token request (V3):
```sh
curl -X 'POST' "https://api.usps.com/oauth2/v3/token" \
     --header 'Content-Type: application/json' \
     --data '{
		"client_id": "$CLIENT_ID",
		"client_secret": "$CLIENT_SECRET",
		"grant_type": "client_credentials"
		}'
```
```json
{
    "access_token": "XXXXXXXXXXXXXXXXX",
    "token_type": "Bearer",
    "issued_at": "1111111111111",
    "expires_in": "11111",
    "status": "approved",
    "scope": "addresses service-delivery-standards  locations prices tracking labels",
    "issuer": "api.usps.com",
    "client_id": "XXXXXXXXXXXXXXXXXXXXXXXXXX",
    "application_name": "XXXXXX-XXXX-XXXX-XXX-XXXXXXXXXXXX",
    "api_products": "[XXXXXXX]",
    "public_key": "XXXXXXXXXXXXXXXXX"
}
```

Set your access token to an environment variable for use in subsequent requests.
```sh
export $TOKEN=<access_token>
```

### Example OAuth Authorization Code Token request (V3):
```sh
curl -X 'POST' "https://api.usps.com/oauth2/v3/token" \
     --header 'Content-Type: application/json' \
     --data '{
		"client_id": "$CLIENT_ID",
		"client_secret": "$CLIENT_SECRET",
		"code": "$CODE",
		"redirect_uri": "https://mycompany.com/authorize",
		"scope": "prices labels tracking",
		"state": "nonce=abscdefg#",
		"grant_type": "authorization_code"
		}'
```
```json
{
    "access_token": "XXXXXXXXXXXXXXXXX",
    "token_type": "Bearer",
    "issued_at": "1111111111111",
    "expires_in": "11111",
    "status": "approved",
    "scope": "prices labels tracking",
    "issuer": "api.usps.com",
    "refresh_token": "XXXXXXXXXXXXXXXXX",
    "refresh_token_issued_at": 1111111111111,
    "refresh_token_status": "approved",
    "refresh_token_expires_in": 11111,
    "refresh_count": 0,
    "client_id": "XXXXXXXXXXXXXXXXXXXXXXXXXX",
    "application_name": "XXXXXX-XXXX-XXXX-XXX-XXXXXXXXXXXX",
    "api_products": "[XXXXXXX]",
    "public_key": "XXXXXXXXXXXXXXXXX"
}
```

### Example OAuth Refresh Token request (V3):
```sh
curl -X 'POST' "https://api.usps.com/oauth2/v3/token" \
     --header 'Content-Type: application/json' \
     --data '{
		"client_id": "$CLIENT_ID",
		"client_secret": "$CLIENT_SECRET",
		"grant_type": "refresh_token",
		"refresh_token": "XXXXXXXXXXXXXXXXX"
		}'
```
```json
{
    "access_token": "XXXXXXXXXXXXXXXXX",
    "token_type": "Bearer",
    "issued_at": "1111111111111",
    "expires_in": "11111",
    "status": "approved",
    "scope": "prices labels tracking",
    "issuer": "api.usps.com",
    "refresh_token": "XXXXXXXXXXXXXXXXX",
    "refresh_token_issued_at": 1111111111111,
    "refresh_token_status": "approved",
    "refresh_token_expires_in": 11111,
    "refresh_count": 1,
    "client_id": "XXXXXXXXXXXXXXXXXXXXXXXXXX",
    "application_name": "XXXXXX-XXXX-XXXX-XXX-XXXXXXXXXXXX",
    "api_products": "[XXXXXXX]",
    "public_key": "XXXXXXXXXXXXXXXXX"
}
```

### Example Revoke OAuth Token request (V3):
```sh
curl -X 'POST' "https://api.usps.com/oauth2/v3/revoke' \
	--header 'Content-Type: application/json' \
	--header 'Authorization: Basic N0MyejJiS1FodDJUTEJjVTE2VmxlZUplQm1hdExiMjQ6TENtSE85RUFENXk0bUNURA==' \
	--data '{
		"token": "ExDTmpomcDt6pTbFVvSgQ1km39YmX8Oy",
		"token_type_hint": "refresh_token"
		}'
```
```
    200 OK
```


## Addresses
The Address API provides validation and standardization of USPS domestic addresses, city and state names, and ZIP Codes. When a valid address, city, and state are provided, for instance, the API will return the standardized address along with the 11-digit delivery point ZIP Code. This API also supports lookups of city and state names by ZIP Code or ZIP Code by city and state names. This functionality enables shippers to validate and standardize address information during order entry. Thus, saving time and money by reducing shipping delays and errors due to incorrect or improper address information. 

###  Address
This API supports ZIP Code and City/State lookups and validates and standardizes USPS domestic addresses, city and state names, and ZIP Codes in accordance with USPS addressing standards. This API supports USPS standardized addresses including the ZIP+4, signifying a USPS delivery point, given a street address, a city, and a state.
```sh
curl	-X 'GET' 'https://api.usps.com/addresses/v1/address?streetAddress=3120%20M%20St&secondarayAddress=NW&city=Washington&state=DC&ZIPCode=20027&ZIPPlus4=3704' \
	--header 'accept: application/json' \
	--header 'x-user-id: XXXXXXXXXXXX' \
	--header 'authorization: Bearer $TOKEN' \
```
Response:
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
curl	-X 'GET' 'https://api.usps.com/addresses/v1/city-state?ZIPCode=30022' \
	--header 'accept: application/json' \
	--header 'X-User-Id: XXXXXXXXXXX' \
	--header 'Authorization: Bearer $TOKEN' \
```
Response:
```json
{
    "city": "ALPHARETTA",
    "state": "GA"
} 
```
###  ZIPCode
Returns the ZIP Code and ZIP Code + 4 corresponding to the given address, city, and state (use USPS state abbreviations).
```sh
curl	-X 'GET' 'https://api.usps.com/addresses/v1/zipcode?streetAddress=1273%20Pale%20San%20Vitores%20RD&city=Tamuning&state=GU' \
	--header 'accept: application/json' \
	--header 'x-user-id: XXXXXXXXXXXX' \
	--header 'authorization: Bearer $TOKEN' \
```
Response:
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


## Appointments
The API allows users to check for available appointment slots and create an appointment.

### Appointment Availability - Get
Appointment-Availability request provides ability to find available appointments.
```sh
curl	-X 'GET' 'https://api.usps.com/appointments/v3/appointment-availability?mailClass=USPS_CONNECT_SAME_DAY&dropshipKey=PZ10171&appointmentType=PALLET&appointmentStartDate=2024-03-28&appointmentEndDate=2024-03-28' \
	--header 'Accept: application/json' \
	--header 'Authorization: Bearer $TOKEN' \
	--data ''
```
Response:
```json
{
    "appointmentSlots": [
        {
            "slot": "2024-03-28T07:00:00.00Z"
        },
        {
            "slot": "2024-03-28T07:15:00.00Z"
        },
        {
            "slot": "2024-03-28T07:30:00.00Z"
        },
        {
            "slot": "2024-03-28T07:45:00.00Z"
        },
        {
            "slot": "2024-03-28T10:00:00.00Z"
        },
        {
            "slot": "2024-03-28T10:15:00.00Z"
        },
        {
            "slot": "2024-03-28T10:30:00.00Z"
        },
        {
            "slot": "2024-03-28T10:45:00.00Z"
        },
        {
            "slot": "2024-03-28T11:00:00.00Z"
        },
        {
            "slot": "2024-03-28T11:15:00.00Z"
        },
        {
            "slot": "2024-03-28T11:30:00.00Z"
        },
        {
            "slot": "2024-03-28T11:45:00.00Z"
        },
        {
            "slot": "2024-03-28T12:00:00.00Z"
        },
        {
            "slot": "2024-03-28T12:15:00.00Z"
        },
        {
            "slot": "2024-03-28T12:30:00.00Z"
        },
        {
            "slot": "2024-03-28T12:45:00.00Z"
        },
        {
            "slot": "2024-03-28T13:00:00.00Z"
        },
        {
            "slot": "2024-03-28T13:15:00.00Z"
        },
        {
            "slot": "2024-03-28T13:30:00.00Z"
        },
        {
            "slot": "2024-03-28T13:45:00.00Z"
        },
        {
            "slot": "2024-03-28T14:00:00.00Z"
        }
    ]
}
```

### Appointment - Create
FAST-Appointments request provides the ability to create an appointment.
```sh
curl -X 'POST' 'https://api.usps.com/v3/fast-appointments' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer $TOKEN' \
--data '{
    "appointmentType": "PALLET",
    "CRID": "94879959",
    "dropshipKey": "PZ10171",
    "preferredAppointment": "2024-03-28T13:00:00Z",
    "mailClass": "USPS_CONNECT_SAME_DAY",
    "palletPositionCount": 0,
    "processingCategory": "MACHINABLE",
    "sackCount": 2,
    "bundleCount": 3,
    "parcelCount": 2
}' 
```
Response:
```json
{
    "appointmentType": "PALLET",
    "CRID": "94879959",
    "dropshipKey": "PZ10171",
    "mailClass": "USPS_CONNECT_SAME_DAY",
    "palletPositionCount": 0,
    "preferredAppointment": "2024-03-28T13:00:00Z",
    "bundleCount": 3,
    "parcelCount": 2,
    "processingCategory": "MACHINABLE",
    "sackCount": 2,
    "appointmentId": 913227005
}
```


## Carrier Pickup
The API supports customers scheduling a carrier to pick up your packages on the next USPS delivery day (Monday through Saturday, excluding holidays) for free. Carrier Pickup is available for sending packages using Priority Mail Express, Priority Mail, First Class Package Service Commercial, international delivery services, or for returned merchandise. You are able check availability, schedule, change, cancel and inquire on a carrier pickup.

### Carrier Pickup - Eligibility
Check carrier pickup service availability at the specified address. Either the city and state or the ZIP code is required, in addition to the street address. Responds with a 200 HTTP status code and includes the USPS standardized address when this location is eligible for carrier pickup.
```sh
curl	-X 'GET' 'https://api.usps.com/pickup/v1/carrier-pickup/eligibility?streetAddress=4120%20Bingham%20Ave&city=Saint%20Louis&state=MO&ZIPCode=63116' \
	--header 'Accept: application/json' \
	--header 'Authorization: Bearer $TOKEN' \
	--data ''
```
Response:
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
Schedule a carrier pickup on a specified date. If the address is eligible for carrier pickup, then any future date is possible. Scheduling a same-day pickup is limited and based on the time of day for the request.
```sh
curl -X 'POST' 'https://api.usps.com/pickup/v1/carrier-pickup' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer $TOKEN' \
--data-raw '{
  "pickupDate": "2023-04-21",
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
Response:
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
Get the previously scheduled carrier pickup by confirmation number. Responds with the entity tag (ETag) to use when updating or cancelling this pickup.
 ```sh
curl	-X 'GET' 'https://api.usps.com/pickup/v1/carrier-pickup/{Confirmation Number}' \
	--header 'Accept: application/json' \
	--header 'Authorization: Bearer $TOKEN' \
```
Response:
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
Update information contained in a previously scheduled carrier pickup such as the pickup date, the types and counts of packages for the carrier to pick up, the weight or the pickup location.
 ```sh
curl	--request PUT 'https://api.usps.com/pickup/v1/carrier-pickup/{Confirmation Number}' \
	--header 'Accept: application/json' \
	--header 'Content-Type: application/json' \
	--header 'If-Match: XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX' \
	--header 'Authorization: Bearer $TOKEN' \
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
Response:
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
Cancel a previously scheduled carrier pick up. A carrier pickup can no longer be updated or cancelled once cancelled. Responds with a 200 HTTP status code when the carrier pickup has been cancelled.
 ```sh
curl --request DELETE 'https://api.usps.com/pickup/v1/carrier-pickup/{Confirmation Number}' \
--header 'Accept: application/json' \
--header 'If-Match: XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX' \
--header 'Authorization: Bearer $TOKEN' \
--data ''
```
Response: 200 OK if successful


## Containers
The Intelligent Mail Container barcode (IMcb) provides visibility at the mail aggregate level. It is used on mailer-generated pallet labels to uniquely identify pallets and similar containers (i.e. All Purpose Containers, hampers, pallet boxes, etc.) in addition to identifying the mail owner and, it provides a link between the electronic mailing record and the physical mail aggregate.

### Add Packages or Mail to a Container Manifest
When a user creates a container, the container can be updated with additional tracking numbers with this endpoint.
```sh
curl	-X 'POST' 'https://api.usps.com/containers/v3/containers/99M900066875EMA000051/packages' \
	--header 'Content-Type: application/json' \
	--header 'Authorization: Bearer $TOKEN' \
	--data '{
		"trackingNumbers": [
			"9261212345678900017627" 
		]
	}'
```
Response:
```json
{
    "containerID": "99M900066875EMA000051",
    "sortType": "SACK",
    "destinationEntryFacilityType": "DESTINATION_DELIVERY_UNIT",
    "destinationZIPCode": "06701",
    "mailClass": "PARCEL_SELECT",
    "originAddress": {
        "streetAddress": "900 BRENTWOOD RD NE",
        "secondaryAddress": null,
        "city": "Washington",
        "state": "DC",
        "urbanization": "",
        "ZIPCode": "20018",
        "ZIPPlus4": "0000"
    },
    "mailerName": "John Smith AC.",
    "processingCategory": "MACHINABLE",
    "containerTopology": "NESTED",
    "warnings": [],
    "MID": "XXXXXXXXX"
}
```

### Create a Container Label
Allows the caller to get a container label and associate packages to that container.
```sh
curl -X 'POST' 'https://api.usps.com/containers/v3/containers' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer $TOKEN' \
--data '{
    "imageInfo": {
        "imageType": "PDF",
        "labelType": "6X4LABEL"
    },
	"trackingNumbers":[
        "9261212345678900017634"
    ],
    "sortType": "SACK",
    "destinationEntryFacilityType": "DESTINATION_DELIVERY_UNIT",
    "destinationZIPCode": "06701",
    "mailClass": "PARCEL_SELECT",
    "originAddress": {
        "streetAddress": "900 BRENTWOOD RD NE",
        "secondaryAddress": "",
        "city": "Washington",
        "state": "DC",
        "ZIPCode": "20018",
        "ZIPPlus4": "0000",
        "urbanization": ""
    },
    "mailerName": "John Smith AC.",
    "processingCategory": "MACHINABLE",
    "containerTopology": "NESTED",
    "MID": "XXXXXXXXX"
}' 
```
Response:
```json
--XkxytuhvSSXDz-aV1q2YverB
Content-Type: application/json
Content-Disposition: form-data; name="containerMetadata"

{
    "containerID": "99M900066875EMA000052",
    "sortType": "SACK",
    "destinationEntryFacilityType": "DESTINATION_DELIVERY_UNIT",
    "destinationZIPCode": "06701",
    "mailClass": "PARCEL_SELECT",
    "originAddress": {
        "streetAddress": "900 BRENTWOOD RD NE",
        "secondaryAddress": "",
        "city": "Washington",
        "state": "DC",
        "urbanization": "",
        "ZIPCode": "20018",
        "ZIPPlus4": "0000"
    },
    "mailerName": "John Smith AC.",
    "processingCategory": "MACHINABLE",
    "containerTopology": "NESTED",
    "warnings": [],
    "MID": "XXXXXXXXX"
}
--XkxytuhvSSXDz-aV1q2YverB
Content-Type: application/pdf
Content-Disposition: form-data; filename="labelImage.pdf"; name="labelImage"

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Pj4KZW5kb2JqCjExIDAgb2JqCjw8IC9UeXBlIC9QYWdlcwovQ291bnQgMQovS2lkcyBbOSAwIFIgXSA+PgplbmRvYmoKMTQgMCBvYmoKPDwKICAvVHlwZSAvQ2F0YWxvZwogIC9QYWdlcyAxMSAwIFIKICAvTGFuZyAoeC11bmtub3duKQo+PgplbmRvYmoKMTAgMCBvYmoKPDwKICAvRm9udCA8PCAvRjEgMTIgMCBSIC9GMyAxMyAwIFIgPj4KICAvUHJvY1NldCBbL1BERiAvSW1hZ2VCIC9JbWFnZUMgL1RleHRdCiAgL1hPYmplY3QgPDwgL0ltMSA1IDAgUiA+PgogIC9Db2xvclNwYWNlIDw8IC9JQ0MyIDQgMCBSID4+Cj4+CmVuZG9iagp4cmVmCjAgMTUKMDAwMDAwMDAwMCA2NTUzNSBmIAowMDAwMDAwMDE1IDAwMDAwIG4gCjAwMDAwMDAxMzEgMDAwMDAgbiAKMDAwMDAwMjY4OCAwMDAwMCBuIAowMDAwMDAyNzA4IDAwMDAwIG4gCjAwMDAwMDI3NDEgMDAwMDAgbiAKMDAwMDAwMzE3MiAwMDAwMCBuIAowMDAwMDAzMTkxIDAwMDAwIG4gCjAwMDAwMDQ3ODAgMDAwMDAgbiAKMDAwMDAwNDgwMCAwMDAwMCBuIAowMDAwMDA1MzQzIDAwMDAwIG4gCjAwMDAwMDUyMDkgMDAwMDAgbiAKMDAwMDAwNDk5MiAwMDAwMCBuIAowMDAwMDA1MDk4IDAwMDAwIG4gCjAwMDAwMDUyNjggMDAwMDAgbiAKdHJhaWxlcgo8PAogIC9Sb290IDE0IDAgUgogIC9JbmZvIDEgMCBSCiAgL0lEIFs8Mjc2NDQ2ODg1RjA5NTgyMEZDMUIyOUIwQjdDMTg1OTI+IDwyNzY0NDY4ODVGMDk1ODIwRkMxQjI5QjBCN0MxODU5Mj5dCiAgL1NpemUgMTUKPj4Kc3RhcnR4cmVmCjU1MDEKJSVFT0YK
--XkxytuhvSSXDz-aV1q2YverB--
```


## Domestic Label
The USPS Labels API allows you to digitally create mailing labels for Parcel Select Ground, Parcel Select Destination Entry, Parcel Select Lightweight, Connect Local, Connect Regional, Priority Mail, Priority Mail Express, First-Class Package Services, Bound Printed Matter, Library Mail, and Media Mail mail classes. To use this API, the caller must establish an Enterprise Payment Account to pay for the Label transactions being generated during each API Call. You can find details on how to establish your Enterprise Payment Account at Fact Sheet. The USPS Labels API uses a token to authorize use of an Enterprise Payment. The specification to generate the token is in the payments API. 

The following fields are used to assure that a label is generated:
- Account Type, specify the type of payment account, either EPS or PERMIT;
- Account Number, specify the account number for either the EPS or PERMIT account (e.g., permit number), and
- Permit ZIP Code, specify the permit ZIP code for the PERMIT account. it is not required for an EPS account.

###  Set the payment account for Domestic Label (V2):
```sh
curl 	-X 'POST' 'https://api.usps.com/payments/v2/payment-authorization' \
		--header 'Content-Type: application/json' \
		--header 'Authorization: Bearer $TOKEN' \
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
Response:
```json
{
"paymentAuthorizationToken": "XXXXXXXXXXXXXXXXXXXXXX",   
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

Set your access token to an environment variable for use in subsequent requests.
```sh
export $PAYMENTTOKEN=<paymentAuthorizationToken>
```

### Domestic Label Request (V2)
Save the example request body to a file: [domesticlabel-v2-request.json](https://github.com/USPS/api-examples/blob/main/domesticlabel-v2-request.json)
```sh
curl 	-X 'POST' 'https://api.usps.com/labels/v2/label' \
		--header 'X-Payment-Authorization-Token: $PAYMENTTOKEN'\
		--header 'Content-Type: application/json' \
		--header 'Authorization: Bearer $TOKEN' \
		--data @domesticlabel-v2-request \
     
```
Response: 
[domesticlabel-v2-response.json](https://github.com/USPS/api-examples/blob/main/domesticlabel-v2-response.json)

###  Set the payment account for Domestic Label (V3):
```sh
curl 	-X 'POST' 'https://api.usps.com/payments/v3/payment-authorization' \
		--header 'Content-Type: application/json' \
		--header 'Authorization: Bearer $TOKEN' \
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
Response:
```json
{
"paymentAuthorizationToken": "XXXXXXXXXXXXXXXXXXXXXX",   
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

Set your access token to an environment variable for use in subsequent requests.
```sh
export $PAYMENTTOKEN=<paymentAuthorizationToken>
```

### Domestic Label Request (V3)
Save the example request body to a file: [domesticlabel-v3-request.json](https://github.com/USPS/api-examples/blob/main/domesticlabel-v3-request.json)
```sh
curl 	-X 'POST' 'https://api.usps.com/labels/v3/label' \
		--header 'X-Payment-Authorization-Token: $PAYMENTTOKEN'\
		--header 'Content-Type: application/json' \
		--header 'Authorization: Bearer $TOKEN' \
		--data @domesticlabel-v3-request \
     
```
Response: 
[domesticlabel-v3-response.json](https://github.com/USPS/api-examples/blob/main/domesticlabel-v3-response.json)

### Edit Label - (V3)
Allow customers to edit package attributes for previously created labels including weight, dimensions, rate indicator, processing category and containers. These fields eligible for editing will not impact label images, so previous label images can still be used. Changing these rate ingredients may effect the prices of the label. Therefore, the Payment Authorization token is required.

Note: Label edits will not be supported for the following scenarios, instead unused label refunds should be requested and new labels should be created.

	- All label edits are disallowed if the original label was created with a suppressPostage = false
	- No dimensional updates are supported for Cubic Softpack Labels
	- Cubic labels can not be edited to non-cubic rate indicators
```sh
curl 	-X 'PATCH' 'https://api.usps.com/labels/v3/label/1234567890123456789012' \
		--header 'X-Payment-Authorization-Token: $PAYMENTTOKEN'\
		--header 'Content-Type: application/json' \
		--header 'Authorization: Bearer $TOKEN' \
		--data '{
			{
				"op": "replace",
				"path": "/packageDescription/weight",
				"value": 1
			},
			{
				"op": "replace",
				"path": "/packageDescription/length",
				"value": 10
			},
			{
				"op": "replace",
				"path": "/packageDescription/height",
				"value": 7
			},
			{
				"op": "replace",
				"path": "/packageDescription/width",
				"value": 4.5
			},
			{
				"op": "replace",
				"path": "/packageDescription/girth",
				"value": 0.50
			},
			{
				"op": "replace",
				"path": "/packageDescription/processingCategory",
				"value": "NON_MACHINABLE"
			},
			{
				"op": "replace",
				"path": "/packageDescription/containers",
				"value": [
					{
						"containerID": "123456789012345",
						"sortType": "PALLET"
					}
				]
			},
			{
				"op": "replace",
				"path": "/packageDescription/rateIndicator",
				"value": "CP"
			}
		}'
```
Response:
```json
{
    "labelAddress": {
        "streetAddress": "STE 150",
        "secondaryAddress": "1100 WYOMING ST",
        "city": "SAINT LOUIS",
        "state": "MO",
        "ZIPCode": "63118",
        "ZIPPlus4": "2628",
        "firstName": "JOE",
        "lastName": "DOE",
        "ignoreBadAddress": false
    },
    "routingInformation": "123456789102",
    "trackingNumber": "{{trackingNumber}}",
    "postage": 7.05,
    "extraServices": [
        {
            "name": "USPS Tracking",
            "price": 0.0,
            "SKU": "XXXXXXXXXXXXX"
        }
    ],
    "zone": "01",
    "commitment": {
        "name": "6 Days",
        "scheduleDeliveryDate": "2024-06-22"
    },
    "weightUOM": "LB",
    "weight": 1.0,
    "fees": [],
    "labelBrokerID": "",
    "constructCode": "C01",
    "SKU": "XXXXXXXXXXXXX"
}
```


## Domestic Prices
The Prices API can be used to look-up postage rates for domestic packages:
- Lookup Base Postage based on a set of given package characteristics
- Lookup Base Postage based on a given SKU
- Lookup Extra Service Postage based on a given Extra Service Code and Mail Class
- The USPS has introduced Stock Keeping Units (SKUs), creating unique identifiers for USPS products, and making them available online for mailer download.

For specifications such as package dimensions, delivery information, etc., please refer to the Domestic Mail Manual (DMM). For a list of published rates please refer to the USPS Price List. To discover the rate ingredients for this API, look at Publication 205.

### Domestic Prices - Base Rates Request (V1)
```sh
curl 	-X 'POST' 'https://api.usps.com/prices/v1/base-rates/search' \
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
Response:
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
### Domestic Prices - Base Rates Request (V3)
```sh
curl 	-X 'POST' 'https://api.usps.com/prices/v3/base-rates/search' \
		--header 'Content-Type: application/json' \
		--header 'Authorization: Bearer $TOKEN' \
		--data '{
			"originZIPCode": "78664",
			"destinationZIPCode": "78665",
			"weight": 7,
			"length": 9,
			"width": 0.25,
			"height": 6,
			"mailClass": "PARCEL_SELECT",
			"processingCategory": "MACHINABLE",
			"destinationEntryFacilityType": "NONE",
			"rateIndicator": "DR",
			"priceType": "COMMERCIAL",
			"accountType": "EPS",
			"accountNumber": "XXXXXXXXXX",
			"mailingDate": "2023-05-25"
}'
```
Response:
```json
{
    "totalBasePrice": 8.02,
    "rates": [
        {
            "SKU": "DVXR0XXXXC01070",
            "description": "Parcel Select Ground Machinable Dimensional Rectangular",
            "priceType": "COMMERCIAL",
            "price": 8.02,
            "weight": 7,
            "dimWeight": 0,
            "fees": [],
            "startDate": "2023-01-22",
            "endDate": "",
            "warnings": [],
            "mailClass": "PARCEL_SELECT",
            "zone": "01"
        }
    ]
}
```
### Domestic Prices - Extra Services Rates Request (V1)
Given a set of rate ingredients, returns a list of potential extra service rates. If contractId and productId are present, include contract-based rates in the results.
```sh
curl 	-X 'POST' 'https://api.usps.com/prices/v1/extra-service-rates/search' \
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
Response:
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
### Domestic Prices - Extra Services Rates Request (V3)
Returns eligible extra service prices, descriptions, and SKUs given a set of package rate ingredients.
```sh
curl 	-X 'POST' 'https://api.usps.com/prices/v3/extra-service-rates/search' \
		--header 'Content-Type: application/json' \
		--header 'Authorization: Bearer $TOKEN' \
		--data '{
			"extraService": 415,
			"mailClass": "PARCEL_SELECT",
			"priceType": "RETAIL",
			"itemValue": 0,
			"weight": 5,
			"mailingDate": "2023-05-25",
			"accountType": "EPS",
			"accountNumber": "XXXXXXXXXX"
		}'
```
Response:
```json
{
    "extraService": "415",
    "name": "Label Delivery",
    "sku": "DXQX0XXXXCX0000",
    "priceType": "RETAIL",
    "price": 1.25,
    "warnings": []
}
```
### Domestic Prices - Total Rates Request (V3)
Returns an eligible price given a set of package rate ingredients.
```sh
curl 	-X 'POST' 'https://api.usps.com/prices/v3/total-rates/search' \
		--header 'Content-Type: application/json' \
		--header 'Authorization: Bearer $TOKEN' \
		--data '{
			"originZIPCode": "36105",
			"destinationZIPCode": "78207",
			"weight": 7,
			"length": 9,
			"width": 0.25,
			"height": 6,
			"mailClass": "PARCEL_SELECT",
			"priceType": "COMMERCIAL",
			"mailingDate": "2023-07-28",
			"accountType": "EPS",
			"accountNumber": "XXXXXXXXXX",
			"itemValue": 500,
			"extraServices": [
				415
			]
		}'
```
Response:
```json
{
    "rateOptions": [
        {
            "totalBasePrice": 4.59,
            "totalPrice": 5.84,
            "rates": [
                {
                    "description": "Parcel Select Machinable DDU Single-piece",
                    "priceType": "COMMERCIAL",
                    "price": 4.59,
                    "weight": 7.0,
                    "dimWeight": 0.0,
                    "fees": [],
                    "startDate": "2023-07-09",
                    "endDate": "",
                    "mailClass": "PARCEL_SELECT",
                    "zone": "00",
                    "SKU": "DVXP0XXUXC00070"
                }
            ],
            "extraServices": [
                {
                    "extraService": "415",
                    "name": "Label Delivery",
                    "priceType": "COMMERCIAL",
                    "price": 1.25,
                    "warnings": [],
                    "SKU": "DXQX0XXXXCX0000"
                }
            ]
        },
        {
            "totalBasePrice": 7.86,
            "totalPrice": 9.11,
            "rates": [
                {
                    "description": "Parcel Select Machinable DNDC Single-piece",
                    "priceType": "COMMERCIAL",
                    "price": 7.86,
                    "weight": 7.0,
                    "dimWeight": 0.0,
                    "fees": [],
                    "startDate": "2023-07-09",
                    "endDate": "",
                    "mailClass": "PARCEL_SELECT",
                    "zone": "00",
                    "SKU": "DVXP0XXCXC00070"
                }
            ],
            "extraServices": [
                {
                    "extraService": "415",
                    "name": "Label Delivery",
                    "priceType": "COMMERCIAL",
                    "price": 1.25,
                    "warnings": [],
                    "SKU": "DXQX0XXXXCX0000"
                }
            ]
        },
        {
            "totalBasePrice": 6.79,
            "totalPrice": 8.04,
            "rates": [
                {
                    "description": "Parcel Select Machinable DSCF SCF",
                    "priceType": "COMMERCIAL",
                    "price": 6.79,
                    "weight": 7.0,
                    "dimWeight": 0.0,
                    "fees": [],
                    "startDate": "2023-07-09",
                    "endDate": "",
                    "mailClass": "PARCEL_SELECT",
                    "zone": "00",
                    "SKU": "DVXP0XXFTC00070"
                }
            ],
            "extraServices": [
                {
                    "extraService": "415",
                    "name": "Label Delivery",
                    "priceType": "COMMERCIAL",
                    "price": 1.25,
                    "warnings": [],
                    "SKU": "DXQX0XXXXCX0000"
                }
            ]
        },
        {
            "totalBasePrice": 5.74,
            "totalPrice": 6.99,
            "rates": [
                {
                    "description": "Parcel Select Machinable Single-piece",
                    "priceType": "COMMERCIAL",
                    "price": 5.74,
                    "weight": 7.0,
                    "dimWeight": 0.0,
                    "fees": [],
                    "startDate": "2023-07-09",
                    "endDate": "",
                    "mailClass": "PARCEL_SELECT",
                    "zone": "00",
                    "SKU": "DVXP0XXBXC00070"
                }
            ],
            "extraServices": [
                {
                    "extraService": "415",
                    "name": "Label Delivery",
                    "priceType": "COMMERCIAL",
                    "price": 1.25,
                    "warnings": [],
                    "SKU": "DXQX0XXXXCX0000"
                }
            ]
        }
    ]
}
```
### Domestic Prices - Letter Rates Request (V3)
Performs a search for letter prices using the submitted rate ingredients.
```sh
curl 	-X 'POST' 'https://api.usps.com/prices/v3/letter-rates/search' \
		--header 'Content-Type: application/json' \
		--header 'Authorization: Bearer $TOKEN' \
		--data '{
			"weight": 3.5,
			"length": 4.0,
			"height": 5.0,
			"thickness": 0.25,
			"processingCategory": "LETTERS",
			"mailingDate": "2023-11-29",
			"nonMachinableIndicators": {
				"isPolybagged": true,
				"hasClosureDevices": true,
				"hasLooseItems": true,
				"isRigid": true,
				"isSelfMailer": true,
				"isBooklet": true
			}
		}'
```
Response:
```json
{
    "totalBasePrice": 1.75,
    "rates": [
        {
            "SKU": "DFLL0XXXXR00035",
            "description": "First Class Letter Metered",
            "priceType": "RETAIL",
            "price": 1.35,
            "weight": 3.5,
            "dimWeight": null,
            "fees": [
                {
                    "name": "Nonmachinable letter fee",
                    "SKU": "N/A",
                    "price": 0.4
                }
            ],
            "startDate": "2023-07-09",
            "endDate": "",
            "warnings": [],
            "mailClass": "USPS_GROUND_ADVANTAGE",
            "zone": null
        }
    ]
}
```
## International Prices
The International Prices API can be used to look-up postage rates for international packages:
- Lookup International Base Postage based on a set of given package characteristics
- Lookup International Base Postage based on a given SKU
- The USPS has introduced Stock Keeping Units (SKUs), creating unique identifiers for USPS products, and making them available online for mailer download.

For specifications such as package dimensions, delivery information, etc., please refer to the International Mail Manual (IMM). For a list of published rates please refer to the USPS Price List. To discover the rate ingredients for this API, look at Publication 205.

### International Prices - Base Rates Request (V1)
```sh
curl 	-X 'POST' 'https://api.usps.com/international-prices/v1/base-rates/search' \
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
### International Prices - Base Rates Request (V3)
```sh
curl 	-X 'POST' 'https://api.usps.com/international-prices/v3/base-rates/search' \
		--header 'Content-Type: application/json' \
		--header 'Authorization: Bearer $TOKEN' \
		--data '{
			"originZIPCode": "22407",
			"foreignPostalCode": "10109",
			"destinationCountryCode": "CA",
			"destinationEntryFacilityType": "NONE",
			"weight": 0.5,
			"length": 9,
			"width": 15,
			"height": 6,
			"mailClass": "FIRST-CLASS_PACKAGE_INTERNATIONAL_SERVICE",
			"processingCategory": "NON_MACHINABLE",
			"rateIndicator": "SP",
			"priceType": "COMMERCIAL",
			"accountType": "EPS",
			"accountNumber": "XXXXXXXXXX",
			"mailingDate": "2023-05-25"
		}'
```
```json
{
    "rates": [
        {
            "SKU": "IFXP0XXXXB01080",
            "description": "First-Class Package International Service Nonmachinable Single-piece",
            "mailClass": "FIRST-CLASS_PACKAGE_INTERNATIONAL_SERVICE",
            "priceType": "COMMERCIAL",
            "zone": "01",
            "dimWeight": 0,
            "weight": 0.5,
            "fees": [],
            "price": 14.96,
            "startDate": "2023-01-22",
            "endDate": "",
            "warnings": []
        }
    ],
    "totalBasePrice": 14.96
}
```
### International Prices - Extra Services Rates Request (V1)
Given a set of rate ingredients, returns international extra service rates. If contractId and productId are present, include contract-based rates in the results.
```sh
curl 	-X 'POST' 'https://api.usps.com/international-prices/v1/extra-service-rates/search' \
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
Response:
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
### International Prices - Extra Services Rates Request (V3)
Given a set of rate ingredients, returns international extra service rates. If contractId and productId are present, include contract-based rates in the results.
```sh
curl 	-X 'POST' 'https://api.usps.com/international-prices/v3/extra-service-rates/search' \
		--header 'Content-Type: application/json' \
		--header 'Authorization: Bearer $TOKEN' \
		--data '{
			"extraService": 930,
			"mailClass": "PRIORITY_MAIL_INTERNATIONAL",
			"priceType": "RETAIL",
			"itemValue": "500",
			"weight": 4,
			"mailingDate": "2023-05-25",
			"rateIndicator": "SP",
			"destinationCountryCode": "CR",
			"accountType": "EPS",
			"accountNumber": "XXXXXXXXXX"
		}'
```
Response:
```json
{
    "SKU": "IXIP0XXXXCX0500",
    "priceType": "COMMERCIAL",
    "price": 19.55,
    "extraService": "930",
    "name": "Insurance <= $500",
    "warnings": []
}
```
### International Prices - Base Rates List Request (V3)
Given size/weight/destination of pieces, returns a list of potential rates. Can also search for contract rates by providing mailer id, EPS, permit number or vendor number and account. If searching for contract rates, then a specified mail class is required.
```sh
curl 	-X 'POST' 'https://api.usps.com/international-prices/v3/base-rates-list/search' \
		--header 'Content-Type: application/json' \
		--header 'Authorization: Bearer $TOKEN' \
		--data '{
			"originZIPCode": "22407",
			"foreignPostalCode": "10109",
			"destinationCountryCode": "CA",
			"weight": 4,
			"mailingDate": "2024-02-29",
			"length": 6,
			"width": 0.25,
			"height": 3,
			"mailClass": "PRIORITY_MAIL_INTERNATIONAL",
			"accountType": "EPS",
			"accountNumber": "XXXXXXXXXX"
		}'
```
Response:
```json
{
    "rateOptions": [
        {
            "rates": [
                {
                    "mailClass": "PRIORITY_MAIL_INTERNATIONAL",
                    "priceType": "COMMERCIAL_BASE",
                    "dimWeight": 0,
                    "zone": "01",
                    "fees": [],
                    "startDate": "2024-01-21",
                    "endDate": "",
                    "SKU": "IPFE0XXXXB01040",
                    "price": 29.66,
                    "weight": 4,
                    "description": "Priority Mail International ISC Flat Rate Envelope"
                }
            ],
            "totalBasePrice": 29.66
        },
        {
            "rates": [
                {
                    "mailClass": "PRIORITY_MAIL_INTERNATIONAL",
                    "priceType": "COMMERCIAL_BASE",
                    "dimWeight": 0,
                    "zone": "01",
                    "fees": [],
                    "startDate": "2024-01-21",
                    "endDate": "",
                    "SKU": "IPFB2XXXXB01040",
                    "price": 30.67,
                    "weight": 4,
                    "description": "Priority Mail International Machinable ISC Small Flat Rate Box"
                }
            ],
            "totalBasePrice": 30.67
        },
        {
            "rates": [
                {
                    "mailClass": "PRIORITY_MAIL_INTERNATIONAL",
                    "priceType": "COMMERCIAL_BASE",
                    "dimWeight": 0,
                    "zone": "01",
                    "fees": [],
                    "startDate": "2024-01-21",
                    "endDate": "",
                    "SKU": "IPFB0XXXXB01200",
                    "price": 70.55,
                    "weight": 4,
                    "description": "Priority Mail International Machinable ISC Large Flat Rate Box"
                }
            ],
            "totalBasePrice": 70.55
        },
        {
            "rates": [
                {
                    "mailClass": "PRIORITY_MAIL_INTERNATIONAL",
                    "priceType": "COMMERCIAL_BASE",
                    "dimWeight": 0,
                    "zone": "01",
                    "fees": [],
                    "startDate": "2024-01-21",
                    "endDate": "",
                    "SKU": "IPFB1XXXXB01200",
                    "price": 58.06,
                    "weight": 4,
                    "description": "Priority Mail International Machinable ISC Medium Flat Rate Box"
                }
            ],
            "totalBasePrice": 58.06
        },
        {
            "rates": [
                {
                    "mailClass": "PRIORITY_MAIL_INTERNATIONAL",
                    "priceType": "COMMERCIAL_BASE",
                    "dimWeight": 0,
                    "zone": "01",
                    "fees": [],
                    "startDate": "2024-01-21",
                    "endDate": "",
                    "SKU": "IPFE2XXXXB01040",
                    "price": 29.66,
                    "weight": 4,
                    "description": "Priority Mail International Machinable ISC Padded Flat Rate Envelope"
                }
            ],
            "totalBasePrice": 29.66
        },
        {
            "rates": [
                {
                    "mailClass": "PRIORITY_MAIL_INTERNATIONAL",
                    "priceType": "COMMERCIAL_BASE",
                    "dimWeight": 0,
                    "zone": "01",
                    "fees": [],
                    "startDate": "2024-01-21",
                    "endDate": "",
                    "SKU": "IPXX0XXXXB01040",
                    "price": 50.6,
                    "weight": 4,
                    "description": "Priority Mail International ISC Single-piece"
                }
            ],
            "totalBasePrice": 50.6
        },
        {
            "rates": [
                {
                    "mailClass": "PRIORITY_MAIL_INTERNATIONAL",
                    "priceType": "COMMERCIAL_BASE",
                    "dimWeight": 0,
                    "zone": "01",
                    "fees": [],
                    "startDate": "2024-01-21",
                    "endDate": "",
                    "SKU": "IPXX0XXXXB01040",
                    "price": 50.6,
                    "weight": 4,
                    "description": "Priority Mail International Machinable ISC Single-piece"
                }
            ],
            "totalBasePrice": 50.6
        },
        {
            "rates": [
                {
                    "mailClass": "PRIORITY_MAIL_INTERNATIONAL",
                    "priceType": "COMMERCIAL_BASE",
                    "dimWeight": 0,
                    "zone": "01",
                    "fees": [],
                    "startDate": "2024-01-21",
                    "endDate": "",
                    "SKU": "IPFE1XXXXB01040",
                    "price": 29.66,
                    "weight": 4,
                    "description": "Priority Mail International ISC Legal Flat Rate Envelope"
                }
            ],
            "totalBasePrice": 29.66
        }
    ]
}
```
### International Prices - Total Rates Request (V3)
Performs a search for base price and extraServices using the submitted rate ingredients. If itemValue is not included the response will not include insurance, registered mail, and collect on delivery extra services. If the extraService array is not specified then all eligible extra services will be included.
```sh
curl 	-X 'POST' 'https://api.usps.com/international-prices/v3/total-rates/search' \
		--header 'Content-Type: application/json' \
		--header 'Authorization: Bearer $TOKEN' \
		--data '{
			"originZIPCode": "22407",
			"foreignPostalCode": "63118",
			"destinationCountryCode": "CA",
			"weight": 1.2,
			"length": 6,
			"width": 6,
			"height": 6,
			"mailClass": "PRIORITY_MAIL_EXPRESS_INTERNATIONAL",
			"priceType": "COMMERCIAL",
			"mailingDate": "2024-02-29",
			"accountType": "EPS",
			"accountNumber": "XXXXXXXXXX",
			"itemValue": 300,
			"extraServices": [
				955
			]
		}'
```
Response:
```json
{
    "rateOptions": [
        {
            "totalBasePrice": 65.64,
            "totalPrice": 71.44,
            "rates": [
                {
                    "description": "Priority Mail Express International ISC Single-piece",
                    "priceType": "COMMERCIAL_BASE",
                    "price": 65.64,
                    "weight": 1.2,
                    "dimWeight": 0.0,
                    "fees": [],
                    "startDate": "2024-01-21",
                    "endDate": "",
                    "mailClass": "PRIORITY_MAIL_EXPRESS_INTERNATIONAL",
                    "zone": "01",
                    "SKU": "IEXX0XXXXB01020"
                }
            ],
            "extraServices": [
                {
                    "extraService": "955",
                    "name": "Return Receipt",
                    "priceType": "COMMERCIAL",
                    "price": 5.80,
                    "warnings": [],
                    "SKU": "IXRX0XXXXCX0000"
                }
            ]
        },
        {
            "totalBasePrice": 55.49,
            "totalPrice": 61.29,
            "rates": [
                {
                    "description": "Priority Mail Express International ISC Padded Flat Rate Envelope",
                    "priceType": "COMMERCIAL_BASE",
                    "price": 55.49,
                    "weight": 1.2,
                    "dimWeight": 0.0,
                    "fees": [],
                    "startDate": "2024-01-21",
                    "endDate": "",
                    "mailClass": "PRIORITY_MAIL_EXPRESS_INTERNATIONAL",
                    "zone": "01",
                    "SKU": "IEFE2XXXXB01040"
                }
            ],
            "extraServices": [
                {
                    "extraService": "955",
                    "name": "Return Receipt",
                    "priceType": "COMMERCIAL",
                    "price": 5.80,
                    "warnings": [],
                    "SKU": "IXRX0XXXXCX0000"
                }
            ]
        }
    ]
}
```

## Locations
The Locations APIs can be used to find USPS facility addresses, hours of operations, available services and more. The purpose of this API is for users to lookup eligible entry locations to receive destination entry rates. USPS has specific rules for these entry types where the volume is only accepted at specific locations. With that in mind the idea is before a mailer submits a label request they may want to check what the acceptable entry locations are for the different rates. So, they could call the API with a destination ZIP Code of 29601 and we would respond with one facility for the DDU entry, one for SCF, & one for NDC (all of which could be unique facilities).

### Dropoff-locations (V1)
```sh
curl 	-X 'GET' 'https://api.usps.com/locations/v1/dropoff-locations?mailClass=USPS_CONNECT_LOCAL&destinationZIPCode=30342' \
		--header 'Authorization: Bearer $TOKEN'
```

Response:
[dropoff-location-v1-response.json](https://github.com/USPS/api-examples/blob/main/dropoff-location-v1-response.json)

### Dropoff-locations (V3)
```sh
curl 	-X 'GET' 'https://api.usps.com/locations/v3/dropoff-locations?mailClass=PARCEL_SELECT&destinationZIPCode=30343' \
		--header 'Accept: application/json' \
		--header 'Authorization: Bearer $TOKEN'
```

Response:
[dropoff-location-v3-response.json](https://github.com/USPS/api-examples/blob/main/dropoff-location-v3-response.json)

### Post Office Locator (V1)
```sh
curl	-X 'GET' 'https://api.usps.com/locations/v1/post-office-locations?ZIPCode=48197&radius=30&offset=2&limit=3' \
	--header 'Accept: application/json' \
	--header 'Authorization: Bearer $TOKEN' \
```

Response:
[postoffice-locator-v1-response.json](https://github.com/USPS/api-examples/blob/main/postoffice-locator-v1-response.json)

### Post Office Locator (V3)
```sh
curl	-X 'GET' 'https://api.usps.com/locations/v3/post-office-locations?radius=1&ZIPCode=10463' \
	--header 'Authorization: Bearer $TOKEN' \
```

Response:
[postoffice-locator-v3-response.json](https://github.com/USPS/api-examples/blob/main/postoffice-locator-v3-response.json)


## Service Standards
This API supports the service standards for the number of days between the acceptance and delivery of a piece of mail that the Postal Service™ considers to be timely delivery. Service standards are delivery benchmarks for how long customers can expect for the Postal Service to deliver different types of mail from origin to destination — Point A to Point B. Service standards are not necessarily the same as the actual service performance.

### Service Standards - Estimates Request (V1)
Allows customers to get estimates on delivery standards between 3 or 5 digit Zip Codes for Priority Mail Express, Priority Mail, First Class Mail, Marketing Mail, Periodicals, and Package Services. For Service Type Codes to utilize in the API call please refer to Pub 199.
```sh
curl	-X 'GET' 'https://api.usps.com/service-standards/v1/estimates?originZIPCode=10018&destinationZIPCode=95823&acceptDate=3%2F5%2F2023&mailClass=PRIORITY_MAIL&destinationType=HOLD_FOR_PICKUP' \
		--header 'Authorization: Bearer $TOKEN' \
```
Response:
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
### Service Standards - Estimates Request (V3)
Allows customers to get estimates on delivery standards between 3 or 5 digit ZIP Codes for the selected mail classes.
```sh
curl	-X 'GET' 'https://api.usps.com/service-standards/v3/estimates?originZIPCode=10018&destinationZIPCode=95823&acceptanceDate=2023-05-26&mailClass=PRIORITY_MAIL&destinationType=HOLD_FOR_PICKUP&serviceTypeCodes=925' \
		--header 'Authorization: Bearer $TOKEN' \
```
Response:
```json
[
    {
        "mailClass": "PRIORITY_MAIL",
        "destinationType": "HOLD_FOR_PICKUP",
        "acceptanceDateTime": "2023-05-26T12:00:00Z",
        "effectiveAcceptanceDate": "2023-05-26",
        "cutOffTime": 1700,
        "serviceStandard": 2,
        "serviceStandardMessage": "2-Day",
        "acceptanceLocations": {
                "facilityType": "POST OFFICE",
                "streetAddress": "223 W 38TH ST",
                "city": "NEW YORK",
                "state": "NY",
                "ZIPCode": 10018,
                "ZIPPlus4": 9998
        },
        "delivery": {
            "scheduledDeliveryDateTime": "2023-05-30",
            "holdForPickupLocation": {
                    "facilityName": "PARKWAY",
                    "streetAddress": "4301 BROOKFIELD DR",
                    "city": "SACRAMENTO",
                    "state": "CA",
                    "ZIPCode": 95823,
                    "ZIPPlus4": 9998,
                    "closeTimes": {
                        "Monday": 1700,
                        "Tuesday": 1700,
                        "Wednesday": 1700,
                        "Thursday": 1700,
                        "Friday": 1700,
                        "Saturday": 1500,
                        "Sunday": "0000",
                        "holidays": "0000"
                    }
            }
        }
    }
]
```
### Service Standards - Standards Request (V1)
Receives requests and returns the average number of days it will take a package to arrive at its destination for First Class Mail Service, Priority Mail, and Package Services. For Service Type Codes to utilize in the API call please refer to Pub 199.
```sh
curl	-X 'GET' 'https://api.usps.com/service-standards/v1/standards?originZIPCode=40504&destinationZIPCode=95823&mailClass=PRIORITY_MAIL&destinationType=Junk' \
		--header 'Authorization: Bearer $TOKEN' \
```
Response:
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
### Service Standards - Standards Request (V3)
Returns the average number of days it will take a package to arrive at its destination for the selected mail class.
```sh
curl	-X 'GET' 'https://api.usps.com/service-standards/v3/standards?originZIPCode=10018&destinationZIPCode=95823&mailClass=PRIORITY_MAIL&destinationType=HOLD_FOR_PICKUP&serviceTypeCodes=925' \
		--header 'Authorization: Bearer $TOKEN' \
```
Response:
```json
[
    {
        "mailClass": "PRIORITY_MAIL",
        "originZIPCode": 10018,
        "destinationZIPCode": 95823,
        "days": 2,
        "effectiveAcceptanceDate": "2023-05-23",
        "scheduledDeliveryDate": "2023-05-25"
    }
]
```


## Shipment Confirmation Acceptance Notice (SCAN) Form
Shipment Confirmation Acceptance Notice(SCAN) form allows integrators to link multiple domestic and international labels through one Electronic File Number (EFN) and physical SCAN Form (PS Form 5630 or 3152). The USPS scans this form at mailing acceptance to generate an acceptance tracking event for each linked label. 

### SCAN Form - Label Shipment
Save the example request body to a file: [SCANForm-LabelShipment-request.json](https://github.com/USPS/api-examples/blob/main/SCANForm-LabelShipment-request.json)
```sh
curl 	-X 'POST' 'https://api.usps.com/scan-forms/v1/scan-form' \
		--header 'Content-Type: application/json' \
		--header 'Authorization: Bearer $TOKEN' \
		--data @SCANForm-LabelShipment-request.json \
     
```
Response: 
[SCANForm-LabelShipment-response.json](https://github.com/USPS/api-examples/blob/main/SCANForm-LabelShipment-response.json)

### SCAN Form - MID Shipment
Save the example request body to a file: [SCANForm-MIDShipment-request.json](https://github.com/USPS/api-examples/blob/main/SCANForm-MIDShipment-request.json)
```sh
curl 	-X 'POST' 'https://api.usps.com/scan-forms/v1/scan-form' \
		--header 'Content-Type: application/json' \
		--header 'Authorization: Bearer $TOKEN' \
		--data @SCANForm-MIDShipment-request.json \
     
```
Response: 
[SCANForm-MIDShipment-response.json](https://github.com/USPS/api-examples/blob/main/SCANForm-MIDShipment-response.json)

### SCAN Form - Manifest MID Shipment
Save the example request body to a file: [SCANForm-ManifestMIDShipment-request.json](https://github.com/USPS/api-examples/blob/main/SCANForm-ManifestMIDShipment-request.json)
```sh
curl 	-X 'POST' 'https://api.usps.com/scan-forms/v1/scan-form' \
		--header 'Content-Type: application/json' \
		--header 'Authorization: Bearer $TOKEN' \
		--data @SCANForm-ManifestMIDShipment-request.json \
     
```
Response: 
[SCANForm-ManifestMIDShipment-response.json](https://github.com/USPS/api-examples/blob/main/SCANForm-ManifestMIDShipment-response.json)


## Shipping Options
Receive a comprehensive list of pricing, service standards, and shipping options for USPS products within a single API call.

### Shipping Options - Package Shipping Options
Allows users to generate a comprehensive list of shipping options that comply with USPS Shipping Standards, all within a single request.
```sh
curl	-X 'POST' 'https://api.usps.com/shipments/v3/options/search' \
	--header 'Accept: application/json' \
	--header 'Authorization: Bearer $TOKEN' \
	--data '{
		"pricingOptions": [
			{
				"priceType": "COMMERCIAL",
				"paymentAccount": {
					"accountType": "EPS",
					"accountNumber": "XXXXXXXXXX"
				}
			}
		],
		"originZIPCode": "05485-8016",
		"destinationZIPCode": "38746 0230",
		"packageDescription": {
			"weight": 1,
			"length": 1,
			"height": 1,
			"width": 1,
			"girth": 1,
			"mailClass": "PARCEL_SELECT",
			"extraServices": [
				365
			],
			"mailingDate": "2024-05-01",
			"packageValue": 35
		}
	}'
```
Response:
```json
{
    "originZIPCode": "05485-8016",
    "destinationZIPCode": "38746-0230",
    "pricingOptions": [
        {
            "shippingOptions": [
                {
                    "mailClass": "PARCEL_SELECT",
                    "rateOptions": [
                        {
                            "commitment": {
                                "name": "3 Days",
                                "scheduleDeliveryDate": "2024-05-04"
                            },
                            "totalPrice": 3.40,
                            "totalBasePrice": 3.40,
                            "rates": [
                                {
                                    "description": "Parcel Select Nonmachinable DDU Single-piece",
                                    "startDate": "2024-01-21",
                                    "endDate": "",
                                    "price": 3.4,
                                    "zone": "00",
                                    "weight": 1.0,
                                    "dimensionalWeight": 0.0,
                                    "fees": [],
                                    "SKU": "DVXP0XXUXC00010"
                                }
                            ],
                            "extraServices": [
                                {
                                    "name": "Global Direct Entry",
                                    "price": 0.00,
                                    "SKU": "NA"
                                }
                            ]
                        },
                        {
                            "commitment": {
                                "name": "3 Days",
                                "scheduleDeliveryDate": "2024-05-04"
                            },
                            "totalPrice": 5.48,
                            "totalBasePrice": 5.48,
                            "rates": [
                                {
                                    "description": "Parcel Select Nonmachinable DNDC Single-piece",
                                    "startDate": "2024-01-21",
                                    "endDate": "",
                                    "price": 5.48,
                                    "zone": "00",
                                    "weight": 1.0,
                                    "dimensionalWeight": 0.0,
                                    "fees": [],
                                    "SKU": "DVXP0XXCXC00010"
                                }
                            ],
                            "extraServices": [
                                {
                                    "name": "Global Direct Entry",
                                    "price": 0.00,
                                    "SKU": "NA"
                                }
                            ]
                        },
                        {
                            "commitment": {
                                "name": "3 Days",
                                "scheduleDeliveryDate": "2024-05-04"
                            },
                            "totalPrice": 5.29,
                            "totalBasePrice": 5.29,
                            "rates": [
                                {
                                    "description": "Parcel Select Nonmachinable DSCF SCF",
                                    "startDate": "2024-01-21",
                                    "endDate": "",
                                    "price": 5.29,
                                    "zone": "00",
                                    "weight": 1.0,
                                    "dimensionalWeight": 0.0,
                                    "fees": [],
                                    "SKU": "DVXP0XXFXC00010"
                                }
                            ],
                            "extraServices": [
                                {
                                    "name": "Global Direct Entry",
                                    "price": 0.00,
                                    "SKU": "NA"
                                }
                            ]
                        },
                        {
                            "commitment": {
                                "name": "3 Days",
                                "scheduleDeliveryDate": "2024-05-04"
                            },
                            "totalPrice": 4.17,
                            "totalBasePrice": 4.17,
                            "rates": [
                                {
                                    "description": "Parcel Select Nonmachinable DHUB Single-piece",
                                    "startDate": "2024-01-21",
                                    "endDate": "",
                                    "price": 4.17,
                                    "zone": "00",
                                    "weight": 1.0,
                                    "dimensionalWeight": 0.0,
                                    "fees": [],
                                    "SKU": "DVXP0XXBXC00010"
                                }
                            ],
                            "extraServices": [
                                {
                                    "name": "Global Direct Entry",
                                    "price": 0.00,
                                    "SKU": "NA"
                                }
                            ]
                        }
                    ]
                }
            ],
            "priceType": "COMMERCIAL",
            "paymentAccount": {
                "accountType": "EPS",
                "accountNumber": "XXXXXXXXXX"
            }
        }
    ]
}
```


## Tracking
This API returns tracking status and related details for a given USPS package, including scan events and their date, time, and location. The Tracking APIs allow you to integrate the status of your shipment into your customer-facing experience and internal fulfillment processes that will benefit your company by:
- Providing your customers with the latest status and delivery expectations while keeping them within your company’s or organization’s website. 
- Integrating status and delivery information for incoming packages or customer returns directly into your systems and processes so that you know when a package will arrive at your facilities. 

### Tracking - Single Request - Summary (V1)
Gets the tracking summary about a single USPS package. The USPS Tracking Request displays the delivery status on such mail items as Priority Mail, Priority Mail Express, and Package Services (Parcel Post, Bound Printed Matter, Library Mail, and Media Mail) packages with USPS Tracking. USPS Tracking collects mail class and service information on the mail piece from Product Tracking Systems and provides it to the user. USPS Tracking displays/Returns the summarized delivery status of single Priority Mail and Package Service Parcels with Delivery Confirmation by supplying the Tracking number.  
```sh
curl 	-X 'GET' 'https://api.usps.com/tracking/v1/tracking/{Tracking Number}?expand=summary' \
		--header 'Authorization: Bearer $TOKEN' \
		--data '' 
```

Response:

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
### Tracking - Single Request - Summary (V3)
Gets the tracking summary about a single USPS package. The USPS Tracking Request displays the delivery status on such mail items as Priority Mail, Priority Mail Express, and Package Services (Parcel Post, Bound Printed Matter, Library Mail, and Media Mail) packages with USPS Tracking. USPS Tracking collects mail class and service information on the mail piece from Product Tracking Systems and provides it to the user. USPS Tracking displays/Returns the summarized delivery status of single Priority Mail and Package Service Parcels with Delivery Confirmation by supplying the Tracking number.  
```sh
curl 	-X 'GET' 'https://api.usps.com/tracking/v3/tracking/{Tracking Number}?expand=summary' \
		--header 'Authorization: Bearer $TOKEN' \
		--data '' 
```

Response:

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
### Tracking - Single Request - Detail (V1)
Gets the detailed tracking information about a single USPS package. The USPS Tracking Request displays the delivery status on such mail items as Priority Mail, Priority Mail Express, and Package Services (Parcel Post, Bound Printed Matter, Library Mail, and Media Mail) packages with USPS Tracking. USPS Tracking collects mail class and service information on the mail piece from Product Tracking Systems and provides it to the user. USPS Tracking displays/Returns the detailed delivery status of single Priority Mail and Package Service Parcels with Delivery Confirmation by supplying the Tracking number.
```sh
curl 	-X 'GET' 'https://api.usps.com/tracking/v1/tracking/{Tracking Number}?expand=detail' \
		--header 'Authorization: Bearer $TOKEN' \
		--data ''
```
Response:
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
### Tracking - Single Request - Detail (V3)
Gets the detailed tracking information about a single USPS package. The USPS Tracking Request displays the delivery status on such mail items as Priority Mail, Priority Mail Express, and Package Services (Parcel Post, Bound Printed Matter, Library Mail, and Media Mail) packages with USPS Tracking. USPS Tracking collects mail class and service information on the mail piece from Product Tracking Systems and provides it to the user. USPS Tracking displays/Returns the detailed delivery status of single Priority Mail and Package Service Parcels with Delivery Confirmation by supplying the Tracking number.
```sh
curl 	-X 'GET' 'https://api.usps.com/tracking/v3/tracking/{Tracking Number}?expand=detail' \
		--header 'Authorization: Bearer $TOKEN' \
		--data ''
```
Response:
```json
{
    "destinationCity": "CEDAR RAPIDS",
    "destinationState": "IA",
    "destinationZIP": "52404",
    "emailEnabled": "true",
    "kahalaIndicator": "false",
    "mailClass": "Priority Mail<SUP>&reg;</SUP>",
    "mailType": "DM",
    "originCity": "RICHMOND",
    "originState": "VA",
    "originZIP": "23227",
    "proofOfDeliveryEnabled": "false",
    "restoreEnabled": "false",
    "RRAMEnabled": "false",
    "RREEnabled": "false",
    "services": "USPS Tracking<SUP>&#174;</SUP>",
    "serviceTypeCode": "14",
    "status": "USPS in possession of item",
    "statusCategory": "Accepted",
    "statusSummary": "USPS is now in possession of your item as of 7:31 am on August 2, 2023 in RICHMOND, VA 23227.",
    "trackingEvents": {
        "eventType": "USPS in possession of item",
        "eventTimestamp": "2023-08-02T07:31:00Z",
        "eventCountry": null,
        "eventCity": "RICHMOND",
        "eventState": "VA",
        "eventZIP": "23227",
        "firm": null,
        "name": null,
        "authorizedAgent": "false",
        "eventCode": "03",
        "additionalProp": null
    },
    "trackingNumber": "XXXXXXXXXXXXXXXXXXXX"
}
```
### Tracking - Email Notification Request (V1)
Register to get e-mail notifications about package tracking events. The USPS Tracking by Email API allows the customer to submit their email address to be notified of current or future tracking activity. USPS Tracking by Email allows users to receive all activity to date, all future activity, or both. As mail pieces receive email notifiable events, an email with the information will be generated and sent to the customer. Along with the new request type, users will be able to make multiple requests for one mail piece in a single request. Tracking status can be obtained using just the impb and if multiple items are found, you can include additional detail in the request such as:
- uniqueTrackingID (MP-Suffix) - The impb may be reused during the USPS retention period. This field uniquely identifies the shipment status of a unique mailpiece when there exists multiple occurrences of the same impb during this retention period. MPSUFFIX value is located in Track/Confirm Fields API response data. Unique to each TrackID.
OR 
- approximateIntakeDate (MP-Date) - When the mailpiece was taken into USPS for shipment. The impb may be reused during the USPS retention period. This field helps to uniquely identify the shipment status of a unique mailpiece when there exists multiple occurrences of the same impb during this retention period. MPDATE value is located in Track/Confirm Fields API response data. Unique to each TrackId.

- One email address is required and user can enter up to three for notifications.
- RequestType at least one is needed and up to 7 within this list [ AL, FD, ED, TD, UP, FS, OA]
```sh
curl 	-X 'POST' 'https://api.usps.com/tracking/v1/tracking/{Tracking Number}/notifications' \
		--header 'Content-Type: application/json' \
		--header 'Accept: application/json' \
		--header 'Authorization: Bearer $TOKEN' \
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

Response:
```json
{
    "NotificationAcknowledgment": {
        "ReturnCode": "2"
    }
}
```


## Errors
Example error handling scenarios pertaining to the Address and Domestic Label APIs:

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
     
Insufficient-Address-Data:
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

Example postman collection file: [Example postman collection.json](https://github.com/USPS/api-examples/blob/main/Example-Postman.postman_collection.json)
