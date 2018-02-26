---
title: Time Series Service
permalink: /mesonet/v2/timeseries/
layout: refdoc
tagline: Returns data for a station or set of stations based on a time span
bannerimage: timeseries.png
bannercaption: Time Series API
---

## Request Format

A Time Series request is an HTTP URL with the following form:

```
https://api.synopticdata.comv2/stations/timeseries
```

Acquiring data from this web service requires certain parameters. When encoding URLs, all parameters are separated using the ampersand (&) character and their value is indicated by an equal sign (=). Below is a list of accepted parameters.

* `token` (_required_), Your application's API token. It is recommended that a new token is created for each application, so that you can manage access and quotas.

* At least one [station selection parameter][station-selectors] i.e. (`stid`, `state`, `network`, `radius`, etc.)

* Either `start` and `end` or `recent` (one is required by not both)

  * `start` & `end`, Defines the start and end time of the request with the form of **YYYYMMDDhhmm**. Where _YYYY_ is year, _MM_ is month, _DD_ is day, _hh_ is hour, and _mm_ is minutes. The start parameter must be used with the end parameter. For example: `&start=201306011800&end=201306021215`.

    > All times are requested in UTC, but may be returned in either UTC or Local time format for each station. See the `obtimezone` parameter.

  * `recent`, Indicates the number of minutes to return from the time the request is send. To return the last two hours of observations; `recent=120`.

**Optional Parameters**

* `obtimezone` (UTC, local), Indicates if the time zone of the response is in UTC (GMT) or the local timezone of the station where the data was observed. A value of either UTC or local. If omitted the default is UTC. Set the timezone used on the observation output (input times are always UTC) Example: `obtimezone=local`

* `showemptystations` (off, on), Indicates if stations with no observations for the requested time span will be returned. Often when querying a large group of stations there will be stations that do not meet the criteria of additional request arguments. Setting this value to "on" will return any station meeting the geographical or network group, even if there is no observation data available.

* `units` (metric, english, [custom format]), Defines the unit of measure for the data to be returned in. By default all data is returned in metric units. For standard measurements used by many in North America "english" will fill most needs. There is also the ability to support custom unit configurations. This is achieved by accessing the variable group such as "temp" and setting the desired unit using a pipe (`,`) character. The following list describes the available units for each variable group.

  * `temp` (C, F, K), Temperature: Celsius, Fahrenheit and Kelvin.
  * `speed` (mps, mph, kph, kts), Speed/Velocity: Meters per second, miles per hour, kilometers per hour, knots.
  * `pres` (pa, mb), Pressure: Pascals, millibars.
  * `height` (m, ft), Height: Meters, feet.
  * `precip` (mm, cm, in), Precipitation: Millimeters, centimeters, inches.
  * `alti` (pa, inhg), Altimeter: Pascals, inches mercury.

  Furthermore, it is possible to modify one of the preset settings (metric/english). This is achieved by appending a variable group and unit to the parameter string with a comma. For example, to use "english" units with altimeter in inHg the parameter would be `units=english,alti, inhg`.

* `precip` (1, 0), Enable derived precip. All raw precip variables will be replaced with two new variables called `precip_interval_set_x` and `precip_accum_set_x`. Example: `precip=1`.

* `hfmetars` (1, 0), Returns high frequency (5 minute) observations for many of the stations available in the NWS/FAA network. You can read more about these [here][hfmetar-blog-post].

The following example will request all the stations in Utah with an observation within the last two hours:

```
https://api.synopticdata.com/v2/stations/timeseries?state=ut&recent=120&token=YOUR_TOKEN_HERE
```

The following example will request only the air temperature for KSLC (Salt Lake City Airport) on January 3, 2015:

```
https://api.synopticdata.com/v2/stations/timeseries?stid=kslc&start=201501030000&end=201501032359&vars=air_temp&token=YOUR_TOKEN_HERE
```

**Response Format Parameters**

* `timeformat`, Defines a time format that all time stamps in the data response to be formatted to. By default the API will return time values in [ISO 8601][iso-8601] format. This behavior can be changed by passing a string with a valid [strftime][strftime] expression. Below are some common examples.

  * `timeformat=%m/%d/%Y at %H:%M` would yield "06/22/2017 at 17:06"
  * `timeformat=%b%20%d%20%Y%20-%20%H:%M` would yield "Jun 22 2017 - 17:06"
  * `timeformat=%s` returns [Unix/POSIX][epoch-seconds] time in terms of seconds.

* `output` (json, csv, xml, geojson), Indicates the response format of the request. It's recommend to use the [JSON] format which there are well supported parsing libraries in all major languages. Below are some of the caveats of using a format other than JSON.

  * CSV only works when requesting a single station. I.e using the `stid` parameter.
  * GeoJSON will only return the best guess sensor if the station has multiple sensors of the same type.

**Data Checks and Quality Control**

By default, the API does not return data that has been flagged as non-plausible by the [SynopticLabs Range Check][sl-range-check], e.g. a temperature value of 200C. It does provide a series of URL arguments that allow the retrieval of removed data as well as their flags.

If the `qc` parameter is omitted then the API will return data while assuming the following QC parameters: `qc=on`, `qc_remove_data=on`, `qc_flags=off` and `qc_checks=sl_range_check`. Below is an explanation of parameters that control the application of data checks (qc) to the data.

* `qc` (on, off), Indicates the application behavior of the data check (qc) attributes on the data requested. If set to "off" then all data will be returned _without data checks and quality control_ (this is not recommended). It is possible to target a particular SynopticLabs data check or ask for the entire SynopticLabs test suite, i.e. `qc=on`. Regardless if any data checks are returned, a `QC_FLAGGED: [bool]` key will appear in the JSON response inside each `STATION` element. Data checks will be in a key titled QC.

  > Note: The existence of a data check flag for an observation is not necessarily an indication of invalid or inaccurate data. For a detailed explanation how to apply data checks and how to leverage them, please [click here][qc-indepth] to read more.

* `qc_remove_data` (on, off, mark), Indicates the response behavior for an observation that fails a user specified data check.

  * "off" returns the data values even if a data check failure is present for that data.
  * "on" removes failed data values with a "null".
  * "mark" replaces failed data with a value of "false".

* `qc_flags` (on, off) Indicates whether the data checks are returned alongside any data that failed a requested check. If "on" then the data checks will be returned in the `STATION[n].QC.sensor_name` block.

* `qc_checks` ([flag name], [flag source], all) defines a list of data checks applied to data values. The settings of other qc parameters determines how the data and data checks are returned.

  * "all" will return any data check in our system.
  * "flag name" allows the targeting of a flag by name or a list (comma separated) of flags.
  * "flag source" allows targeting a data check provider i.e. "synopticlabs" for SynopticLabs.

Some common examples of modifying the default QC parameters are:

* `qc_checks=synopticlabs,ma_range_check`, Applies the SynopticLabs test suite and MADIS range check
* `qc_checks=synopticlabs,madis`, Applies the SynopticLabs and MADIS test suites.

## Request Response

**JSON Format**

The Time Series service will return its results in a single organized and self describing JSON object. At a minimum, every request will return a JSON object with a `SUMMARY` field.

An example JSON response would be:

```json
{
  "UNITS": {
    "air_temp": "Celsius"
  },
  "QC_SUMMARY": {
    "QC_TESTS_APPLIED": ["sl_range_check"],
    "TOTAL_OBSERVATIONS_FLAGGED": 0,
    "PERCENT_OF_TOTAL_OBSERVATIONS_FLAGGED": 0
  },
  "STATION": [
    {
      "STATUS": "ACTIVE",
      "MNET_ID": "1",
      "LONGITUDE": "-111.96503",
      "LATITUDE": "40.77069",
      "TIMEZONE": "America/Denver",
      "ID": "53",
      "STATE": "UT",
      "PERIOD_OF_RECORD": {
        "start": "1970-01-01T00:00:00Z",
        "end": "2017-06-22T18:20:00Z"
      },
      "ELEVATION": "4226",
      "NAME": "Salt Lake City, Salt Lake City International Airport",
      "QC_FLAGGED": false,
      "STID": "KSLC",
      "SENSOR_VARIABLES": {
        "date_time": { "date_time": {} },
        "air_temp": {
          "air_temp_set_1": {
            "position": ""
          }
        }
      },
      "OBSERVATIONS": {
        "date_time": [
          "2015-01-03T00:00:00Z",
          "2015-01-03T00:05:00Z",
          "2015-01-03T00:10:00Z",
          "2015-01-03T00:15:00Z",
          "2015-01-03T00:20:00Z"
        ],
        "air_temp_set_1": [-5.6, -5.6, -6.1, -6.1, -6.7]
      }
    }
  ],
  "SUMMARY": {
    "RESPONSE_CODE": 1,
    "RESPONSE_MESSAGE": "OK",
    "TOTAL_DATA_TIME": "12.0379924774 ms",
    "NUMBER_OF_OBJECTS": 1
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
  * `OBSERVATIONS[]`, contains all the observational data.
  * `QC[]`, contains all the data attributes.
  * `QC_FLAGGED`, boolean value indicating data check attributes are returned (if requested).

* `QC_SUMMARY{}`

  * `QC_TESTS_APPLIED[]`, a list of data checks that were applied to the data.
  * `TOTAL_OBSERVATIONS_FLAGGED`, number of observations that have additional data check attributes.
  * `PERCENT_OF_TOTAL_OBSERVATIONS_FLAGGED`, floating point number indicating the percentage of the observations that have additional data check attributes.

<!-- References & URLs -->

[station-selectors]: ../station-selectors/
[timeseries-api]: ../timeseries/
[network-api]: ../networks/
[epoch-seconds]: https://en.wikipedia.org/wiki/Unix_time
[iso-8601]: https://en.wikipedia.org/wiki/ISO_8601
[json]: https://json.org/
[sl-range-check]: https://synopticlabs.org/api/mesonet/reference/qc/#Range_check
[strftime]: https://man7.org/linux/man-pages/man3/strftime.3.html
[hfmetar-blog-post]: https://blog.synopticlabs.org/blog/2016/08/29/hf-metars.html
