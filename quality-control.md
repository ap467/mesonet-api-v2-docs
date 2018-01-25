---
title: SynopticLabs Data Checks
permalink: /v2/quality-control/
layout: refdoc
tagline: Seamless integration of data and data checks for the `/timeseries`, `/latest` and `/nearesttime` api services
---

| Data Check                                                                              | API name                       | Description                                                                                                                                                                               |
| --------------------------------------------------------------------------------------- | ------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Range check](#Range_check)                                                             | `sl_range_check`               | Identifies physically implausible values. For example, an air temperature of 150 degrees or -80 degree Fahrenheit would be flagged.                                                       |
| [Anemometer wind speed vs. wind gust check](#Anemometer_wind_speed_vs._wind_gust_check) | `sl_winddir_windspd_check`     | Determines if the wind speed is greater than the wind gust.                                                                                                                               |
| [Time rate of change test](#Time_rate_of_change_test)                                   | `sl_rate_check`                | Identifies if the absolute difference in two successive values is greater than the maximum plausible rate of change expected for the observed time interval.                              |
| [Persistence check](#Persistence_check)                                                 | `sl_pers_check`                | Identifies if a sequence of observations appear unchanging. For example, an air temperature sensor reporting the same value every 15 minutes for 24 hours would be labeled as persistent. |
| UU2DVAR rejection check                                                                 | `sl_uu2dvar_rejection`         | Spatial analysis check.                                                                                                                                                                   |
| Linear regression check                                                                 | `sl_multvariate_lin_reg_check` | Spatial analysis check.                                                                                                                                                                   |

## Introduction

A small set of highly efficient data checks are available for archived data as well as the provisional data continuously being received. Additional data checks are being developed and will be implemented after further testing. The `/timeseries`, `/latest` and `/nearesttime` api services provide these checks as additional attributes delivered along with the data.

All data accessible via the api services must be considered provisional. Official archives for some of the data are available elsewhere. There are many potential causes of erroneous data: sensor failure, transmission errors, or incorrect conversion of the transmitted data arising from invalid metadata. No matter what may have caused erroneous data, the data checks are intended to identify: (1) physically implausible values and (2) values that may not be representative of the conditions prevailing at that time.

_Note:_ Nearly all users will rely on the `/timeseries`, `/latest` and `/nearesttime` api services to identify the values that fail relevant data checks. However, those services rely on code to access the archived data checks that is also available to users via the QC segment api service [`/qcsegments`](https://synopticlabs.org/api/mesonet/reference/#qcsegments). For efficient storage and short access times, only the start and end times are stored for each period (referred to as a QC segment) for which any values have failed a data check. For example, if a parameter's data value falls outside the realistic range for that variable type at one time with realistic values preceding and following, then the QC segment will only be for that single time. However, if the sensor is consistently reporting unrealistic values over many days, then only the beginning and ending times of that segment are stored, thereby reducing the time required for the `/timeseries`, `/latest` and `/nearesttime` services to obtain that information. Further, if the value fails the range check, then any open qc segments for that variable are closed and a range check segment is opened since it is then not necessary to continue to apply the other tests.

## Overview of the SynopticLabs data checks

Technical descriptions of the SynopticLabs data checks.

[]()

### Range check

> Is the value physically plausible?

**API Name**: `sl_range_check`

Physically implausible values are identified using the SynopticLabs range check](<https://synopticlabs.org/api/mesonet/reference/qc/#Range_check>). Only if a value passes this check will the remaining checks be applied.

There are situations where physically implausible values may not indicate sensor failure. For example, negative snow depth is considered physically implausible but may result from the height of the sensor above ground changing over the winter season as snow compresses the grass or other underlying obstructions. Further, the maximum relative humidity with respect to water is defined to be 100%, although many research-grade sensors commonly report values in excess of that limit.

| Variable                            | Unit           | Minimum  | Maximum     |
| ----------------------------------- | -------------- | -------- | ----------- |
| Altimeter                           | Pascals        | 84998.31 | 108025.75   |
| Pressure                            | Pascals        | 60000.0  | 108000.0    |
| Temperature                         | Celsius        | -59.44   | 57.22       |
| Dew Point                           | Celsius        | -59.44   | 35.0        |
| Relative Humidity                   | %              | 0.00     | 100.00      |
| Wind Speed                          | m/s            | 0.00     | 200.00      |
| Wind Direction                      | Degrees        | 0.00     | 360.00      |
| Wind Gust                           | m/s            | 0.00     | 200.00      |
| Solar Radiation                     | W/m\*\*2       | 0.00     | 1500.00     |
| Soil Temperature                    | Celsius        | -58.00   | 185.00      |
| Sea_level pressure                  | Pascals        | 850.00   | 1080.00     |
| Hours of sun                        | Hours          | 0.00     | 24.00       |
| Water Temperature                   | Celsius        | 0.00     | 135.00      |
| Weather conditions                  | code           | 0.00     | 512000.00   |
| Cloud_layer_3 height/coverage       | code           | 0.00     | 8009.00     |
| Low_cloud symbol                    | code           | 0.00     | 9.00        |
| Mid_cloud symbol                    | code           | 10.00    | 19.00       |
| High_cloud symbol                   | code           | 20.00    | 29.00       |
| Pressure Tendency                   | code           | 0.00     | 8999.00     |
| Road sensor number                  |                | 1.00     | 10.00       |
| Road Temperature                    | Celsius        | -40.00   | 150.00      |
| Road_Freezing Temperature           | Celsius        | 0.00     | 40.00       |
| Road_Surface Conditions             | code           | 1.00     | 100.00      |
| Cloud_layer_1 height/coverage       | code           | 0.00     | 18009.00    |
| Cloud_layer_2 height/coverage       | code           | 0.00     | 8009.00     |
| Visibility                          | Statute miles  | 0.00     | 200.00      |
| Sonic_Wind Direction                | Degrees        | 0.00     | 360.00      |
| Remarks                             | text           | NULL     | NULL        |
| Raw observation                     | text           | NULL     | NULL        |
| 6 Hr High Temperature               | Celsius        | -75.00   | 135.00      |
| 6 Hr Low Temperature                | Celsius        | -75.00   | 135.00      |
| Peak_Wind Speed                     | m/s            | 0.00     | 200.00      |
| Fuel Temperature                    | Celsius        | -40.00   | 140.00      |
| Fuel Moisture                       | gm             | 0.00     | 100.00      |
| Ceiling                             | Meters         | 0.00     | 40000.00    |
| Sonic_Wind Speed                    | m/s            | 0.00     | 200.00      |
| Pressure change                     | code           | 0.00     | 2000.00     |
| IR_Soil Temperature                 | Celsius        | -75.00   | 135.00      |
| Temperature in_case                 | Celsius        | -75.00   | 135.00      |
| Soil Moisture                       | %              | 0.00     | 100.00      |
| Battery voltage                     | volts          | 0.00     | 50.00       |
| Data Insert Date/Time               | minutes        | 0.00     | 16000000.00 |
| Data Update Date/Time               | minutes        | 0.00     | 16000000.00 |
| Road Subsurface Temperature         | Celsius        | -40.00   | 150.00      |
| Water Temperature                   | Celsius        | 0.00     | 135.00      |
| Evapotranspiration                  | Millimeters    | 0.00     | 5.00        |
| 24 Hr High Temperature              | Celsius        | -75.00   | 135.00      |
| 24 Hr Low Temperature               | Celsius        | -75.00   | 135.00      |
| Peak_Wind Direction                 | Degrees        | 0.00     | 360.00      |
| Net Radiation                       | W/m\*\*2       | -500.00  | 1000.00     |
| Soil Moisture tension               | centibars      | 0.00     | 300.00      |
| 1500 m Pressure                     | Pascals        | 700.00   | 1000.00     |
| Wet bulb temperature                | Celsius        | -75.00   | 135.00      |
| Air_Temperature at_2_meters         | Celsius        | -75.00   | 135.00      |
| Air_Temperature at_10_meters        | Celsius        | -75.00   | 135.00      |
| Pressure                            | Pascals        | 600.00   | 1080.00     |
| Temperature                         | Celsius        | -75.00   | 135.00      |
| Relative Humidity                   | %              | 0.00     | 100.00      |
| Wind Speed                          | m/s            | 0.00     | 200.00      |
| Wind Direction                      | Degrees        | 0.00     | 360.00      |
| Wind Gust                           | m/s            | 0.00     | 250.00      |
| Latitude                            | Degrees        | -90.00   | 90.00       |
| Longitude                           | Degrees        | -180.00  | 180.00      |
| Elevation                           | Meters         | -300.00  | 30000.00    |
| Platform True_Direction             | Degrees        | 0.00     | 360.00      |
| Primary_Swell Wave_Direction        | Degrees        | 0.00     | 360.00      |
| Primary_Swell Wave_Period           | Seconds        | 0.00     | 99.00       |
| Primary_Swell Wave_Height           | Meters         | 0.00     | 33.63       |
| Secondary_Swell Wave_Direction      | Degrees        | 0.00     | 360.00      |
| Secondary_Swell Wave_Period         | Seconds        | 0.00     | 99.00       |
| Secondary_Swell Wave_Height         | Meters         | 0.00     | 33.63       |
| Tide Indicator                      | code           | 0.00     | 10.00       |
| Tide Departure                      | Meters         | 0.00     | 100.00      |
| Surface Temperature                 | Celsius        | -58.00   | 185.00      |
| Platform True_Speed                 | m/s            | 0.00     | 125.00      |
| Wave Period                         | Seconds        | 0.00     | 99.00       |
| Wave Height                         | Meters         | 0.00     | 33.63       |
| Net Shortwave Radiation             | W/m\*\*2       | -500.00  | 1000.00     |
| Net Longwave Radiation              | W/m\*\*2       | -500.00  | 1000.00     |
| Sonic Temperature                   | Celsius        | -75.00   | 135.00      |
| Vertical_Velocity                   | m/s            | -2.00    | 2.00        |
| Zonal_Wind Standard_Deviation       | m/s            | 0.00     | 5.00        |
| Meridional_Wind Standard_Deviation  | m/s            | 0.00     | 5.00        |
| Vertical_Wind Standard_Deviation    | m/s            | 0.00     | 5.00        |
| Temperature Standard_Deviation      | Centigrade     | 0.00     | 5.00        |
| Vertical Heat_Flux                  | m/s C          | -2.00    | 2.00        |
| Friction Velocity                   | m/s            | 0.00     | 5.00        |
| SIGW/USTR                           | nondimensional | 0.00     | 5.00        |
| Sonic_Obs Total                     | nondimensional | 0.00     | 5000.00     |
| Sonic Warnings                      | nondimensional | 0.00     | 5000.00     |
| Moisture Standard_Deviation         | g/m\*\*3       | 0.00     | 5.00        |
| Vertical Moisture_Flux              | m/s g/m\*\*3   | -1.00    | 1.00        |
| Dew Point                           | Celsius        | -75.00   | 135.00      |
| Virtual Temperature                 | Celsius        | -75.00   | 135.00      |
| Geopotential Height                 | Meters         | -300.00  | 30000.00    |
| Outgoing Shortwave Radiation        | W/m\*\*2       | 0.00     | 1000.00     |
| Clear Sky Solar Radiation           | W/m\*\*2       | 0.00     | 1500.00     |
| Grip 2 Level of Grip                |                | 0.00     | 1.00        |
| Grip 1 Ice Friction Code            |                | 0.00     | 1.00        |
| Photosynthetically Active Radiation | umol/m\*\*2 s  | 0.00     | 2500.00     |
| PM_2.5 Concentration                | ug/m3          | 0.00     | 200.00      |
| Flow Rate                           | liters/min     | 0.00     | 100.00      |
| Internal Relative_Humidity          | %              | 0.00     | 100.00      |
| Air Flow Temperature                | Celsius        | -75.00   | 135.00      |
| Ozone Concentration                 | ppb            | 0.00     | 500.00      |
| Stream flow                         | ft3/s          | NULL     | NULL        |
| Gage height                         | ft             | NULL     | NULL        |
| Black Carbon Concentration          | ug/m3          | 0.00     | 200.00      |
| Soil Temperature 2                  | Celsius        | -58.00   | 185.00      |
| 18_Inch Soil_Temperature            | Celsius        | -58.00   | 185.00      |
| 18_Inch Soil_Temperature2           | Celsius        | -58.00   | 185.00      |
| 20_Inch Soil_Temperature            | Celsius        | -58.00   | 185.00      |
| Soil Temperature 3                  | Celsius        | -58.00   | 185.00      |
| Soil Temperature 4                  | Celsius        | -58.00   | 185.00      |
| Soil Moisture 2                     | %              | 0.00     | 100.00      |
| Particulate Concentration           | ug/m3          | 0.00     | 10000.00    |
| Filter Percentage                   | %              | 0.00     | 100.00      |
| Sensor Error Code                   | code           | 0.00     | 1000.00     |
| Electric Conductivity               | dS/m           | 0.00     | 10.00       |
| Permittivity                        |                | 0.00     | 100.00      |
| Past weather                        | code           | 0.00     | 9.00        |
| Surface Level                       | Millimeters    | -100.00  | 100.00      |
| Incoming Longwave Radiation         | W/m\*\*2       | 0.00     | 1500.00     |
| Outgoing Longwave Radiation         | W/m\*\*2       | 0.00     | 1500.00     |

[]()

### Anemometer wind speed vs. wind gust check

> Is the wind speed value greater than the wind gust value?

**API Name**: `sl_winddir_windspd_check`

If the reported wind speed is greater than the wind gust, then the wind gust is flagged. Note: the conventions for reporting wind speed and wind gust differ among different data providers. The wind gust reported may be a maximum wind within the time interval for that observation.

[]()

### Time rate of change test

> Is the change between two values too large?

**API Name**: `sl_rate_check`

For parameters for which it is appropriate to do so, the rate change test compares the absolute difference between two successive observations to the maximum plausible rate of change expected within that time interval.

<table>
  <thead>
    <tr>
      <th>Variable Name</th>
      <th>Unit</th>
      <th colspan="9">Change per Minute</th>
    </tr>
      <th colspan="2"></th><th>1</th><th>2</th><th>3</th><th>4</th>
      <th>5</th><th>10</th><th>15</th><th>30</th><th>60</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Pressure</td>
      <td>Pascals</th>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td>925</td>
      <td>1000</td>
      <td>1500</td>
      <td>1500</td>
      <td>1500</td>

    </tr>
    <tr>
      <td>Temperature</td>
      <td>Celsius</th>
      <td>3</td>
      <td>3</td>
      <td>3</td>
      <td>4</td>
      <td>5</td>
      <td>7.5</td>
      <td>15</td>
      <td>20</td>
      <td></td>
    </tr>

  </tbody>
</table>

### Persistence check

> Do the values appear to be unchanging?

**API Name**: `sl_pers_check`

For parameters for which it is appropriate to do so, the persistence check compares the range of values (difference between the maximum and minimum) within a specified time period relative to the minimum plausible range expected for that parameter.

The following table lists the variable, time period evaluated, and the minimum plausible range expected for that variable..

| Variable Name                       | GEMPAK | unit          | Evaluation period | Min number of obs | Required Change |
| ----------------------------------- | ------ | ------------- | ----------------- | ----------------- | --------------- |
| Altimeter                           | ALTI   | Pascals       | 24 hrs            | 24                | 5.0             |
| Pressure                            | PRES   | Pascals       | 24 hrs            | 24                | 5.0             |
| Temperature                         | TMPF   | Celsius       | 24 hrs            | 24                | 0.1             |
| Dew Point                           | DWPF   | Celsius       | 24 hrs            | 24                | 0.1             |
| Relative Humidity                   | RELH   | %             | 24 hrs            | 24                | 0.5             |
| Wind Speed                          | SKNT   | m/s           | 24 hrs            | 24                | 0.25            |
| Wind Direction                      | DRCT   | Degrees       | 24 hrs            | 24                | 2.5             |
| Wind Gust                           | GUST   | m/s           | 24 hrs            | 24                | 0.25            |
| Solar Radiation                     | SOLR   | W/m\*\*2      | 48 hrs            | 48                | 0.5             |
| Soil Temperature                    | TSOI   | Celsius       | 48 hrs            | 48                | 0.05            |
| Sea_level pressure                  | PMSL   | Pascals       | 24 hrs            | 24                | 5.0             |
| Water Temperature                   | TLKE   | Celsius       | 48 hrs            | 48                | 0.05            |
| Road Temperature                    | TRD    | Celsius       | 24 hrs            | 24                | 0.1             |
| Sonic_Wind Direction                | DIRS   | Degrees       | 24 hrs            | 24                | 1.0             |
| Peak_Wind Speed                     | PEAK   | m/s           | 24 hrs            | 24                | 0.25            |
| Fuel Temperature                    | FT     | Celsius       | 72 hrs            | 72                | 0.1             |
| Fuel Moisture                       | FM     | gm            | 24 hrs            | 24                | 0.1             |
| Sonic_Wind Speed                    | SPDS   | m/s           | 24 hrs            | 24                | 0.1             |
| IR_Soil Temperature                 | TIR    | Celsius       | 24 hrs            | 24                | 0.1             |
| Road Subsurface Temperature         | TSRD   | Celsius       | 48 hrs            | 48                | 0.05            |
| Water Temperature                   | TLRW   | Celsius       | 48 hrs            | 48                | 0.05            |
| Peak_Wind Direction                 | PDIR   | Degrees       | 24 hrs            | 24                | 2.5             |
| Net Radiation                       | NETR   | W/m\*\*2      | 48 hrs            | 48                | 0.5             |
| Air_Temperature at_2_meters         | T2M    | Celsius       | 24 hrs            | 24                | 0.1             |
| Air_Temperature at_10_meters        | T10M   | Celsius       | 24 hrs            | 24                | 0.1             |
| Pressure                            | MPRS   | Pascals       | 24 hrs            | 24                | 5.0             |
| Temperature                         | MTMP   | Celsius       | 24 hrs            | 24                | 0.1             |
| Relative Humidity                   | MRH    | %             | 24 hrs            | 24                | 0.5             |
| Wind Speed                          | MSKT   | m/s           | 24 hrs            | 24                | 0.25            |
| Wind Direction                      | MDIR   | Degrees       | 24 hrs            | 24                | 2.5             |
| Wind Gust                           | MGST   | m/s           | 24 hrs            | 24                | 2.5             |
| Surface Temperature                 | TGND   | Celsius       | 24 hrs            | 24                | 0.1             |
| Net Shortwave Radiation             | NETS   | W/m\*\*2      | 48 hrs            | 48                | 0.5             |
| Net Longwave Radiation              | NETL   | W/m\*\*2      | 48 hrs            | 48                | 0.5             |
| Sonic Temperature                   | TSNC   | Celsius       | 24 hrs            | 24                | 0.1             |
| Dew Point                           | MDWP   | Celsius       | 24 hrs            | 24                | 0.1             |
| Virtual Temperature                 | MTVR   | Celsius       | 24 hrs            | 24                | 0.1             |
| Outgoing Shortwave Radiation        | OUTS   | W/m\*\*2      | 48 hrs            | 48                | 0.5             |
| Photosynthetically Active Radiation | SPAR   | umol/m\*\*2 s | 48 hrs            | 48                | 0.5             |
| PM_2.5 Concentration                | PM25   | ug/m3         | 24 hrs            | 24                | 0.5             |
| Ozone Concentration                 | OZNE   | ppb           | 24 hrs            | 24                | 0.5             |
| Black Carbon Concentration          | BLKC   | ug/m3         | 24 hrs            | 24                | 0.5             |
| Particulate Concentration           | PMCN   | ug/m3         | 24 hrs            | 24                | 0.5             |
