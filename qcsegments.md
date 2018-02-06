---
title: Quality Control Segments Service
permalink: /v2/qcsegments/
layout: refdoc
tagline: Returns data for a station or set of stations for a time span
bannerimage: qcsegments.png
bannercaption: QC Segments
---

## QC Segments Requests

A QC Segments request is an HTTP URL with the following form:

```
https://api.synopticdata.com/v2/qcsegments
```

Acquiring data from this web service requires certain parameters. When encoding URLs, all parameters are separated using the ampersand (&) character and their value is indicated by an equal sign (=). Below is a list of accepted parameters.

* `token` (_required_), You're application's API token. It is recommended that a new token is created for each application, so that you can manage access and quotas.

* At least one [station selection parameter][station-selectors] i.e. (`stid`, `state`, `network`, `radius`, etc.)

* Either `start` and `end` or `recent` (one is required by not both)

  * `start` & `end`, Defines the start and end time of the request with the form of **YYYYMMDDhhmm**. Where _YYYY_ is year, _MM_ is month, _DD_ is day, _hh_ is hour, and _mm_ is minutes. The start parameter must be used with the end parameter. For example: `start=201306011800&end=201306021215`.

    > All times are requested in UTC, but may be returned in either UTC or Local time format for each station. See the `obtimezone` parameter.

  * `recent`, Indicates the number of minutes to return from the time the request is send. To return the last two hours of observations; `recent=120`.

**Optional Parameters**

* `inside` (1, 0), Requires the QC segment start within the requested time span. Example: `inside=1`.

* `obtimezone` (UTC, local), Indicates if the time zone of the response is in UTC (GMT) or the local timezone of the station where the data was observed. A value of either UTC or local. If omitted the default is UTC. Set the timezone used on the observation output (input times are always UTC) Example: `obtimezone=local`

* `showemptystations` (off, on), Indicates if stations with no observations for the requested time span will be returned. Often when querying a large group of stations there will be stations that do not meet the criteria of additional request arguments. Setting this value to "on" will return any station meeting the geographical or network group, even if there is no observation data available.

* `qc_checks` ([flag name], [flag source], all) defines a list of data checks applied to data values. The settings of other QC parameters determines how the data and data checks are returned.

  * "all" will return any data check in our system.
  * "flag name" allows the targeting of a flag by name or a list (comma separated) of flags.
  * "flag source" allows targeting a data check provider i.e. "synopticlabs" for SynopticLabs.

The following example request all the stations in Utah with an open QC segment within the last two hours:

```
https://api.synopticdata.com/v2/qcsegments?state=ut&recent=120&token=YOUR_TOKEN_HERE
```

The following example requests only the QC segments for air temperature at KSLC (Salt Lake City Airport) on January 3, 2015:

```
https://api.synopticdata.com/v2/qcsegments?stid=kslc&start=201501030000&end=201501032359&vars=air_temp&token=YOUR_TOKEN_HERE
```

To get receive an API key click here or to create a token from your key click here.

**Response Format Parameters**

* `timeformat`, Defines a time format that all time stamps in the data response to be formatted to. By default the API will return time values in [ISO 8601][iso-8601] format. This behavior can be changed by passing a string with a valid [strftime][strftime] expression. Below are some common examples.

  * `timeformat=%m/%d/%Y at %H:%M` would yield "06/22/2017 at 17:06"
  * `timeformat=%b%20%d%20%Y%20-%20%H:%M` would yield "Jun 22 2017 - 17:06"
  * `timeformat=%s` returns [Unix/POSIX][epoch-seconds] time in terms of seconds.

* `output` (json, xml, geojson), Indicates the response format of the request. It's recommend to use the [JSON] format which there are well supported parsing libraries in all major languages. Below are some of the caveats of using a format other than JSON.

  * GeoJSON will only return the best guess sensor if the station has multiple sensors of the same type.

## Request Response

**JSON Format**

The QC Segments service will return its results in a single organized and self describing JSON object. At a minimum, every request will return a JSON object with a `"SUMMARY"` field.

An example JSON response would be:

```json
{
  "QC_SHORTNAMES": {
    "78": "sl_multvariate_lin_reg_check",
    "80": "sl_uu2dvar_rejection"
  },
  "QC_SOURCENAMES": {
    "78": "SynopticLabs",
    "80": "SynopticLabs"
  },
  "STATION": [
    {
      "STATUS": "ACTIVE",
      "MNET_ID": "195",
      "PERIOD_OF_RECORD": {
        "start": "2014-03-20T00:00:00Z",
        "end": "2018-01-22T02:00:00Z"
      },
      "ELEVATION": "3885",
      "NAME": "ARNOLD CA",
      "DISTANCE": 7.77,
      "RESTRICTED": false,
      "STID": "ARLC1",
      "STATE": "CA",
      "LATITUDE": "38.23",
      "TIMEZONE": "America/Los_Angeles",
      "ID": "42541",
      "ELEV_DEM": "3819",
      "LONGITUDE": "-120.36",
      "SENSOR_VARIABLES": {
        "air_temp": { "air_temp_qc_1": {} }
      },
      "QC": [
        {
          "start": "2018-01-21T17:00:00Z",
          "qc_flag": 78,
          "sensor": "air_temp_qc_1",
          "end": "2018-01-21T17:00:00Z",
          "is_open": true
        },
        {
          "start": "2018-01-21T17:00:00Z",
          "qc_flag": 80,
          "sensor": "air_temp_qc_1",
          "end": "2018-01-21T17:00:00Z",
          "is_open": false
        }
      ]
    }
  ],
  "SUMMARY": {
    "RESPONSE_CODE": 1,
    "RESPONSE_MESSAGE": "OK",
    "TOTAL_DATA_TIME": "14.3580436707 ms",
    "NUMBER_OF_OBJECTS": 17
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

  * `QC[]`, contains the QC segments.
    * `start`, start of segment.
    * `end`: end time of segment. If the segment is open, the end time will be time of query.
    * `qc_flag`, QC check ID (see [QC Types][qctypes-api]).
    * `sensor`, sensor name and instance.
    * `is_open`, is the segment open or closed.

* `QC_SHORTNAMES{}`, key/value pairs of QC flag IDs to short names.

* `QC_NAMES{}`, key/value pair of QC flag IDs to flag's formal name.

* `QC_SOURCENAMES{}`, key/value pairs of QC flag IDs to provider name.

**Note**: The data in this example is simulated.

<!-- References & URLs -->

[station-selectors]: ../station-selectors/
[timeseries-api]: ../timeseries/
[network-api]: ../networks/
[epoch-seconds]: https://en.wikipedia.org/wiki/Unix_time
[iso-8601]: https://en.wikipedia.org/wiki/ISO_8601
[json]: https://json.org/
[sl-range-check]: https://synopticlabs.org/api/mesonet/reference/qc/#Range_check
[strftime]: https://man7.org/linux/man-pages/man3/strftime.3.html
[qctypes-api]: ../qctypes/
