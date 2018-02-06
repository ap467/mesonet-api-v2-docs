---
title: Network Types API
permalink: /v2/networktypes/
layout: refdoc
tagline: Returns network category metadata
---

# Network Types Web Service

> Returns network category metadata.

## Network Types Requests

```
https://api.synopticdata.com/v2/networktypes
```

Returns a list of network types. You can also see a list of them [here][networktypes-lookup].

Acquiring data from this web service requires certain parameters. When encoding URLs, all parameters are separated using the ampersand (&) character and their value is indicated by an equal sign (=). Below is a list of accepted parameters.

* `token` (_required_), You're application's API token. It is recommended that a new token is created for each application, so that you can manage access and quotas.

**Optional Parameters**

* `id`, An internal SynopticLabs ID number for the network type. Example: `id=1,2,3`

**Response Format Parameters**

* `output` (json, xml), Indicates the response format of the request. It's recommend to use the [JSON][json] format which there are well supported parsing libraries in all major languages. Below are some of the caveats of using a format other than JSON.

## Request Response

**JSON Format**

The QC Types service will return its results in a single organized and self describing JSON object. At a minimum, every request will return a JSON object with a `"SUMMARY"` field.

An example JSON response would be:

```json
{
  "MNETCAT": [
    {
      "DESCRIPTION": "Agricultural",
      "ID": "1",
      "NAME": "AG"
    },

    ...

  ],
  "SUMMARY": {
    "NUMBER_OF_OBJECTS": 13,
    "RESPONSE_CODE": 1,
    "RESPONSE_MESSAGE": "OK",
  }
}
```

* `SUMMARY{}`

  * `NUMBER_OF_OBJECTS`, (always returned) is a integer value of the number of stations returned.
  * `RESPONSE_CODE`, (always returned) is a numerical code indicating the status of the request.

    * "1" = "OK"
    * "2" = "Zero Results"
    * "200" = "Authentication failure"
    * "400" = "Violates a rule of the API"

  * `RESPONSE_MESSAGE`, (always returned) is a string explaining the `RESPONSE_CODE`.

* `MNETCAT[]`

  * `DESCRIPTION`, description of network.
  * `NAME`, short name of network type.
  * `ID`, network type ID.

<!-- References & URLs -->

[networktypes-lookup]: https://synopticlabs.org/demos/lookup/?lookup=networktypes
[json]: https://json.org/
