---
title: AuthArmor API Reference

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

Welcome to the Auth Armor API! 


To obtain an Access token, you must make a request to the Auth Armor OAuth2 server using the client_credentials OAuth2 grant.


| Param | Required | Description |
| ---- | ----- | ---- | 
| client_id | Yes | Client ID from Auth Armor |
| client_secret | Yes | Client Secret from Auth Armor |
| scope | No | Permissions that the token will be allowed to perform |


## API Access

Both the `client_id` and `client_secret` are obtained via the developer dashboard located at [https://dashboard.autharmor.com](https://dashboard.autharmor.com)

Login then select or create a project. Navigate to the Api Access section and create a new api client. when you create a client, you are asked to give the client permissions, or scopes; select one more more scopes here. Please see the scopes section for more info. After client creation, you will be presented with a `client_id` and `client_secret`. Copy these values into your request.

## Scopes

Scopes are permissions that the token will be allowed to perform. There are two scopes allowed. They are `aarmor.api.generate_invite_code` and `aarmor.api.request_auth`. When you create an api access client, you will be asked to select what scopes the client is allowed to perform. When requesting a token using that client, you can optionally leave the scope field blank. If left blank, all of the scopes that are assigned to the client will be applied to the token. If you specify the scopes in the token request, the token will then only have the scopes that were requested.

> Request

```shell
curl -X POST 
-d "grant_type=client_credentials&client_id=YOUR_CLIENT_ID&client_secret=YOUR_CLIENT_SECRET"
https://login.autharmor.com/connect/token
```

The access token returned will be used as a Bearer token for all the other requests.

> Response

```json
{
  "access_token":"eyJhbGciOiJSUzI1N...",
  "expires_in":600,
  "token_type":"Bearer",
  "scope":"aarmor.api.generate_invite_code aarmor.api.request_auth"
}
```

# Push Request

> Request

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
        "name": "securitykey"     
      }
    ]
  }'
'https://api.autharmor.com/auth/request'

```

> Response

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
                },
                {
                    "name": "SecurityKey",
                    "rules": []
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
                "signature_data": {
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

# QR Invite

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


| Param | Required | Description |
| ---- | ----- | ---- | 
| nickname | Yes | A nickname for your user to easily identify |
| reference_id | No | An Optional value you can set to refer to later, such as an internal database id. |



