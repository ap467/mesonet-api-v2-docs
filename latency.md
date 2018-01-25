---
title: Latency API
permalink: /v2/latency/
layout: refdoc
tagline: Returns transmission latency for a station or set of stations based on a start and end date/time.
---

## Latency Requests

A Latency request is an HTTP URL with the following form:

```
https://api.synopticdata.com/v2/stations/timeseries?parameters
```

This service reports the delay time (in minutes) of an observation received at our ingest servers relative to the observation's timestamp. Due to the nature of computer clock drift and time synchronization, some observations can be received "before they occur". This results in a negative latency value. This can occur from in incorrect time stamp as provided by the station, or (more often than not) natural clock drift.

Acquiring data from this web service requires certain parameters. When encoding URLs, all parameters are separated using the ampersand (&) character and their value is indicated by an equal sign (=). Below is a list of accepted parameters.

* `token` (_required_), You're application's API token. It is recommended that a new token is created for each application, so that you can manage access and quotas.

* At least one [station selection parameter][station-selectors] i.e. (`stid`, `state`, `network`, `radius`, etc.)

* `start` and `end`

  * `start` & `end`, Defines the start and end time of the request with the form of **YYYYMMDDhhmm**. Where _YYYY_ is year, _MM_ is month, _DD_ is day, _hh_ is hour, and _mm_ is minutes. The start parameter must be used with the end parameter. For example: `start=201306011800&end=201306021215`.

    > All times are requested in UTC, but may be returned in either UTC or Local time format for each station. See the `obtimezone` parameter.

**Optional Parameters**

* `obtimezone` (UTC, local), Indicates if the time zone of the response is in UTC (GMT) or the local timezone of the station where the data was observed. A value of either UTC or local. If omitted the default is UTC. Set the timezone used on the observation output (input times are always UTC) Example: `obtimezone=local`

* `showemptystations` (off, on), Indicates if stations with no observations for the requested time span will be returned. Often when querying a large group of stations there will be stations that do not meet the criteria of additional request arguments. Setting this value to "on" will return any station meeting the geographical or network group, even if there is no observation data available.

* `stats` (min, max, mean, median, count, stdev, all), Indicates what statistical values to return. Values can not be combined.

  * `stats=min`, returns the minimum value and time stamp.
  * `stats=max`, returns the maximum value and time stamp.
  * `stats=mean`, returns the mean (average) value and start and end time stamps.
  * `stats=median`, returns the median value and start and end time stamps.
  * `stats=count`, returns the number of mins in time span.
  * `stats=stdev`, returns the standard deviation value and start and end time stamps.
  * `stats=all`, returns all statistics.

The following example returns the latency and statistics for `stid=wbb` in for January 1, 2018:

```
http://api.synopticdata.com/v2/stations/latency?token=dev&stid=wbb&start=201801010000&end=201801012359&stats=all
```

**Response Format Parameters**

* `timeformat`, Defines a time format that all time stamps in the data response to be formatted to. By default the API will return time values in [ISO 8601][iso-8601] format. This behavior can be changed by passing a string with a valid [strftime][strftime] expression. Below are some common examples.

  * `timeformat=%m/%d/%Y at %H:%M` would yield "06/22/2017 at 17:06"
  * `timeformat=%b%20%d%20%Y%20-%20%H:%M` would yield "Jun 22 2017 - 17:06"
  * `timeformat=%s` returns [Unix/POSIX][epoch-seconds] time in terms of seconds.

* `output` (json, xml), Indicates the response format of the request. It's recommend to use the [JSON] format which there are well supported parsing libraries in all major languages. Below are some of the caveats of using a format other than JSON.

## Request Response

**JSON Format**

The Latency service will return its results in a single organized and self describing JSON object. At a minimum, every request will return a JSON object with a `"SUMMARY"` field.

An example request response in JSON would be:

```
{
  STATION: [
    {
      STATUS: "ACTIVE",
      MNET_ID: "153",
      PERIOD_OF_RECORD: {
        start: "1997-01-01T00:00:00Z",
        end: "2018-01-09T00:45:00Z"
      },
      ELEVATION: "4806",
      NAME: "U of U William Browning Building",
      LATITUDE: "40.76623",
      TIMEZONE: "America/Denver",
      ID: "1",
      STID: "WBB",
      LONGITUDE: "-111.84755",
      STATE: "UT",
      LATENCY: {
        date_time: [
          "2018-01-01T00:00:00Z", "2018-01-01T00:01:00Z",
          "2018-01-01T00:02:00Z", ...
        ],
        values: [-3, 5, 4, ...]
      },
      STATISTICS: {
        count: 1440,
        start: "201801010000",
        minimum: -3,
        end: "201801012359",
        mintime: "2018-01-01T00:00:00Z",
        standard_deviation: 2.766415596,
        maxtime: "2018-01-01T00:01:00Z",
        median: 3,
        average: 2.2166666985,
        maximum: 5
      }
    }
  ],
    SUMMARY: {
      RESPONSE_CODE: 1,
      RESPONSE_MESSAGE: "OK",
      TOTAL_DATA_TIME: "45.5708503723 ms",
      NUMBER_OF_OBJECTS: 1
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

* `STATION{}`

  * `LATENCY[]`, contains a timestamps and latency values in (in minutes)
  * `STATISTICS[]`, contains statistics.

<!-- References & URLs -->

[station-selectors]: ../station-selectors/
[timeseries-api]: ../timeseries/
[network-api]: ../networks/
[epoch-seconds]: https://en.wikipedia.org/wiki/Unix_time
[iso-8601]: https://en.wikipedia.org/wiki/ISO_8601
[json]: http://json.org/
[sl-range-check]: https://synopticlabs.org/api/mesonet/reference/qc/#Range_check
[strftime]: http://man7.org/linux/man-pages/man3/strftime.3.html
