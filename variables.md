---
title: Variables API
permalink: /v2/variables/
layout: refdoc
tagline: Returns a list of the available variables within the Mesonet APIs
---

## Variables Requests

```
https://api.synopticdata.com/v2/variables
```

Returns a list of known variables (sensors) available within the Mesonet APIs. You can also explore currently available sensors [here][variables-lookup].

Acquiring data from this web service requires certain parameters. When encoding URLs, all parameters are separated using the ampersand (&) character and their value is indicated by an equal sign (=). Below is a list of accepted parameters.

* `token` (_required_), You're application's API token. It is recommended that a new token is created for each application, so that you can manage access and quotas.

**Optional Parameters**

No optional arguments.

**Response Format Parameters**

* `output` (json, xml), Indicates the response format of the request. It's recommend to use the [JSON][json] format which there are well supported parsing libraries in all major languages. Below are some of the caveats of using a format other than JSON.

## Request Response

**JSON Format**

The variables service will return its results in a single organized and self describing JSON object. At a minimum, every request will return a JSON object with a `"SUMMARY"` field.

An example JSON response would be:

```json
{
  "VARIABLES": [
    {
      "air_temp": {
        "long_name": "Temperature",
        "unit": "Celsius"
      }
    },
    {
      "dew_point_temperature": {
        "long_name": "Dew Point",
        "unit": "Celsius"
      }
    }

    ...

    ],
    "SUMMARY": {
      "NUMBER_OF_OBJECTS": 123,
      "RESPONSE_CODE": 1,
      "RESPONSE_MESSAGE": "OK",
      "RESPONSE_TIME": "0.164985656738 ms"
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

* `VARIABLES[]`

  * `long_name`, formal name of sensor or variable
  * `unit`, default unit of measure

<!-- References & URLs -->

[variables-lookup]: https://synopticlabs.org/demos/lookup/?lookup=variables
[json]: https://json.org/
