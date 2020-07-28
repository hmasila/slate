---
title: AuthArmor API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell: cURL
  - ruby: Ruby
  - php: PHP

<!-- toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/slatedocs/slate'>Documentation Powered by Slate</a> -->

includes:
  - errors

search: true

---

# Introduction

Auth Armor turns smart mobile devices into modern secure authenticators preventing attacks and un-authorized access.

Passwords and shared secrets are outdated and insecure. Account take-overs, password theft and breaches cost billions each year and are getting worse.

Use Auth Armor to protect websites, apps, backoffice and more with our simple API. Get running in minutes.


# Authorization

To obtain an Access token, you must make a request to the Auth Armor OAuth2 server using the client_credentials OAuth2 grant.

> Request

```shell
curl -X POST 
-d "grant_type=client_credentials&client_id=YOUR_CLIENT_ID&client_secret=YOUR_CLIENT_SECRET"
https://login.autharmor.com/connect/token
```

```ruby
require "auth-armor"
AuthArmor::Client.new(client_id: "CLIENT_ID", client_secret: "CLIENT_SECRET")

```

```php 
<?

>
```


| Param | Required | Description |
| ---- | ----- | ---- | 
| client_id | Yes | Client ID from Auth Armor |
| client_secret | Yes | Client Secret from Auth Armor |
| scope | No | Permissions that the token will be allowed to perform |


> Response

```json
{
  "access_token":"eyJhbGciOiJSUzI1N...",
  "expires_in":600,
  "token_type":"Bearer",
  "scope":"aarmor.api.generate_invite_code aarmor.api.request_auth"
}
```

## API Access

Both the `client_id` and `client_secret` are obtained via the developer dashboard located at [https://dashboard.autharmor.com](https://dashboard.autharmor.com)

Login then select or create a project. Navigate to the Api Access section and create a new api client. when you create a client, you are asked to give the client permissions, or scopes; select one more more scopes here. Please see the scopes section for more info. After client creation, you will be presented with a `client_id` and `client_secret`. Copy these values into your request.

## Scopes

Scopes are permissions that the token will be allowed to perform. There are two scopes allowed. They are `aarmor.api.generate_invite_code` and `aarmor.api.request_auth`. When you create an api access client, you will be asked to select what scopes the client is allowed to perform. When requesting a token using that client, you can optionally leave the scope field blank. If left blank, all of the scopes that are assigned to the client will be applied to the token. If you specify the scopes in the token request, the token will then only have the scopes that were requested.

The access token returned will be used as a Bearer token for all the other requests.


# Push Request

This is the API Endpoint to use for sending an auth request to your user(s).

```shell

curl -XPOST
-H 'Authorization: Bearer TOKEN'
-H "Content-type: application/json"
-d '{
  "auth_profile_id": "706751cf-012d-4576-b639-654d0dfbdb5c",
  "action_name": "Login",
  "short_msg": "Login requested detected from IP: 192.160.0.1",
  "timeout_in_seconds": "120",
  "accepted_auth_methods": [
      {
        "name": "mobiledevice",      
        "rules": [
          {
            "name": "forcebiometric",
            "value": "true"
          }
        ]
      },
      {
        "name": "securitykey",
      }
    ]
  }'
'https://api.autharmor.com/auth/request'

```


```ruby
AuthArmor::Client.auth_request(
  auth_profile_id: "AUTH_PROFILE_ID",
  action_name: "Login",
  short_msg: "This is a test message",
)
```

| Param | Required | Description |
| ---- | ----- | ---- | 
| auth_profile_id | Yes |  This is the ID of the auth profile for your user. You get this value after you call the invite api, or you can get it from the dashboard after inviting the user. |
| action_name | Yes | This is the action you are sending an auth request for. Examples: “Login”, “Money Transfer”, “Account update”, etc. The min length is 2, and the max length is 25. |
| short_message | Yes |  This is the short message that will appear in the app under the action name. |
| timeout_in_seconds | No | This is the amount of time you want to allow the auth to be valid before it expires. The min is 15, and max is 300. This is optional, and if not provided, will use the default time for the project. |
| accepted_auth_methods | No |  This is an optional field. If you do not send it, Auth Armor will allow any type of auth method, including biometric, security key or even pin fall back from non-biometric devices. |

If you send `accepted_auth_methods` param, you must provide a rule to go along with it. The accepted auth methods currently supported are: `mobileDevice` and `securityKey`.

## Mobile Device

> Using Mobile Device

```shell

curl -XPOST
-H 'Authorization: Bearer TOKEN'
-H "Content-type: application/json"
-d '{
  "auth_profile_id": "706751cf-012d-4576-b639-654d0dfbdb5c",
  "action_name": "Login",
  "short_msg": "Login requested detected from IP: 192.160.0.1",
  "timeout_in_seconds": "120",
  "accepted_auth_methods": [
      {
        "name": "mobiledevice",      
        "rules": [
          {
            "name": "forcebiometric",
            "value": "true"
          }
        ]
      }
    ]
  }'
'https://api.autharmor.com/auth/request'

```

```ruby
AuthArmor::Client.auth_request(
  auth_profile_id: "AUTH_PROFILE_ID",
  action_name: "Login",
  short_msg: "This is a test message",
  accepted_auth_methods: "mobiledevice",
  forcebiometric: true
)
```

The current rules for mobileDevice are `forceBiometirc`. This can either be set to true or false.

If `forceBiometric` is set to true, a mobile device will be required and biometrics will be required. Pin fallback and non-biometric devices will not be compatible with this method. Only biometrics, such as fingerprint or faceID will be allowed. 

If `forcebiometric` is set to false, then a mobile device must be used, and a security key will not be allowed. Pin fallback and non-biometric devices using a pin will also be allowed

## Security Key

> Using Security Key

```shell

curl -XPOST
-H 'Authorization: Bearer TOKEN'
-H "Content-type: application/json"
-d '{
  "auth_profile_id": "706751cf-012d-4576-b639-654d0dfbdb5c",
  "action_name": "Login",
  "short_msg": "Login requested detected from IP: 192.160.0.1",
  "timeout_in_seconds": "120",
  "accepted_auth_methods": [
      {
        "name": "securitykey"     
      }
    ]
  }'
'https://api.autharmor.com/auth/request'

```

```ruby
AuthArmor::Client.auth_request(
  auth_profile_id: "AUTH_PROFILE_ID",
  action_name: "Login",
  short_msg: "This is a test message",
  accepted_auth_methods: "securitykey"
)
```

At this time, there are no rules for security keys.

If you set to security key, then mobile device will not be allowed, meaning no pin or biometrics are performed. Only a security key will be allowed. At this time, Auth Armor is using `U2F`. Full `FIOD2` with user presence is coming soon.

## Response



| Param | Description |
| ---- | ---- | 
| auth_history_id | This is the unique ID for the auth request.|
| response_code | The response code result – 0 = unknown, 2 = success, 3 = declined, 4 = possible fraud attempt, 5 = timeout. |
| response_message | The text representation of the result code.|
| authorized | Boolean – if the auth request is authenticated. |
| auth_details | Auth detail object, request and response details. More details defined in the table below. |

> Mobile Device Response

```json

{
    "auth_history_id": "36f0d...",
    "response_code": 2,
    "response_message": "Success",
    "authorized": true,
    "auth_details": {
        "request_details": {
            "date": "2020-07-11T17:09:44.7860965Z",
            "auth_profile_details": {
                "auth_profile_id": "706751cf-012d-4576-b639-654d0dfbdb5c",
                "nickname": "john_doe",
                "ref_id": "123456789"
            },
            "location_data": {
                "latitude": null,
                "longitude": null
            },
            "accepted_auth_methods": [
                {
                    "name": "MobileDevice",
                    "rules": [
                        {
                            "name": "ForceBiometric",
                            "value": false
                        }
                    ]
                }
            ]
        },
        "response_details": {
            "date": "2020-07-11T17:09:53.3862361Z",
            "auth_method": {
                "name": "MobileDevice",
                "usetype": "biometric"
            },
            "auth_validation_details": {
                "PublicKey": "MFkwEwYHKoZI....==",
                "KeySize": "256",
                "KeyType": "EC",
                "SigningAlgorithm": "SHA256",
                "CurveType": "secp256r1",
                "KeyFormat": "x.509"
            },
            "secure_signed_message": {
                "signed_data": "eyJtZXNzYWdlX2RhdGEi.....",
                "signature_data_details": {
                    "hash_value": "20bc1578...",
                    "signature_data": "{\"signature_data\":{\"counter\":17,\"signature_value\":\"MEUCIH...=\",\"challenge\":\"20bc15....\"},\"metaData\":{},\"version\":1,\"format\":1,\"key_Id\":\"\"}",
                    "auth_method_usetype": "biometric",
                    "auth_method": "MobileDevice",
                    "hash_method": "Sha256"
                }
            },
            "mobile_device_details": {
                "platform": "Android",
                "model": "Samsung A50s"
            }
        }
    }
}

```

> Security Key Response

```json

{
    "auth_history_id": "36f0d...",
    "response_code": 2,
    "response_message": "Success",
    "authorized": true,
    "auth_details": {
        "request_details": {
            "date": "2020-07-11T17:09:44.7860965Z",
            "auth_profile_details": {
                "auth_profile_id": "706751cf-012d-4576-b639-654d0dfbdb5c",
                "nickname": "john_doe",
                "ref_id": "123456789"
            },
            "location_data": {
                "latitude": null,
                "longitude": null
            },
            "accepted_auth_methods": [
                {
                    "name": "SecurityKey",
                    "rules": []
                }
            ]
        },
        "response_details": {
            "date": "2020-07-11T17:09:53.3862361Z",
            "auth_method": {
                "name": "SecurityKey",
                "usetype": "u2f_securitykey"
            },
            "auth_validation_details": {
                "PublicKey": "MFkwEwYHKoZI....==",
                "KeySize": "256",
                "KeyType": "EC",
                "SigningAlgorithm": "SHA256",
                "CurveType": "secp256r1",
                "KeyFormat": "x.509"
            },
            "secure_signed_message": {
                "signed_data": "eyJtZXNzYWdlX2RhdGEi.....",
                "signature_data_details": {
                    "hash_value": "20bc1578...",
                    "signature_data": "{\"keyHandle\":\"bIap1cuTOBhl7VDd4Z56rFPkFJL0ow927f1kWI0SC3_oHjuTFxm7OUcHRrfavcjudIAkyuHiIC_WzKPAYaJPdA\",\"clientData\":\"eyJ0eXAiOiJuYXZpZ2F0b3IuaWQuZ2V0QXNzZXJ0aW9uIiwiY2hhbGxlbmdlIjoiNzQyNjJhZDU4NTYzMjQxZjQ0ZjNjNzFmZjZiYjA3MWQ2NDUyNTc5YmQzZjRkZWI4M2UzZmZiYzU1YWU4OThjZSIsIm9yaWdpbiI6ImFuZHJvaWQ6YXBrLWtleS1oYXNoOjRqMFpQRFNEMnJSWmlDV0JqR051aFRBNDNCcmZyV0orYitRc2E2aGZkaGsifQ\",\"signatureData\":\"AQAAAIYwRQIgGcCfCahRWxI5NbFvLY_0Nsc0hgAj7I67i9d0f4F3mCoCIQC4oByT5AW2GaqWOjeHLCr9pszmmG9pLqFBQEO7l1arQw\"}",
                    "auth_method_usetype": "u2f_securitykey",
                    "auth_method": "SecurityKey",
                    "hash_method": "Sha256"
                }
            },
            "mobile_device_details": {

            }
        }
    }
}

```

<b><span style="color: black">Auth Details</span></b>

| Param | Description |
| ---- | ---- | 
| request_details | Object for all the request details. More details defined in the request details table.|
| response_details | Object for all the response details. More details defined in the response details table.|

<b><span style="color: black">Request Details</span></b>

| Param | Description |
| ---- | ---- | 
| date | The date the auth request was made. |
| auth_profile_details | The details of the auth_profile that was targeted. More details defined in the auth profile details table.|
| location_data | If location data was provided in the request, it is echoed here. More details defined in the location data table. |
| accepted_auth_methods | The accepted auth methods for this request. If it was provided, these values are echoed back. If not provided, then all methods are echoed back with default rules. More details defined in the accepted auth methods table.|

<b><span style="color: black">Auth Profile Details</span></b>

| Param | Description |
| ---- | ---- | 
| auth_profile_id  | Echo back the ID in the request. |
| nickname | The nickname of the auth_profile record. |
| ref_id | The ref id of the auth profile record.|

<b><span style="color: black">Location Data</span></b>

| Param | Description |
| ---- | ---- | 
| latitude | Latitude location.|
| longitude | Longitude location. |

<b><span style="color: black">Accepted Auth Methods</span></b>

| Param | Description |
| ---- | ---- | 
| name | The accepted method name.|
| rules | The rules for the accepted auth method. |

<b><span style="color: black">Response Details</span></b>

| Param | Description |
| ---- | ---- | 
| date | The date the auth request was responded to. |
| auth_method | The auth method that was used for the response. If there was a timeout, or declined, this will not be populated. More details defined in the auth method table.|
| auth_validation_details | The details that can be used to validate the signature. More details defined in the auth validation details table.|
| secure_signed_message  | This is the secured signed message payload. More details defined in the secure signed message table.|
| mobile_device_details | The details of the mobile device that wasused to facilitate the auth. Even if the auth method was security key, there was still a mobile device where the app was installed. More details defined in the mobile devices details table.|


<b><span style="color: black">Auth Method</span></b>

| Param | Description |
| ---- | ---- | 
| name | The name of the auth method used.|
| usetype  | The way the auth method was used. Options for mobile device are: biometric or pin. |

<b><span style="color: black">Auth Validation Details</span></b>

| Param | Description |
| ---- | ---- | 
| public_key | The public key value used to validate the signature. |
| key_size | The size of the public/private key.|
| key_type | The type of key used. EC is the only key supported currently|
| signing_algorithm | The type of hashing algorithm used to sign – the only possible value right now is SHA256. |
| curve_type | The type of curve used if applicable. |
| key_format | The format of the key, the only value right now is x.509.|


<b><span style="color: black">Secure Signed Message</span></b>

| Param | Description |
| ---- | ---- | 
| signed_data | This is a base64 payload of the data that was signed for the auth request. |
| signature_data_details | The data that is used to validate the signed data. More details defined in the signature data details table.|

<b><span style="color: black">Signature Data Details</span></b>

| Param | Description |
| ---- | ---- | 
| hash_value  | The hash of the signed_data value. |
| signature_data | All the signature data relating to this auth. This is the u2f signature_data from the security key.|
| auth_method_usetype | The method used to sign the message. |
| auth_method | The auth method that was used to sign the message.|

<b><span style="color: black">Signature Data (security key)</span></b>

| Param | Description |
| ---- | ---- | 
| keyHandle  | The key handle. |
| clientData | The client data.|
| signatureData | The signature data.|


<b><span style="color: black">Signature Data (mobile device)</span></b>

| Param | Description |
| ---- | ---- | 
| counter  | The counter |
| signature_value | The signature value|
| challenge | The challenge. |
| meta | Metadata. |
| version | The version.|
| format | The version.|
| key_id | The key id. |

<b><span style="color: black">Mobile Device Details</span></b>

| Param | Description |
| ---- | ---- | 
| platform | The mobile platform that was used (Android or iOS). |
| model | The model of the device.|
 


# Send Invite

> Request

```shell

curl -XPOST
-H 'Authorization: Bearer TOKEN'
-H "Content-type: application/json"
-d '{
  "nickname": "john_doe",
  "reference_id": "123456789"
}'
'https://api.autharmor.com/invite/request'

```


```ruby
AuthArmor::Client.invite_request(
  nickname: "NICKNAME"
)
```

Before you can send auth requests, you must invite users to your project. You can use the invite API to request an invite, then send to your users. Auth Armor supports two ways users can be invited. Either methods first needs a call to the Auth Armor invite API

| Param | Required | Description |
| ---- | ----- | ---- | 
| nickname | Yes |  This is a friendly display name for your user. It would be best to set the nickname to the username value or something similar so you can relate to your system. If your usernames are email addresses, consider masking the email address before using as a nickname to avoid personal information being stored in Auth Armor. You can search history using this ID |
| reference_id | No | This is an optional value that you can set to further cross reference your records. For example, you could set this value to your unique value in your system such as a UUID, a row ID, or some other value. You can search history using this ID. |

## QR code

```ruby
AuthArmor::Client.generate_qr_code

```

> Example

To use the QR code format, please format a json payload like below:

``` json
{
  "type":"profile_invite",
  "version":1,
  "format_id":1,
  "payload": {
    "aa_sig": "AA_SIG",
    "invite_code":"INVITE_CODE"
  }
}
```

A QR code can be generated and then scanned, much like many of the other authenticators out there. In the app, a user simply needs to scan the QR code to get setup for your project.



Then generate a QR code using this payload text. The QR code can then be scannedin the app

## Invite link

```ruby
AuthArmor::Client.get_invite_link
```

> Example

> `https://invite.autharmor.com/?i=aacc137b-e13e-4f35-9d3b-402d530d954c&aa_sig=6036145BEB43D02F61F5D41CE43F8BA5858ECBC2ED4AA7F35C8176BE37B92E68`

In this method, the user can either click a link and follow invite instructions, or by using our client SDK, you can open a small pop-up window that will drive the invite process.

Invite Link format:

`https://invite.autharmor.com/?i=INVITE_CODE>&aa_sig=AA_SIG`



## Response

> Response

```json
{
    "auth_profile_id": "706751cf-012d-4576-b639-654d0dfbdb5c",
    "invite_code": "43748ae...",
    "date_expires": "2020-07-11T17:18:56.3058075Z",
    "invite_type": "WebInviteAndOrQRCode",
    "aa_sig": "6433C0A...."
}
```

| Param | Description |
| ---- | ---- | 
| auth_profile_id | This is the ID you will use to send an auth request. You must save this ID somewhere with your user records|
| invite_code | This is the unique invite code for this invite. |
| date_expires | This is when the invite will expire|
| invite_type | This is the invite type, right now, the only option is "TBD". |
| aa_sig | This is a special signature check value that helps prevent abuse. |


