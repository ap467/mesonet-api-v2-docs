---
title: Quality Control Types Service
permalink: /v2/qctypes/
layout: refdoc
tagline: Returns details about a quality control (data attribute) flag
---

## QC Types Requests

```
https://api.synopticdata.comv2/qctypes
```

Returns a list of the available data checks provided by both SynopticLabs and our third party providers. For an in-depth & technical description of the SynopticLabs data checks, [you can read here][sl-data-checks]. This service also provides some information from third party vendors. You can also explore currently available data checks [here][qc-types-lookup].

Acquiring data from this web service requires certain parameters. When encoding URLs, all parameters are separated using the ampersand (&) character and their value is indicated by an equal sign (=). Below is a list of accepted parameters.

* `token` (_required_), You're application's API token. It is recommended that a new token is created for each application, so that you can manage access and quotas.

**Optional Parameters**

* `shortname`, Requests a data check by its short name. This is used to target a particular test. Example: `shortname=sl_range_check`

* `id`, An internal SynopticLabs ID number for a test or test. Example: `id=1,2,3`

The following example returns basic information about the SynopticLabs Range Check.

```
https://api.synopticdata.com/v2/qctypes?token=YOUR_TOKEN_HERE&shortname=sl_range_check
```

**Response Format Parameters**

* `output` (json, xml), Indicates the response format of the request. It's recommend to use the [JSON][json] format which there are well supported parsing libraries in all major languages. Below are some of the caveats of using a format other than JSON.

## Request Response

**JSON Format**

The QC Types service will return its results in a single organized and self describing JSON object. At a minimum, every request will return a JSON object with a `"SUMMARY"` field.

An example JSON response would be:

```json
{
  "QCTYPES": [
    {
      "SOURCE_ID": "1",
      "SHORTNAME": "sl_range_check",
      "ID": "1",
      "NAME": "SynopticLabs Range Check"
    }
  ],
  "SUMMARY": {
    "NUMBER_OF_OBJECTS": 1,
    "RESPONSE_CODE": 1,
    "RESPONSE_MESSAGE": "OK"
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

* `QCTYPES[]`

  * `SOURCE_ID`, provider's ID number. See table below.
  * `SHORTNAME`, short name description of data check.
  * `ID`, data check ID. This is the ID used in reporting data attributes.
  * `NAME`, formal name of data check.

## Provider IDs

| ID  | Provider     |
| --- | ------------ |
| 1   | SynopticLabs |
| 2   | MADIS (NOAA) |

<!-- References & URLs -->

[qc-types-lookup]: https://synopticlabs.org/demos/lookup/?lookup=qctypes
[sl-data-checks]: https://synopticlabs.org/api/mesonet/reference/qc/
[json]: https://json.org/
