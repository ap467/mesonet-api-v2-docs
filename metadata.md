---
title: Metadata API
permalink: /v2/metadata
layout: refdoc
tagline: Returns metadata for a station or set of stations
---

## Metadata Requests

A Metadata request is an HTTP URL with the following form:

```
https://api.synopticdata.com/v2/stations/metadata?parameters
```

Acquiring data from this web service requires certain parameters. When encoding URLs, all parameters are separated using the ampersand (&) character and their value is indicated by an equal sign (=). Below is a list of accepted parameters.

* `token` (_required_), You're application's API token. It is recommended that a new token is created for each application, so that you can manage access and quotas.
* At least one [station selection parameter][station-selectors] i.e. (`stid`, `state`, `network`, `radius`, etc.)

**Optional Parameters**

* `complete` (0, 1), Indicates if the complete metadata for the station will be returned. By default only the most common metadata values are returned.
* `sensorvars` (0, 1), Indicates if the stations sensor information will be returned. If true, then the response will contain a complete list of all the sensors the station has ever owned, regardless if they are currently active. Each sensor element contains a `PERIOD_OF_RECORD` value that describes the period of the sensor being active.
* `obrange` (start, start,end), Defines the start and end time of the request with the form of **YYYYMMDDhhmm**. Where _YYYY_ is year, _MM_ is month, _DD_ is day, _hh_ is hour, and _mm_ is minutes. The start parameter must be used with the end parameter. For example: `obrange=201306011800,201306021215`. If no end is provided the response will contain all the stations returned from the start until the current time.

**Response Format Parameters**

* `timeformat`, Defines a time format that all time stamps in the data response to be formatted to. By default the API will return time values in [ISO 8601][iso-8601] format. This behavior can be changed by passing a string with any combination of valid [strftime][strftime] expression. Below are some common examples.

  * `timeformat=%m/%d/%Y at %H:%M` would yield "06/22/2017 at 17:06"
  * `timeformat=%b%20%d%20%Y%20-%20%H:%M` would yield "Jun 22 2017 - 17:06"
  * `timeformat=%s` returns [Unix/POSIX][epoch-seconds] time in terms of seconds.

* `output` (json, xml, geojson), Indicates the response format of the request. It's recommend to use the [JSON] format which there are well supported parsing libraries in all major languages. Below are some of the caveats of using a format other than JSON.

## Request Response

**JSON Format**

The Metadata service will return its results in a single organized and self describing JSON object. At a minimum, every request will return a JSON object with a `"SUMMARY"` field.

An example request response in JSON would be:

```
{
  STATION: [{
    STATUS: "ACTIVE",
    MNET_ID: "1",
    LONGITUDE: "-111.96503",
    LATITUDE: "40.77069",
    TIMEZONE: "America/Denver",
    ID: "53"
    STATE: "UT",
    PERIOD_OF_RECORD: {
      start: "1970-01-01T00:00:00Z",
      end: "2017-06-22T18:20:00Z"
    },
    ELEVATION: "4226",
    NAME: "Salt Lake City, Salt Lake City International Airport",
    QC_FLAGGED: false,
    STID: "KSLC",
    SENSOR_VARIABLES: {
      wind_direction: {
        wind_direction_1: {
          position: "10",
          period_of_record: {
            start: "2016-01-04T20:30:00Z",
            end: "2017-08-23T16:50:00Z"
          }
        }
      },
      ...
    }
  }],
  SUMMARY: {
    RESPONSE_CODE: 1,
    RESPONSE_MESSAGE: "OK",
    NUMBER_OF_OBJECTS: 1,
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
  * `RESPONSE_TIME`, (always returned) server time to process the request.

* `STATION[]`

  * `SENSOR_VARIABLES[]`, summary of variables in the OBSERVATIONS element.

* `QC_SUMMARY{}`

  * `QC_TESTS_APPLIED[]`, a list of data checks that were applied to the data.
  * `TOTAL_OBSERVATIONS_FLAGGED`, number of observations that have additional data check attributes.
  * `PERCENT_OF_TOTAL_OBSERVATIONS_FLAGGED`, floating point number indicating the percentage of the observations that have additional data check attributes.

<!-- References & URLs -->

[station-selectors]: ./station-selectors
[epoch-seconds]: https://en.wikipedia.org/wiki/Unix_time
[iso-8601]: https://en.wikipedia.org/wiki/ISO_8601
[json]: http://json.org/
[sl-range-check]: https://synopticlabs.org/api/mesonet/reference/qc/#Range_check
[strftime]: http://man7.org/linux/man-pages/man3/strftime.3.html
