---
title: Precipitation API
permalink: /v2/precipitation/
layout: refdoc
tagline: Returns derived precipitation for a station or set of stations based on a time span
---

## Precipitation Requests

A Precipitation request is an HTTP URL with the following form:

```
https://api.synopticdata.com/v2/stations/precip?parameters
```

Acquiring data from this web service requires certain parameters. When encoding URLs, all parameters are separated using the ampersand (&) character and their value is indicated by an equal sign (=). Below is a list of accepted parameters.

* `token` (_required_), You're application's API token. It is recommended that a new token is created for each application, so that you can manage access and quotas.
* At least one [station selection parameter][station-selectors] i.e. (`stid`, `state`, `network`, `radius`, etc.)
* Either `start` and `end` or `recent` (one is required by not both)
* `start` & `end`, Defines the start and end time of the request with the form of **YYYYMMDDhhmm**. Where _YYYY_ is year, _MM_ is month, _DD_ is day, _hh_ is hour, and _mm_ is minutes. The start parameter must be used with the end parameter. For example: `start=201306011800&end=201306021215`.

> All times are requested in UTC, but may be returned in either UTC or Local time format for each station. See the `obtimezone` parameter.

* `recent`, Indicates the number of minutes to return from the time the request is send. To return the last two hours of observations; `recent=120`.

**Optional Parameters**

* `pmode` (totals, intervals, last), defines the interval mode to calculate precipitation. **If omitted the service reverts to the legacy precipitation service.**

  * `pmode=totals`, Returns totals for the start and end dates.
  * `pmode=intervals`, Returns accumulated precipitation for intervals provided in the additional argument and interval. Valid keywords are **hour**, **day**, **month**, **year**, or **integer in hours**. Default value is day if `interval` is not provided. All intervals in the date range are returned and marked with an interval value in the response. Intervals move from end to start. Partial intervals at the end are still returned.
  * `pmode=last`, Returns accumulated precipitation intervals based on `end` date. **Requires argument `accum_hours`**. Default (omitted) is 1. Accepts a comma separated list of hours, starting from `end` and moving back through time. Each interval always ends on the `end` date. If a `start` value is given, it is ignored. If `end` is not given, a default of "now" is used.

* `obtimezone` (UTC, local), Indicates if the time zone of the response is in UTC (GMT) or the local timezone of the station where the data was observed. A value of either UTC or local. If omitted the default is UTC. Set the timezone used on the observation output (input times are always UTC) Example: `obtimezone=local`

* `showemptystations` (off, on), Indicates if stations with no observations for the requested time span will be returned. Often when querying a large group of stations there will be stations that do not meet the criteria of additional request arguments. Setting this value to "on" will return any station meeting the geographical or network group, even if there is no observation data available.

* `units` (metric, english, [custom format]), Defines the unit of measure for the data to be returned in. By default all data is returned in metric units. For standard measurements used by many in North America "english" will fill most needs. There is also the ability to support custom unit configurations. This is achieved by accessing the variable group such as "temp" and setting the desired unit using a pipe (`,`) character. The following list describes the available units for each variable group.

  * `precip` (mm, cm, in), Precipitation: Millimeters, centimeters, inches.

The following example requests precipitation from from `stid=wbb` for all of January 2017 in terms of day intervals.

```
http://api.synopticdata.com/v2/stations/precip?token=YOUR_TOKEN_HERE&stid=wbb&start=201701010000&end=201701312359&pmode=intervals&interval=day
```

To get receive an API key click here or to create a token from your key click here.

**Response Format Parameters**

* `timeformat`, Defines a time format that all time stamps in the data response to be formatted to. By default the API will return time values in [ISO 8601][iso-8601] format. This behavior can be changed by passing a string with a valid [strftime][strftime] expression. Below are some common examples.

  * `timeformat=%m/%d/%Y at %H:%M` would yield "06/22/2017 at 17:06"
  * `timeformat=%b%20%d%20%Y%20-%20%H:%M` would yield "Jun 22 2017 - 17:06"
  * `timeformat=%s` returns [Unix/POSIX][epoch-seconds] time in terms of seconds.

* `output` (json, xml), Indicates the response format of the request. It's recommend to use the [JSON] format which there are well supported parsing libraries in all major languages. Below are some of the caveats of using a format other than JSON.

## Request Response

**JSON Format**

The Precipitation service will return its results in a single organized and self describing JSON object. At a minimum, every request will return a JSON object with a `"SUMMARY"` field.

An example JSON response would be:

```json
{
  "UNITS": {
    "precipitation": "Millimeters"
  },
  "STATION": [
    {
      "STATUS": "ACTIVE",
      "MNET_ID": "153",
      "PERIOD_OF_RECORD": {
        "start": "1997-01-01T00:00:00Z",
        "end": "2018-01-10T12:00:00Z"
      },
      "ELEVATION": "4806",
      "NAME": "U of U William Browning Building",
      "RESTRICTED": false,
      "STID": "WBB",
      "ELEV_DEM": "4738",
      "LONGITUDE": "-111.84755",
      "STATE": "UT",
      "LATITUDE": "40.76623",
      "TIMEZONE": "America/Denver",
      "ID": "1",
      "OBSERVATIONS": {
        "precipitation": [
          {
            "count": 12,
            "first_report": "2017-01-31T23:00:00Z",
            "interval": 1,
            "report_type": "precip_accum_five_minute",
            "last_report": "2017-01-31T23:55:00Z",
            "total": 0
          }
        ]
      }
    }
  ],
  "SUMMARY": {
    "DATA_QUERY_TIME": 169.8219776154,
    "RESPONSE_CODE": 1,
    "RESPONSE_MESSAGE": "OK",
    "NUMBER_OF_OBJECTS": 1
  }
}
```

* `SUMMARY[]`

  * `NUMBER_OF_OBJECTS`, (always returned) is a integer value of the number of stations returned.
  * `RESPONSE_CODE`, (always returned) is a numerical code indicating the status of the request.

    * "1" = "OK"
    * "2" = "Zero Results"
    * "200" = "Authentication failure"
    * "400" = "Violates a rule of the API"

  * `RESPONSE_MESSAGE`, (always returned) is a string explaining the `RESPONSE_CODE`.
  * `RESPONSE_TIME`, (always returned) server time to process the request.

* `STATION[]`

  * `OBSERVATIONS[]`, contains all the observational data.

<!-- References & URLs -->

[epoch-seconds]: https://en.wikipedia.org/wiki/Unix_time
[iso-8601]: https://en.wikipedia.org/wiki/ISO_8601
[json]: http://json.org/
[station-selectors]: ../station-selectors/
[strftime]: http://man7.org/linux/man-pages/man3/strftime.3.html
