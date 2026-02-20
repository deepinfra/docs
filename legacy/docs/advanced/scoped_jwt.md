---
title: Scoped JWT authentication
description: How to use JWT authentication for scope-limited inference access
---

##  Contents

* [Overview](#overview)
* [Format](#format)
    * [Header](#header)
    * [Payload](#payload)
    * [Signature](#signature)
    * [Token](#token)
* [Usage](#usage)

## Overview

Scoped JWT authentication allows you to create scope-limited tokens for accessing DeepInfra inference API endpoints.

For example, you can issue a scoped JWT and give it to a third party that you provide services to. That third party can now directly do inference using the JWT, but limited to your specification. You don't need to share your API key with that party or to proxy their requests.

Scoped JWT tokens are associated with an API key, and they let you specify expiration, allowed models and spending limit.

Inference usage done with a scoped JWT will be counted towards the API key that was used for signing that token.

## Simple Usage

You can create JWT tokens with a POST to /v1/scoped-jwt:

```bash
curl -X POST "https://api.deepinfra.com/v1/scoped-jwt" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $DEEPINFRA_API_KEY" \
  -d '{
      "api_key_name": "auto",
      "models": ["deepseek-ai/DeepSeek-R1"],
      "expires_delta": 3600,
      "spending_limit": 1.0
  }'

{"token":"jwt:eyJhbGciOiJIUzI1NiIsImtpZCxxxxxxxxxxxxxxxxxx"}
```

This creates a JWT token associated with api key `auto`, limited to deepseek-r1, expiring in 1 hour, with spending limit 1.00 USD.

You can skip `models` (allow any model), `expires_delta` (no expiration -- ATM
that means 1 year) and `spending_limit` (no spending limit). Also you can
provide `expires_at` (unix TS) instead of `expires_delta`.

You can also check (decode) the JWT token via GET to /v1/scoped-jwt (make sure the token used is the same as the encoding token).

```bash
curl "https://api.deepinfra.com/v1/scoped-jwt?jwtoken=XXXX" \
    -H "Authorization: Bearer $DEEPINFRA_API_KEY"

{
  "expires_at": 1738843515,
  "models": [
    "deepseek-ai/DeepSeek-R1"
  ],
  "spending_limit": 1
}
```

## Usage

Once issued, the scoped JWT can be used in all inference endpoints in place of an API key, but only if the restrictions are met (models allowed, before expiration date, before money limit is exhausted).

```bash
curl "https://api.deepinfra.com/v1/openai/chat/completions" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $SCOPED_JWT" \
  -d '{
      "model": "deepseek-ai/DeepSeek-R1",
      "messages": [
        {
          "role": "user",
          "content": "Hello!"
        }
      ]
    }'
```

## Format

You can also create and inspect Scoped JWT tokens yourself, here is a detailed
explanation on how they are formed. The generral idea is that the payload
encodes the restrictions and the signature is based on the API key used.

### Header

For the standard `alg` field we only accept `HS256` value (HMAC-SHA256). This is the algorithm you should use to produce the signature.

The `kid` field stores your key id. It is formed from your DeepInfra id and the Base64 encoding of the name of the API key you use for signing. These two parts are concatenate with a colon separator.

In the example bellow we specify a user with id `di:1000000000000` with an API key named `auto`, which when Base64 encoded becomes `YXV0bw==`. Then we concatenate the two with a colon to get the key id `di:1000000000000:YXV0bw==`.

```json
{
    "alg": "HS256",
    "kid": "di:1000000000000:YXV0bw==",
    "typ": "JWT"
}
```

### Payload

The `sub` field specifies again your user id. The `model` field specifies which model the token is will be allows to access. The `exp` specifies an expiration UTC timestamp in seconds, that can point to no later than week from the moment of issuing the token.

```json
{
    "sub": "di:1000000000000",
    "model": "deepseek-ai/DeepSeek-R1",
    "exp": 1734616903
}
```

### Signature

Employ the standard way of calculating the JWT signature, using your chosen API key as a secret. We support only the HMAC-SHA256 algorithm.

```
HMAC_SHA256(
  api_key,
  base64urlEncoding(header) + '.' + base64urlEncoding(payload)
)
```
### Token

Finally, encode the the three parts and concatenate them with the period separator to form the token.

```
scoped_jwt = 'jwt:' + base64urlEncoding(header) + '.' +
    base64urlEncoding(payload) + '.' + base64urlEncoding(signature)
```
