---
title: Authentication Service
permalink: /v2/auth/
layout: refdoc
tagline: Create and manage tokens to access Mesonet APIs.
---

## Request Format

Interface for account management services. **Should not be used by clients** because it interfaces with your private API key, not tokens.

An Authentication request is an HTTP URL with the following form:

```
https://api.synopticdata.com/v2/auth
```

Acquiring data from this web service requires certain parameters. When encoding URLs, all parameters are separated using the ampersand (&) character and their value is indicated by an equal sign (=). Below is a list of accepted parameters.

* `apikey` (_required_), Your private API key. All tokens created with this key will be associated with this key.

**Optional Parameters**

> Only the private key used to generate a token can be used to perform actions on that token.

* `disableToken` (token), Permanently deletes the token passed. **This action is un-revocable**.
* `list` (1|0), returns a list of the tokens associated with the provided API key.

**Response Format Parameters**

* `output` (json|xml), Indicates the response format of the request. It's recommend to use the [JSON] format which there are well supported parsing libraries in all major languages.

## Request Response

The Authentication service will return single organized and self describing JSON object. Each response is a slight bit different for each command executed against the API.

### Create a token

```json
# https://api.synopticdata.com/v2/auth?apikey=ABC123

{
  "TOKEN": "1672c91e97a7421f8ac67f7681d5810a"
}
```

### List all tokens

```json
# https://api.synopticdata.com/v2/auth?apikey=ABC123&list=1

{
  "TOKENS": [
    "a6a82dddc14a46c892077bded6f5a342",
    "3839e26df2c54ae1aa064dc677ac1697",
    "0ca4be58420a43b3a27251ad9c376978",
    "1672c91e97a7421f8ac67f7681d58154"
  ]
}
```

### Delete a token

```json
# https://api.synopticdata.com/v2/auth?apikey=ABC123&disableToken=1672c91e97a7421f8ac67f7681d58142

{
  "MESSAGE": "Token 1672c91e97a7421f8ac67f7681d58142 is disabled."
}
```

<!-- References & URLs -->
