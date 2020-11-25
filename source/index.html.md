---
title: KumoScale Provisioner API

language_tabs: # must be one of https://git.io/vQNgJ
  - shell: cURL

<!-- toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/slatedocs/slate'>Documentation Powered by Slate</a> -->

includes:
  - errors

search: true

---

# Introduction

This document specifies the detailed requests/responses JSONs structures of the KumoScale's REST APIs.
Notes! – 
GET responses might have additional fields, which are not described in this document. Those fields are usually for internal usage and should be ignored if not mentioned in the API description.
Unrecognized JSON fields in POST requests will be ignored by the KumoScale engine.

# Inventory Management 

The inventory management APIs provide the means for maintaining the pool of KumoScale storage nodes, available for volume allocation.
This includes monitoring the pools its health, maintaining its coherency and persistency, and extracting the pool’s information.
Creating and modifying the pool is done via operators.


## List Backends
> Request

```shell

curl -k --cert ./ssdtoolbox.pem https://10.93.66.121:8090/backends
```


> Response

```json
[
  {
    "persistentID": "0c:c4:7a:66:96:83",
      "mgmtIPs": [
        "172.28.116.13"
      ],
      "rack": "Rack1",
      "zone": "Zone2",
      "region": "Region2",
      "inUse": true,
      "state": "Available",
      "totalCapacity": 8001574060032,
      "availableCapacity": 8001431797760,
      "lastProbTime": 1576066112110,
      "probeInterval": 60,
      "totalBW": 0,
      "availableBW": 0,
      "totalIOPS": 0,
       "availableIOPS": 0,
      "portals":[{
        "ip":"10.10.10.1",
        "port":4420,
        "transport":"RoCEv2"
      }],
    "hostId": "5836c3cc-1c7e-4f2e-9698-e39175a41bd7" 
  }
]

```

### Request

No URI parameters.


### Response

| Param | Type | Description |
| ---- | ----- | ---- | 
| persistentID | String | KS persistent ID (~ MAC address) |
| mgmtIPs | List<String> | KS Management IP or DNS name |
| rack | String | KS location Rack ID   |
| zone | String | Accessible Zone ID  (e.g., shared switch)   |
| region | String | Accessible Region ID  (e.g., shared cage)   |
| inUse | Boolean | Is it used for volume management   |
| state | Enum | Available / Unavailable |
| totalCapacity | Long | KS current total capacity -  The sum of all SSDs capacity |
| availableCapacity | Long | KS sum of all available space    |
| lastProbTime | Long | Last probe time    |
| probeInterval | Int | Probe interval in secs   |
| totalBW | Long | KS total potential bandwidth in B/s   |
| availableBW | Long | KS available bandwidth in B/s (unused out of potential value) |
| totalIOPS | Long | KS total potential IOPS    |
| availableIOPS | Long | KS available IOPS (unused out of potential value)    |
| portals | List<String> | A list of portals. Each entry includes the following parameters: ip – the ip address of the portal, port – the port of the portal, transport – the transport type of the portal   |
| hostId | String | The host id   |


## Get Backend by ID

Retrieve a KumoScale backend and its details from the Provisioner’s pool according to its ID

>Request


```shell
curl -k --cert ./ssdtoolbox.pem https://10.93.66.121:8090/backends/0c:c4:7a:66:96:83
```

>Response

```json
{
  "persistentID": "0c:c4:7a:66:96:83",
    "mgmtIPs": [
      "172.28.116.13"
    ],
    "rack": "Rack1",
    "zone": "Zone2",
    "region": "Region2",
    "inUse": true,
    "state": "Available",
    "totalCapacity": 8001574060032,
    "availableCapacity": 8001431797760,
    "lastProbTime": 1576066112110,
    "probeInterval": 60,
    "totalBW": 0,
    "availableBW": 0,
    "totalIOPS": 0,
    "availableIOPS": 0,
    "portals":[{
      "ip":"10.10.10.1",
      "port":4420,
      "transport":"RoCEv2"
    }],
  "hostId": "5836c3cc-1c7e-4f2e-9698-e39175a41bd7" 
 }

```


### Request

URI parameters.

| Param | Type | Is Mandatory | Description |
| ---- | ----- | ---- | ---- |
| persistentID | String | Mandatory | The persistent ID of the backend |

### Response

| Param | Type | Description |
| ---- | ----- | ---- | 
| persistentID | String | KS persistent ID (~ MAC address) |
| mgmtIPs | List<String> | KS Management IP or DNS name |
| rack | String | KS location Rack ID   |
| zone | String | Accessible Zone ID  (e.g., shared switch)   |
| region | String | Accessible Region ID  (e.g., shared cage)   |
| inUse | Boolean | Is it used for volume management   |
| state | Enum | Available / Unavailable |
| totalCapacity | Long | KS current total capacity -  The sum of all SSDs capacity |
| availableCapacity | Long | KS sum of all available space    |
| lastProbTime | Long | Last probe time    |
| probeInterval | Int | Probe interval in secs   |
| totalBW | Long | KS total potential bandwidth in B/s   |
| availableBW | Long | KS available bandwidth in B/s (unused out of potential value) |
| totalIOPS | Long | KS total potential IOPS    |
| availableIOPS | Long | KS available IOPS (unused out of potential value)    |
| portals | List<String> | A list of portals. Each entry includes the following parameters: ip – the ip address of the portal, port – the port of the portal, transport – the transport type of the portal   |
| hostId | String | The host id   |



# Multi-Tenancy Management

> Request

```shell

curl -XPOST
-H 'Authorization: Bearer TOKEN'
-H "Content-type: application/json"
-d '{
  "nickname": "john_doe",
  "reference_id": "123456789"
}'
'https://api.autharmor.com/v1/invite/request'

```



Before you can send auth requests, you must invite users to your project. You can use the invite API to request an invite, then send to your users. Auth Armor supports two ways users can be invited. Either methods first needs a call to the Auth Armor invite API

| Param | Required | Description |
| ---- | ----- | ---- | 
| nickname | Yes |  This is a friendly display name for your user. It would be best to set the nickname to the username value or something similar so you can relate to your system. If your usernames are email addresses, consider masking the email address before using as a nickname to avoid personal information being stored in Auth Armor. You can search history using this ID |
| reference_id | No | This is an optional value that you can set to further cross reference your records. For example, you could set this value to your unique value in your system such as a UUID, a row ID, or some other value. You can search history using this ID. |
| reset_and_reinvite | No | Default is false. If you need to reset the current user and reinvite, set this flag to true. This will remove all mobile devices and/or active pending invites and create a new invite. Use caution when using this - it will reset any existing user with this nickname |

## QR code


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



> Example

> `https://invite.autharmor.com/?i=aacc137b-e13e-4f35-9d3b-402d530d954c&aa_sig=6036145BEB43D02F61F5D41CE43F8BA5858ECBC2ED4AA7F35C8176BE37B92E68`

In this method, the user can either click a link and follow invite instructions, or by using our client SDK, you can open a small pop-up window that will drive the invite process.

Invite Link format:

`https://invite.autharmor.com/?i=INVITE_CODE>&aa_sig=AA_SIG`



## Response

> Response

```json
{
    "nickname": "sample test",
    "auth_profile_id": "706751cf-012d-4576-b639-654d0dfbdb5c",
    "invite_code": "43748ae...",
    "date_expires": "2020-07-11T17:18:56.3058075Z",
    "invite_type": "WebInviteAndOrQRCode",
    "aa_sig": "6433C0A...."
}
```

| Param | Description |
| ---- | ---- |
| nickname | Echoing back this value from the request. |
| auth_profile_id | This is the ID you will use to send an auth request. You must save this ID somewhere with your user records|
| invite_code | This is the unique invite code for this invite. |
| date_expires | This is when the invite will expire|
| invite_type | This is the invite type, right now, the only option is "TBD". |
| aa_sig | This is a special signature check value that helps prevent abuse. |


# Push Request

This is the API Endpoint to use for sending an auth request to your user(s).

```shell

curl -XPOST
-H 'Authorization: Bearer TOKEN'
-H "Content-type: application/json"
-d '{
  "nickname": "sample_user",
  "action_name": "Login",
  "short_msg": "Login requested detected from IP: 192.160.0.1",
  "nonce": "some unique value of your choice here"
  }'
'https://api.autharmor.com/v1/auth/request'

```



| Param | Required | Description |
| ---- | ----- | ---- | 
| nickname | Yes |  The nickname for the user you want to send this auth request to. |
| action_name | Yes | This is the action name that shows up in the push notification. This is the action the user is approving. Examples: “Login”, “Money Transfer”, “Account update”, etc. The min length is 2, and the max length is 25. |
| short_message | Yes |  This is the short message that will appear in the app under the action name. |
| nonce | No |  An optional value that is signed and returned in the request. This helps validate the response was from your request and no one has done a man-in-the-middle attack. |
| timeout_in_seconds | No | This is the amount of time you want to allow the auth to be valid before it expires. The min is 15, and max is 300. This is optional, and if not provided, will use the default time for the project. |
| accepted_auth_methods | No |  This is an optional field. If you do not send it, Auth Armor will allow any type of auth method, including biometric, security key or even pin fall back from non-biometric devices. |
| origin_location_data | No |  You can specify location data in the format of latitude and longitude. If provided, location details will show in the push message about the origin of the request. |

If you send `accepted_auth_methods` param, you must provide a rule to go along with it. The accepted auth methods currently supported are: `mobileDevice` and `securityKey`.

## Mobile Device

> Using Mobile Device

```shell

curl -XPOST
-H 'Authorization: Bearer TOKEN'
-H "Content-type: application/json"
-d '{
  "nickname": "sample_user",
  "action_name": "Login",
  "short_msg": "Login requested detected from IP: 192.160.0.1",
  "nonce": "some unique value of your choice here",
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
'https://api.autharmor.com/v1/auth/request'

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
  "nickname": "sample_user",
  "action_name": "Login",
  "short_msg": "Login requested detected from IP: 192.160.0.1",
  "nonce": "some unique value of your choice here",
  "timeout_in_seconds": "120",
  "accepted_auth_methods": [
      {
        "name": "securitykey"     
      }
    ]
  }'
'https://api.autharmor.com/v1/auth/request'

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
            "origin_location_data": {
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
            "secure_signed_message": {
                "signed_data": "eyJtZXNzYWdlX2RhdGEi.....",
                "signature_data_details": {
                    "hash_value": "20bc1578...",
                    "signature_data": "{\"signature_data\":{\"counter\":17,\"signature_value\":\"MEUCIH...=\",\"challenge\":\"20bc15....\"},\"metaData\":{},\"version\":1,\"format\":1,\"key_Id\":\"\"}",
                    "auth_method_usetype": "biometric",
                    "signing_method": "AuthArmor_ECDsa",
                    "auth_method": "MobileDevice",
                    "hash_method": "Sha256"
                },
                "signed_data_type": "AuthResponse",
                "signature_validation_details": {
                    "public_key": "MFkwEwYHKoZI....==",
                    "key_size": "256",
                    "key_type": "EC",
                    "signing_algorithm": "SHA256",
                    "curve_type": "secp256r1",
                    "key_format": "x.509"
                },
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
            "origin_location_data": {
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
            "secure_signed_message": {
                "signed_data": "eyJtZXNzYWdlX2RhdGEi.....",
                "signature_data_details": {
                    "hash_value": "20bc1578...",
                    "signature_data": "{\"keyHandle\":\"bIap1cuTOBhl7VDd4Z56rFPkFJL0ow927f1kWI0SC3_oHjuTFxm7OUcHRrfavcjudIAkyuHiIC_WzKPAYaJPdA\",\"clientData\":\"eyJ0eXAiOiJuYXZpZ2F0b3IuaWQuZ2V0QXNzZXJ0aW9uIiwiY2hhbGxlbmdlIjoiNzQyNjJhZDU4NTYzMjQxZjQ0ZjNjNzFmZjZiYjA3MWQ2NDUyNTc5YmQzZjRkZWI4M2UzZmZiYzU1YWU4OThjZSIsIm9yaWdpbiI6ImFuZHJvaWQ6YXBrLWtleS1oYXNoOjRqMFpQRFNEMnJSWmlDV0JqR051aFRBNDNCcmZyV0orYitRc2E2aGZkaGsifQ\",\"signatureData\":\"AQAAAIYwRQIgGcCfCahRWxI5NbFvLY_0Nsc0hgAj7I67i9d0f4F3mCoCIQC4oByT5AW2GaqWOjeHLCr9pszmmG9pLqFBQEO7l1arQw\"}",
                    "auth_method_usetype": "u2f_securitykey",
                    "signing_method": "AuthArmor_ECDsa",
                    "auth_method": "SecurityKey",
                    "hash_method": "Sha256"
                },
                "signed_data_type": "AuthResponse",
                "signature_validation_details": {
                    "public_key": "MFkwEwYHKoZI....==",
                    "key_size": "256",
                    "key_type": "EC",
                    "signing_algorithm": "SHA256",
                    "curve_type": "secp256r1",
                    "key_format": "x.509"
                },
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
| origin_location_data | If location data was provided in the request, it is echoed here. More details defined in the location data table. |
| accepted_auth_methods | The accepted auth methods for this request. If it was provided, these values are echoed back. If not provided, then all methods are echoed back with default rules. More details defined in the accepted auth methods table.|

<b><span style="color: black">Auth Profile Details</span></b>

| Param | Description |
| ---- | ---- | 
| auth_profile_id  | Echo back the ID in the request. |
| nickname | The nickname of the auth_profile record. |
| ref_id | The ref id of the auth profile record.|

<b><span style="color: black">Origin Location Data</span></b>

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
| secure_signed_message  | An object representing the signed object and cryptographic signature details. More details defined in the secure signed message table.|
| mobile_device_details | The details of the mobile device that wasused to facilitate the auth. Even if the auth method was security key, there was still a mobile device where the app was installed. More details defined in the mobile devices details table.|


<b><span style="color: black">Auth Method</span></b>

| Param | Description |
| ---- | ---- | 
| name | The name of the auth method used.|
| usetype  | The way the auth method was used. Options for mobile device are: biometric or pin. |

<b><span style="color: black">Secure Signed Message</span></b>

| Param | Description |
| ---- | ---- | 
| signed_data | This is a base64 payload of the data that was signed for the auth request. |
| signature_data_details | The data that is used to validate the signed data. More details defined in the signature data details table.|

<b><span style="color: black">Signature Data Details</span></b>

| Param | Description |
| ---- | ---- | 
| hash_value  | The hashed value of the signed_data base64 data. |
| signature_data | Dynamic JSON payload with signature data based on the auth method. More details below.|
| auth_method_usetype | The way the auth method was used. |
| auth_method | The auth method that was used to sign this data.|
| hash_method | The hashing method of the base64 signed data.|
| signing_method | How the message was signed.|

<b><span style="color: black">Signature Data (security key)</span></b>

| Param | Description |
| ---- | ---- | 
| keyHandle  | The U2F KeyHandle that was used. |
| clientData | The clientdata from the U2F security key.|
| signatureData | The signature from the U2F security key.|


<b><span style="color: black">Signature Data (mobile device)</span></b>

| Param | Description |
| ---- | ---- | 
| counter  | The counter value from the security key. |
| signature_value | The base64 encoded signature value from the security key. |
| challenge | The hash value from the base64 signed message. This is the value that is signed.|
| meta | Data about the signature - mostly reserved for future use. |
| version | The version of the signature data. |
| format | The format of the signature data. |
| key_id | The key id. Not used at this time |


<b><span style="color: black">Signature Validation Details</span></b>

| Param | Description |
| ---- | ---- | 
| public_key | The public key value used to validate the signature. |
| key_size | The size of the public/private key.|
| key_type | The type of key used. EC is the only key supported currently|
| signing_algorithm | The type of hashing algorithm used to sign – the only possible value right now is SHA256. |
| curve_type | The type of curve used if applicable. |
| key_format | The format of the key, the only value right now is x.509.|


<b><span style="color: black">Mobile Device Details</span></b>

| Param | Description |
| ---- | ---- | 
| platform | The mobile platform that was used (Android or iOS). |
| model | The model of the device.|
 


-----------------------------------------------------

## Bucketlists

> Using Bucketlists

```shell

curl -XPOST
-H 'Authorization: Bearer TOKEN'
-H "Content-type: application/json"
-d '{
  "name": "vacation",
  "done": "false",
  "cost": 1000,
  }'
'https://api.autharmor.com/v1/bucketlists'

```


| Param | Required | Type | Description |
| ---- | ----- | ---- |  ---- | 
| name | Yes |  string |  The name of the bucketlist. |
| done | No |   boolean | Is the bucketlist done. Defaults to false |
| cost | Yes |   integer | What's the cost of the bucketlist |

## Response

```json
{
    "id": 44,
    "name": "vacation",
    "items": [],
    "date_created": "2020-10-20  3:52:18",
    "date_modified": "2020-10-20  3:52:18",
    "created_by": "John Doe"
}
```

The response uses the logged user as the creator of the bucketlist
