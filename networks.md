---
title: Networks API
permalink: /v2/networks
layout: refdoc
tagline: Returns metadata and analytics on the networks within the Mesonet dataset
---

## Networks Requests

```
https://api.synopticdata.com/v2/networks
```

Returns a list of networks both current and previous that have data within the Mesonet dataset. You can also explore networks [here][networks-lookup].

_This service returns basic information regarding restricted networks. Meaning you may see networks that will require an upgraded service account to access._

Acquiring data from this web service requires certain parameters. When encoding URLs, all parameters are separated using the ampersand (&) character and their value is indicated by an equal sign (=). Below is a list of accepted parameters.

* `token` (_required_), You're application's API token. It is recommended that a new token is created for each application, so that you can manage access and quotas.

**Optional Parameters**

* `id` (network id), Single or comma separated list of network IDs. Example: `&id=1,2,3,4`.

* `shortname` (network short name) Single or comma separated list of network short names. Example: `&shortname=uunet,raws`.

* `sortby` (alphabet) Only valid value is alphabet for determining the sorting order. By default networks are sorted by ID. Example: `&sortby=alphabet`.

**Response Format Parameters**

* `output` (json, xml), Indicates the response format of the request. It's recommend to use the [JSON][json] format which there are well supported parsing libraries in all major languages. Below are some of the caveats of using a format other than JSON.

## Request Response

**JSON Format**

The Network service will return its results in a single organized and self describing JSON object. At a minimum, every request will return a JSON object with a `"SUMMARY"` field.

An example request response in JSON would be:

```
{
  MNET: [
    {
      CATEGORY: "6",
      REPORTING_STATIONS: 57,
      LAST_OBSERVATION: "2018-01-19T15:50:00Z",
      URL: null,
      PERCENT_REPORTING: 98.28,
      PERIOD_CHECKED: 120,
      TOTAL_STATIONS: 64,
      ACTIVE_STATIONS: 58,
      LONGNAME: "University of Utah MesoWest Mesonet",
      SHORTNAME: "UUNET",
      PERCENT_ACTIVE: 90.63,
      ID: "153"
    },

    ...

    ],
    SUMMARY: {
      NUMBER_OF_OBJECTS: 123,
      RESPONSE_CODE: 1,
      RESPONSE_MESSAGE: "OK",
      RESPONSE_TIME: "0.164985656738 ms"
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

* `MNET[]`

  * `CATEGORY`, network type. See [Network Types][networktypes-api] service for details
  * `REPORTING_STATIONS`, number of stations currently reporting in this network. Interval is defined below.
  * `LAST_OBSERVATION`, time stamp of last observation seen.
  * `URL`, network/data provider's URL, if available.
  * `PERCENT_REPORTING`, percentage of active stations currently reporting data.
  * `PERIOD_CHECKED`, the interval of time used to calculate analytics. In minutes.
  * `TOTAL_STATIONS`, total number of stations assigned to this network.
  * `ACTIVE_STATIONS`: number of stations currently set to active status.
  * `LONGNAME`, formal name of network.
  * `SHORTNAME`, short name of network. This can be used to with the `network` selector.
  * `PERCENT_ACTIVE`, percentage of active stations compared to the total number of stations assigned to this network.
  * `ID`, network ID number. This can be used to with the `network` selector.

<!-- References & URLs -->

[networks-lookup]: https://synopticlabs.org/demos/lookup/?lookup=variables
[json]: http://json.org/
