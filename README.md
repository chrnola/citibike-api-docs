# Citi Bike (Motivate) API Documentation

An overview of the API endpoints required to unlock a Citi Bike. The information contained here is for research purposes only and was obtained by inspecting the traffic from a running instance of the official [Citi Bike Android app](https://play.google.com/store/apps/details?id=com.citibikenyc.citibike&hl=en_US) (v12.20.0).

## Notes

* Transport: the Android app uses HTTP/2.0, but the API seems to accept HTTP/1.1 as well.
* Multi-session: the API enforces a one-device-at-a-time policy that will invalidate a user's session on all other devices

## Authentication (Login)

Generates user access tokens (see `memberAccessToken.access_token` in the response body) which can be used to access more privileged endpoints. It is unclear how long these tokens are valid for.

### Request

* Method: `POST`
* Endpoint: `https://layer.bicyclesharing.net/mobile/v1/nyc/login`
* Headers:
    * accept: `application/json`
    * accept-language: `en`
    * content-type: `application/json; charset=UTF-8`
    * content-length: ...
    * accept-encoding: `gzip`
    * user-agent: `citibike/12.20.0(322) Dalvik/2.1.0 (Linux; U; Android 7.1.1; ONEPLUS A3010 Build/NMF26F)`
    * x-mtvl-telemetry: `app_version:12.20.0,is_location_services_enabled:false,is_push_notifications_enabled:true,is_bike_lanes_layer_enabled:true,is_bike_angels_layer_enabled:false`
* Payload:
    ```json
    {
        "deviceId": "...",
        "password": "...",
        "username": "..."
    }
    ```
    * `deviceId`
        * 16 charater hexadecimal string, lowercase
        * [Android Device ID](https://developer.android.com/reference/android/provider/Settings.Secure#ANDROID_ID)
    * `password`
        * account password, verbatim
    * `username`
        * email address associated with the account, verbatim

### Response

* Headers
    * date
    * content-type: `application/json`
    * x-system: `nyc`
* Status Code: 200
* Payload:
    ```json
    {
        "accountStatus": "o",
        "firebaseToken": "<token>",
        "member": {
            "additionalFields": [
                {
                    "code": "profile_image_last_update",
                    "value": "<epoch time>"
                },
                {
                    "code": "bike-angels",
                    "value": "true"
                }
            ],
            "allowedAccessMethods": [
                "CREDIT_CARD",
                "BIKE_KEY",
                "MOBILE"
            ],
            "birthday": {
                "day": 1,
                "month": 1,
                "year": 1900
            },
            "canActivateBikeKey": false,
            "canAssignBikeKey": true,
            "canPurchaseSubscription": false,
            "currentSubscription": {
                "corporateEmailConfirmed": "n",
                "endDateMs": 000000000,
                "externalId": "<UUID>",
                "id": "int32? identifer",
                "numberOfConcurrentBikes": 1,
                "status": "a",
                "type": {
                    "activationStrategy": "m",
                    "additionalFields": [
                        {
                            "code": "polaris.title.locale.es",
                            "value": "Localization text"
                        }
                    ],
                    "configuration": {
                        "paymentStrategy": "o"
                    },
                    "description": "Join Citi Bike to unlock thousands of bikes across Manhattan, Brooklyn, Queens & Jersey City, and enjoy unlimited 45-minute rides. Bike share is designed for quick, convenient trips – it’s faster than walking, cheaper than a taxi, and more fun than the subway. Customers using an eligible Citi® credit or Citibank® debit card at checkout receive 10% off the membership price.",
                    "expirationNotificationDelayMs": 0000000,
                    "externalId": "<UUID>",
                    "freeRentalPeriodDurationMs": 000000,
                    "id": "68",
                    "minimumDelayBetweenRentalsMs": 0,
                    "name": "Annual Membership from Citi Bike App"
                }
            },
            "email": "<user's email>",
            "firstName": "<user's first name>",
            "gender": "m",
            "hasAcceptedTermsAndConditions": true,
            "hasPassword": true,
            "id": "10-char identifer",
            "language": "en",
            "lastName": "<user's last name>",
            "memberSinceMs": 0000000,
            "needsFirstKey": true,
            "notificationConfiguration": {
                "bikeReturnEmail": false,
                "overageFeesEmail": false
            },
            "partial": false,
            "phoneNumber": "<user's 10-digit phone number>",
            "rideInsightsEnabled": true,
            "subscriptionRenewDate": 00000000,
            "subscriptionRenewalConfiguration": {
                "canToggleRenewal": true,
                "enabled": true,
                "renewal": "s",
                "subscriptionType": {
                    "activationStrategy": "m",
                    "configuration": {
                        "description": "Join Citi Bike to unlock thousands of bikes across Manhattan, Brooklyn, Queens & Jersey City, and enjoy unlimited 45-minute rides. Bike share is designed for quick, convenient trips – it’s faster than walking, cheaper than a taxi, and more fun than the subway. Customers using an eligible Citi® credit or Citibank® debit card at checkout receive 10% off the membership price.",
                        "paymentStrategy": "o",
                        "price": 16900
                    },
                    "description": "Join Citi Bike to unlock thousands of bikes across Manhattan, Brooklyn, Queens & Jersey City, and enjoy unlimited 45-minute rides. Bike share is designed for quick, convenient trips – it’s faster than walking, cheaper than a taxi, and more fun than the subway. Customers using an eligible Citi® credit or Citibank® debit card at checkout receive 10% off the membership price.",
                    "externalId": "<UUID>",
                    "id": "68",
                    "minimumDelayBetweenRentalsMs": 0,
                    "name": "Annual Membership from Citi Bike App"
                }
            },
            "termsAndConditionsAcceptanceDateMs": 000000000,
            "type": "h"
        },
        "memberAccessToken": {
            "access_token": "<UUID>"
        },
        "memberType": "h"
    }
    ```

## Authentication (Token Validation)

Validates a user access token.

### Request

* Method: `POST`
* Endpoint: `https://layer.bicyclesharing.net/mobile/v2/nyc/check-login`
* Headers
    * authorization: the value of `memberAccessToken.access_token` from the `/login` response
    * accept: `application/json`
    * accept-language: `en`
    * content-type: `application/json; charset=UTF-8`
    * content-length: ...
    * accept-encoding: `gzip`
    * user-agent: `citibike/12.20.0(322) Dalvik/2.1.0 (Linux; U; Android 7.1.1; ONEPLUS A3010 Build/NMF26F)`
    * x-mtvl-telemetry: `app_version:12.20.0,is_location_services_enabled:false,is_push_notifications_enabled:true,is_bike_lanes_layer_enabled:true,is_bike_angels_layer_enabled:false`
* Payload
    ```json
    {
        "deviceId": "...",
        "memberId": "..."
    }
    ```
    * `deviceId`
        * 16 charater hexadecimal string, lowercase
        * must be the same identifier originally sent to the `/login` endpoint
    * `memberId`
        * the value of `member.id` from the `/login` response

### Response

* Status Code: `200`, if the user's token is still valid for the given device & member tuple
* Payload
    ```json
    ""
    ```

## Favorite Stations

Retrieves a list of identifiers for the authenticated user's favorite docking stations.

### Request

* Method: `GET`
* Endpoint: `https://layer.bicyclesharing.net/v2/nyc/favoritestation`
* Headers:
    * api-key: base-64 encoded Motivate API key (e.g. `base64("name:secret")`)
    * authorization: the value of `memberAccessToken.access_token` from the `/login` response
    * user-agent: `citibike/12.20.0(322) Dalvik/2.1.0 (Linux; U; Android 7.1.1; ONEPLUS A3010 Build/NMF26F)`
    * accept: `application/json`
    * content-type: `application/json`
    * accept-language: `en`
    * accept-encoding: `gzip`

### Response

* Status Code: `200`
* Headers
    * date
    * content-type: `application/json`
    * content-length: ...
    * x-system: `nyc`
* Payload
    ```json
    {
        "ids": [
            "123",
            ...
        ]
    }
    ```

## All Stations

Retrieves a list of all docking stations in the system. Does not require authentication.

### Request

* Method: `GET`
* Endpoint: `https://layer.bicyclesharing.net/map/v1/nyc/map-inventory`
* Headers:
    * user-agent: `citibike/12.20.0(322) Dalvik/2.1.0 (Linux; U; Android 7.1.1; ONEPLUS A3010 Build/NMF26F)`
    * accept: `application/json`
    * content-type: `application/json`
    * accept-language: `en`
    * accept-encoding: `gzip`

### Response

* Status Code: `200`
* Headers
    * date
    * content-type: `application/json`
    * content-length: ...
    * expires: `Mon, 18 May 2020 01:44:33 GMT`
    * etag: `"600762c973ff04baadbed1d69526cc8af6dead17"`
* Payload
    ```json
    {
        "features": [
            {
                "geometry": {
                    "coordinates": [
                        -73.99392888,
                        40.76727216
                    ],
                    "type": "Point"
                },
                "properties": {
                    "bike_angels": {
                        "score": 0
                    },
                    "bike_angels_action": "neutral",
                    "bike_angels_digits": 0,
                    "bike_angels_points": 0,
                    "bikes": [],
                    "icon_dot_bike_layer": "dot-green",
                    "icon_dot_dock_layer": "dot-green",
                    "icon_pin_bike_layer": "pin-bike-green-most",
                    "icon_pin_dock_layer": "pin-dock-green-half",
                    "station": {
                        "accepts_dockable_bikes": true,
                        "accepts_lockable_bikes": false,
                        "bikes_available": 31,
                        "bikes_disabled": 2,
                        "capacity": 55,
                        "docks_available": 22,
                        "docks_disabled": 0,
                        "ebike_surcharge_waiver": false,
                        "id": "72",
                        "id_public": "66db237e-0aca-11e7-82f6-3863bb44ef7c",
                        "installed": true,
                        "last_reported": 1589765744,
                        "name": "W 52 St & 11 Ave",
                        "renting": true,
                        "returning": true,
                        "terminal": "6926.01"
                    }
                },
                "type": "Feature"
            },
            ...
        ]
    }
    ```
    * `features[*].geometry.coordinates`: [longitude, latitude]
    * `properties.station.id`: identifier of the station (correlates with responses from `/favoritestation`)

## Unlock Bike

### Request

* Method: `POST`
* Endpoint: `https://layer.bicyclesharing.net/v1/nyc/rent`
* Headers:
    * api-key: base-64 encoded Motivate API key (e.g. `base64("name:secret")`)
    * authorization: the value of `memberAccessToken.access_token` from the `/login` response
    * user-agent: `citibike/12.20.0(322) Dalvik/2.1.0 (Linux; U; Android 7.1.1; ONEPLUS A3010 Build/NMF26F)`
    * accept: `application/json`
    * accept-language: `en`
    * content-type: `application/json; charset=UTF-8`
    * accept-encoding: `gzip`
* Payload
    ```json
    {
        "stationId": 1234,
        "subscriptions": [
            {
                "id": "...",
                "numberOfBikes": 1
            }
        ]
    }
    ```
    * `subscriptions.id` must match the value of `member.currentSubscription.id` from the `/login` response

### Response

* Status Code: `200`
* Headers
    * date
    * content-type: `application/json`
    * content-length: ...
* Payload
    ```json
    {
        "rentals": [
            {
                "rentalPin": {
                    "eligibleBikeCount": 1,
                    "pin": "11111",
                    "pinDurationMs": 30000
                },
                "result": "a",
                "subscription": {
                    "id": "..."
                }
            }
        ]
    }
    ```
    * `rentalPin.pin` is the 5-digit code that can be keyed into a station to unlock a bike
    * The `rentalPin` object is packed into an array of `rentals` seemingly to support the "Group Ride" feature which permits users to request unlock codes for guest riders.
