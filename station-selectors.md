---
title: Station Selectors
permalink: /v2/station-selectors/
layout: refdoc
tagline: Ways to filter and discover available weather stations
---

<!-- The following parameters can be used in the following APIs: -->

<!-- * [Time Series API][timeseries-api]
* [Latest API](../latest/)
* [Nearest Time API](../nearesttime/)
* [Precipitation API](../precipitation/)
* [Metadata API](../metadata/)
* [Latency API](../latency/)
* [Climatology API](../climatology/)
* [QC Segments API](../qcsegments/) -->

**Station Selectors**

These selectors individually or combined to target the desired data.

* `stid` (string), Single or comma separated list of SynopticLabs station IDs. Example: `stid=mtmet,kslc,fps`.
* `state` (string), Single or comma separated list of abbreviated 2 character states. If country is not included, default is United States. Example: `state=ut,wy,dc`.
* `country` (string), Single or comma separated list of abbreviated 2 or 3 character countries. Example: `country=us,ca,mx`.
* `nwszone` (string), Single or comma separated list of National Weather Service Zones. Example: `nwszone=UT003,CA041`.
* `nwsfirezone` (string), Single or comma separated list of National Weather Service Fire Zones. Example: `nwsfirezone=LOX241`
* `cwa` (string), Single or comma separated list of National Weather Service County Warning Areas. Example: `cwa=LOX`.
* `gacc` (string), Single or comma separated list of Geographic Area Coordination Centers. Example: `gacc=GB`.
* `subgacc` (string), Single or comma separated list of Sub Geographic Area Coordination Centers. Example: `subgacc=EB07`.
* `county` (string), Single or comma separated list of counties. Use the `state` parameter to filter by state in the case of duplicate county names (i.e. "King"). Example: `county=king&state=wa`.
* `vars` (string), Single or comma separated list of sensor variables found here. The request will return all stations matching at least one of the variables provided. This is useful for filtering all stations that sense only certain variables, such as wind speed, or pressure. Do not specify vars twice in a query string. Example: `vars=wind_speed,pressure`.
* `network` (number, string), Single or comma separated list of network IDs or short names. The ID can be found be using the [Networks API][network-api]. Example: `network=153` or `network=uunet,raws`
* `radius` (string), A comma separated list of three values of the type `[latitude,longitude,miles]` or `[stn_id,miles]`. Coordinates are in decimal degrees. Returns all stations within radius of the point (or station, given by the station ID) and provides the distance of the station from given location by 'Distance' with units of miles. Adding `limit=n` to the query will limit the number of returned stations to **n** stations. Some examples are: `radius=41.5,-120.25,20`, `radius=wbb,10`, `radius=41.5,-120.25,20&limit=10`.
* `bbox` (string), A bounding box defined by the lower left and upper right corners in decimal degrees latitude and longitude coordinates, in the form of `[lonmin,latmin,lonmax,latmax]`. Recall that for regions involving the western and southern hemispheres that the coordinates are negative values (e.g., 120 W is -120, 20 S is -20). Example: `bbox=-120,40,-119,41`.
* `status` (string), A value of either active or inactive returns only stations that are currently set as active in the. archive. By default, omitting this parameter will return all stations. Example: `status=active`.
* `groupby` (string), Requests can be counted by grouping using one of the following key words: `state`, `country`, `county`, `cwa`, `nwszone`, `nwsfirezone`, `gacc`, `subgacc`. Example: `groupby=state`.
* `complete` (1, 0), A value of 1 or 0. When set to 1 an extended list of metadata attributes for each returned station is provided. This result is useful for exploring the zones and regions in which a station resides. Default is 0. Example: `complete=1`.

<!-- Refs and Such -->

[timeseries-api]: ./timeseries/
[network-api]: ./networks/
