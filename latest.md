---
title: Latest API
permalink: /v2/latest/
layout: refdoc
tagline: Returns the most recent observation from a station or set of stations
bannerimage: latest.png
bannercaption: Latest API
---

## Latest Requests

A Latest request is an HTTP URL with the following form:

```
https://api.synopticdata.com/v2/stations/latest?parameters
```

Acquiring data from this web service requires certain parameters. When encoding URLs, all parameters are separated using the ampersand (&) character and their value is indicated by an equal sign (=). Below is a list of accepted parameters.

* `token` (_required_), You're application's API token. It is recommended that a new token is created for each application, so that you can manage access and quotas.

**Optional Parameters**

* At least one [station selection parameter][station-selectors] i.e. (`stid`, `state`, `network`, `radius`, etc.). **It is highly recommended that you provide at lease one station selection parameter**. Without one, the response will contain the most recent observation from every station in our inventory.

* `obtimezone` (UTC, local), Indicates if the time zone of the response is in UTC (GMT) or the local timezone of the station where the data was observed. A value of either UTC or local. If omitted the default is UTC. Set the timezone used on the observation output (input times are always UTC) Example: `obtimezone=local`

* `showemptystations` (off, on), Indicates if stations with no observations for the requested time span will be returned. Often when querying a large group of stations there will be stations that do not meet the criteria of additional request arguments. Setting this value to "on" will return any station meeting the geographical or network group, even if there is no observation data available.

* `units` (metric, english, [custom format]), Defines the unit of measure for the data to be returned in. By default all data is returned in metric units. For standard measurements used by many in North America "english" will fill most needs. There is also the ability to support custom unit configurations. This is achieved by accessing the variable group such as "temp" and setting the desired unit using a pipe (`,`) character. The following list describes the available units for each variable group.

* `within`, Restricts the response to observations to a time window ending at the current observation, i.e. `within=60` would return only observations within the last 60 minutes.

**Response Format Parameters**

* `timeformat`, Defines a time format that all time stamps in the data response to be formatted to. By default the API will return time values in [ISO 8601][iso-8601] format. This behavior can be changed by passing a string with any combination of valid [strftime][strftime] expression. Below are some common examples.

  * `timeformat=%m/%d/%Y at %H:%M` would yield "06/22/2017 at 17:06"
  * `timeformat=%b%20%d%20%Y%20-%20%H:%M` would yield "Jun 22 2017 - 17:06"
  * `timeformat=%s` returns [Unix/POSIX][epoch-seconds] time in terms of seconds.

* `output` (json, xml, geojson), Indicates the response format of the request. It's recommend to use the [JSON] format which there are well supported parsing libraries in all major languages. Below are some of the caveats of using a format other than JSON.

  * GeoJSON will only return the best guess sensor if the station has multiple sensors of the same type.

**Data Checks and Quality Control**

By default, the API does not return data that has been flagged as non-plausible by the [SynopticLabs Range Check][sl-range-check], e.g. a temperature value of 200C. It does provide a series of URL arguments that allow the retrieval of removed data as well as their flags.

If the `qc` parameter is omitted then the API will return data while assuming the following QC parameters: `qc=on`, `qc_remove_data=on`, `qc_flags=off` and `qc_checks=sl_range_check`. Below is an explanation of parameters that control the application of data checks (QC) to the data.

* `qc` ("on, off"), Indicates the application behavior of the QC attributes on the data requested. If set to "off" then all data will be returned _without data checks and quality control_ (this is not recommended). It is possible to target a particular SynopticLabs data check or ask for the entire SynopticLabs test suite, i.e. `qc=on`. Regardless if any data checks are returned, a `"QC_FLAGGED: [bool]"` key will appear in the JSON response inside each `"STATION"` element. Data checks will be in the response key titled QC.

  > Note: The existence of a data check flag for an observation is not necessarily an indication of invalid or inaccurate data. For a detailed explanation how to apply data checks and how to leverage them, please click here to read more. Optional Output formatting and usability parameters

* `qc_remove_data` ("on, off, mark"), Indicates the response behavior for an observation that fails a user specified data check.

  * "off" returns the data values even if a data check failure is present for that data.
  * "on" removes failed data values with a "null".
  * "mark" replaces failed data with a value of "false".

* `qc_flags` ("on, off") Indicates whether the data checks are returned alongside any data that failed a requested check. If "on" then the data checks will be returned in the `"STATION[n].QC.sensor_name"` block.

* `qc_checks` ("[flag name], [flag source], all") defines a list of data checks applied to data values. The settings of other QC parameters determines how the data and data checks are returned.

  * "all" will return any data check in our system.
  * "flag name" allows the targeting of a flag by name or a list (comma separated) of flags.
  * "flag source" allows targeting a data check provider i.e. "synopticlabs" for SynopticLabs.

Some common examples of modifying the default QC parameters are:

* `qc_checks=synopticlabs,ma_range_check`, Applies the SynopticLabs test suite and MADIS range check
* `qc_checks=synopticlabs,madis`, Applies the SynopticLabs and MADIS test suites.

## Request Response

**JSON Format**

The Latest service will return its results in a single organized and self describing JSON object. At a minimum, every request will return a JSON object with a `"SUMMARY"` field.

An example request response in JSON would be:

```
{
  UNITS: {
    solar_radiation: "W/m**2"
  },
  QC_SUMMARY: {
    QC_TESTS_APPLIED: ["sl_range_check"],
    TOTAL_OBSERVATIONS_FLAGGED: 0,
    PERCENT_OF_TOTAL_OBSERVATIONS_FLAGGED: 0
  },
  STATION: [{
    STATUS: "ACTIVE",
    MNET_ID: "153",
    PERIOD_OF_RECORD: {
        start: "1997-01-01T00:00:00Z",
        end: "2017-08-21T05:33:00-0600"
    },
    ELEVATION: "4806",
    NAME: "U of U William Browning Building",
    QC_FLAGGED: false,
    STID: "WBB",
    SENSOR_VARIABLES: {
        ...
    },
    ELEV_DEM: "4738",
    LONGITUDE: "-111.84755",
    STATE: "UT",
    SENSOR_VARIABLES: {
        date_time: { date_time: { } },
        air_temp: {
            air_temp_set_1: {
                position: ""
            }
        }
    },
    OBSERVATIONS: {
      "solar_radiation_value_1": {
        "date_time": "2017-08-21T05:33:00-0600",
        "value": 42
      }
    },
  }],
  SUMMARY: {
    RESPONSE_CODE: 1,
    RESPONSE_MESSAGE: "OK",
    RESPONSE_TIME: "12.0379924774 ms",
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
  * `OBSERVATIONS[]`, contains all the observational data.
  * `QC[]`, contains all the data attributes.
  * `QC_FLAGGED`, boolean value indicating data check attributes are returned (if requested).

* `QC_SUMMARY{}`

  * `QC_TESTS_APPLIED[]`, a list of data checks that were applied to the data.
  * `TOTAL_OBSERVATIONS_FLAGGED`, number of observations that have additional data check attributes.
  * `PERCENT_OF_TOTAL_OBSERVATIONS_FLAGGED`, floating point number indicating the percentage of the observations that have additional data check attributes.

<!-- References & URLs -->

[station-selectors]: ./station-selectors
[network-api]: ./networks
[epoch-seconds]: https://en.wikipedia.org/wiki/Unix_time
[iso-8601]: https://en.wikipedia.org/wiki/ISO_8601
[json]: http://json.org/
[sl-range-check]: https://synopticlabs.org/api/mesonet/reference/qc/#Range_check
[strftime]: http://man7.org/linux/man-pages/man3/strftime.3.html
