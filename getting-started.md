---
title: Getting Started with the Mesonet Web Services
permalink: /mesonet/v2/getting-started/
layout: refdoc
tagline: How to get started
---

## Getting Set up

All of the Mesonet web services use JSON as the default response format. JSON ([JavaScript Object Notation][json]) is the defacto standard of moving data across the web. There are many tools you can use to inspect JSON data, or you can just view the response right in your web browser, but we find it's helpful to install a JSON viewing utility. Some common JSON viewers are:

* [JSON Viewer for Google Chrome][json-viewer]
* [JSON Formatter for Microsoft Edge][edge-json]
* FireFox already has one built in

### Gaining access

When you [sign up to use the Mesonet API][sign-up-for-service-key], you will receive an API key. If you already have a key, just skip down to **creating a token**.

### Sign up for a API key

If you do not have an SynopticLabs or SynopticData API key you can sign up for on [here][sign-up-for-service-key]. This is a private key that is used to generate tokens for your applications. You are only assigned one key to your account, so you will want to keep this safe. **Never code this key into your applications**.

### Creating a token

Once you have an API key, you'll need to generate a token for your projects. You are allowed to create as many tokens as you'd like. In fact we recommend that each application you create uses a different token. Tokens are what we use to authenticate your account when requesting data. If a token becomes compromised, simply delete it and create a new one. Creating tokens is easy with our our [Authentication API][auth-service]. In your web browser copy the following URL and fill in the `YOUR_API_KEY` with the one you received in your email.

> When encoding URLs all parameters are separated using the ampersand (&) and their value is indicated by an equal sign (=). The first parameter is preceded by a question mark (?)

```
https://api.synopticdata.com/v2/auth?apikey=YOUR_API_KEY
```

**Tip:** To see which tokens are associated with an API key, just add the parameter `list=1` to the request.

At this point you should see a response that looks similar the response below. You will need to copy this token somewhere, as we are going to use it throughout the following examples.

```json
{
  "TOKENS": ["a6a82dddc14a46c892077bded6f5a3d6"]
}
```

## Determining which service to use

When deciding which Mesonet service to use the biggest questions are:

* How much data do you need to make the application work?
* Do you need historical or real-time observations?

The most services are [Time Series][timeseries-service], [Latest][latest-service] and [Nearest Time][nearesttime-service] and [Metadata][metadata-service]. Each fills a certain need and are described below.

* [Time Series][timeseries-service] (our most popular) returns a sequential series of observations for a requested time span.
* [Latest][latest-service] returns the most recent observation.
* [Nearest Time][nearesttime-service] returns the observation nearest to a requested time. Similar to the Latest service but allows for targeting a specific time.
* [Metadata][metadata-service] returns metadata.

## Constructing a request

Mesonet web services are [REST APIs][rest-service], meaning that each request is stateless and only represents the arguments within the request. A request is just a URL that contains the URL of the service being requested, a token and any additional parameters. Constructing a request URL is simple. Often the best tool for experimenting with these services is just using the address bar of a web browser.

> **Note**: For some services such as Latest and Metadata the omission of selection parameters will return data from every available station in the inventory.

The base URL for all services is `https://api.synopticdata.com/v2/`. From this point you can access all the public services. Now just add the service you wish to access. This example will use the Time Series service i.e. `stations/timeseries`. Then add parameters to reduce the data returned to what you care to see.

> **Tip** You can quickly look for stations near either a known station or a lat/lon set. For example: `https://api.synopticdata.com/v2/stations/metadata?token=demotoken&radius=33.704,-112.014,10&limit=10` will return the 10 closest stations to a radius of 10 miles of downtown Phoenix, Arizona.

```
// Make sure you view this on a screen wide enough to not word wrap

                Base URL                   Stations we want returned (comma separated)
                    |                                      |
                    ▼                                      ▼
https://api.synopticdata.com/v2/stations/timeseries?stid=mtmet,nahu&token=YOUR_TOKEN_HERE&recent=15
                                  ▲                  ▲                ▲         ▲
                                  |                  |                |         |
                                  |                  |          token parameter and token
                    'stations/latest' API            |
                                      station ID (stid) parameter
```

## Understanding the response

Every non CSV request to a Mesonet API will yield a response with at a minimum a SUMMARY section and status code. A Time Series request with the following URL would return a result similar to:

```
https://api.synopticdata.com/v2/stations/timeseries?stid=mtmet&start=201701030830&end=201701030930&vars=air_temp,volt&token=YOUR_TOKEN_HERE
```

```json
{
  "UNITS": {
    "position": "ft",
    "air_temp": "Celsius",
    "elevation": "ft",
    "volt": "volts"
  },
  "QC_SUMMARY": {
    "QC_TESTS_APPLIED": ["sl_range_check"],
    "TOTAL_OBSERVATIONS_FLAGGED": 0,
    "PERCENT_OF_TOTAL_OBSERVATIONS_FLAGGED": 0
  },
  "STATION": [
    {
      "STATUS": "ACTIVE",
      "MNET_ID": "153",
      "PERIOD_OF_RECORD": {
        "start": "2012-04-26T00:00:00Z",
        "end": "2018-01-21T21:35:00Z"
      },
      "ELEVATION": "4996",
      "NAME": "U of U Mountain Met Lab",
      "RESTRICTED": false,
      "STID": "MTMET",
      "SENSOR_VARIABLES": {
        "date_time": {
          "date_time": {}
        },
        "volt": {
          "volt_set_4": {},
          "volt_set_3": {},
          "volt_set_2": {},
          "volt_set_1": {
            "position": "0"
          }
        },
        "air_temp": {
          "air_temp_set_1": {
            "position": "8"
          }
        }
      },
      "ELEV_DEM": "4993",
      "LONGITUDE": "-111.828211",
      "STATE": "UT",
      "DATA_AFFECTED": false,
      "QC_FLAGGED": false,
      "LATITUDE": "40.766573",
      "TIMEZONE": "America/Denver",
      "ID": "33898",
      "OBSERVATIONS": {
        "date_time": ["2017-01-03T08:30:00Z", "2017-01-03T08:35:00Z", "2017-01-03T08:40:00Z", "2017-01-03T08:45:00Z"],
        "volt_set_4": [13.5, 13.51, 13.61, 13.61],
        "volt_set_3": [12.26, 12.26, 12.26, 12.26],
        "volt_set_2": [14.4, 14.4, 14.4, 14.4],
        "volt_set_1": [13.54, 13.55, 13.55, 13.55],
        "air_temp_set_1": [-4.82, -4.87, -4.89, -4.89]
      }
    }
  ],
  "SUMMARY": {
    "DATA_QUERY_TIME": "8.29005241394 ms",
    "RESPONSE_CODE": 1,
    "RESPONSE_MESSAGE": "OK",
    "METADATA_RESPONSE_TIME": "0.0929832458496 ms",
    "DATA_PARSING_TIME": "0.491142272949 ms",
    "TOTAL_DATA_TIME": "8.78214836121 ms",
    "NUMBER_OF_OBJECTS": 1,
    "FUNCTION_USED": "time_data_parser"
  }
}
```

Here's an explicit breakdown of the response above.

* `UNITS{}`, key/value pairs of sensor/item type and unit of measure.

* `QC_SUMMARY{}`, contains information about Quality Control attributes returned.

  * `QC_TESTS_APPLIED[]`, list of QC flags by short name.
  * `TOTAL_OBSERVATIONS_FLAGGED` (number), number of observations flagged.
  * `PERCENT_OF_TOTAL_OBSERVATIONS_FLAGGED` (number), percentage of observations flagged.

* `STATION[]`, a list of objects containing information about each station returned.

  * `STATUS` (string), (ACTIVE, INACTIVE) status of station as declared by data provider.
  * `MNET_ID` (string), network ID number of station's assigned network.
  * `PERIOD_OF_RECORD{}`, start and end times of station's activity.

    * `start` (string), timestamp of station's start.
    * `end` (string), timestamp of station's end (current time if still active).

  * `NAME` (string), formal name of station.
  * `RESTRICTED` (boolean), is station under restricted access.
  * `STID` (string), four to six character station ID.
  * `LATITUDE`(string), numerical latitude.
  * `LONGITUDE` (string), numerical longitude.
  * `ELEVATION` (number), station's elevation in feet.
  * `ELEV_DEM` (number), Digital Elevation Model elevation in feet.
  * `STATE` (string), state/province station resides in.
  * `DATA_AFFECTED` (boolean), true if station has moved locations during the request period. Only the later (most recent) location will be returned and previous data will be truncated.
  * `QC_FLAGGED` (boolean), true if any observation in response is QC flagged.
  * `TIMEZONE`: Olson timezone.
  * `ID` (string), numerical ID of station.

* `SENSOR_VARIABLES{}`, contains metadata about the sensors returned.

  * `variable_name{}`, general category of sensor/variable.

    * `variable_instance{}`, object containing sensor/variable information. Takes the form of `sensor_set_instance`, i.e. `air_temp_set_1`.

      * `position`, height of sensor from station's base.

* `OBSERVATIONS{}`, contains observational data.

  * `data_time[]`, (always returned) list of timestamps with a 1:1 relationship to the accompanied observational values.
  * `sensor_set_instance[]`, a list of observations with a temporal relationship to `data_time`.

* `SUMMARY`

  * `NUMBER_OF_OBJECTS`, (always returned) is a integer value of the number of stations returned.
  * `RESPONSE_CODE`, (always returned) is a numerical code indicating the status of the request.

    * "1" = "OK"
    * "2" = "Zero Results"
    * "200" = "Authentication failure"
    * "400" = "Violates a rule of the API"

  * `RESPONSE_MESSAGE`, (always returned) is a string explaining the `RESPONSE_CODE`.

  * `RESPONSE_TIME`, (always returned) server time to process the request.

### CSV users

Currently only the Time Series service supports CSV ([Comma Separated Values][csv]) output. If you would like the CSV output, just add `output=csv` to the URL request. The same request above in CSV format would like like:

```csv
# STATION: MTMET
# STATION NAME: U of U Mountain Met Lab
# LATITUDE: 40.766573
# LONGITUDE: -111.828211
# ELEVATION [ft]: 4996
# STATE: UT
Station_ID,Date_Time,air_temp_set_1,volt_set_1,volt_set_2,volt_set_3,volt_set_4
,,Celsius,volts,volts,volts,volts
MTMET,2017-01-03T08:30:00Z,-4.82,13.54,14.4,12.26,13.5
MTMET,2017-01-03T08:35:00Z,-4.87,13.55,14.4,12.26,13.51
MTMET,2017-01-03T08:40:00Z,-4.89,13.55,14.4,12.26,13.61
MTMET,2017-01-03T08:45:00Z,-4.89,13.55,14.4,12.26,13.61
MTMET,2017-01-03T08:50:00Z,-4.92,13.55,14.4,12.26,13.49
MTMET,2017-01-03T08:55:00Z,-4.93,13.54,14.4,12.26,13.62
MTMET,2017-01-03T09:00:00Z,-4.94,13.55,14.4,12.26,13.51
MTMET,2017-01-03T09:05:00Z,-4.96,13.55,14.4,12.26,13.53
MTMET,2017-01-03T09:10:00Z,-4.97,13.55,14.4,12.26,13.62
MTMET,2017-01-03T09:15:00Z,-4.98,13.55,14.4,12.26,13.53
MTMET,2017-01-03T09:20:00Z,-5.01,13.54,14.4,12.26,13.5
MTMET,2017-01-03T09:25:00Z,-4.99,13.55,14.4,12.26,13.61
MTMET,2017-01-03T09:30:00Z,-4.97,13.55,14.4,12.26,13.51
```

The first six lines are metadata about this station and they are noted by the comment indicator (#). Line seven and eight are the column name and unit of measure respectively.

# Fin

We hope this tutorial has been helpful. If you made it this far, then you've got the basic skills to start exploring the Mesonet services and using real-time and historical data in your applications.

Some further reading:

* [Station Selectors][station-selectors]
* [Time Series Service][timeseries-service]
* [Latest Service][latest-service]

<!-- Refs and such -->

[station-selectors]: ../station-selectors/
[auth-service]: ../authentication/
[csv]: https://en.wikipedia.org/wiki/Comma-separated_values
[edge-json]: https://www.microsoft.com/en-gb/store/p/json-formatter-for-edge/9nz9d2j86w6s
[json]: https://json.org/
[json-viewer]: https://chrome.google.com/webstore/detail/jsonview/chklaanhfefbnpoihckbnefhakgolnmc?utm_source=chrome-ntp-icon
[latest-service]: ../latest/
[metadata-service]: ../metadata/
[nearesttime-service]: ../nearesttime/
[rest-service]: https://en.wikipedia.org/wiki/Representational_state_transfer
[sign-up-for-service-key]: https://synopticlabs.org/api/signup/
[timeseries-service]: ../timeseries/
