---
title: Mesonet API Documentation Home
permalink: /v2/
layout: default
---

Gain access to over 60,000 surface weather stations in the United States and Canada. The SynopticLabs (SL) Mesonet Service is an easy to use REST API that can easily leverage real time and historical data in nearly any application.

If you are new to the Mesonet APIs, please take a second to read our [getting started section][getting-started].

## Available Web Services

### Data services

* [Time Series Web Service][timeseries-api]
* [Latest Web Service][latest-api]
* [Nearest Time Web Service][nearesttime-api]
* [Precipitation Web Service][precipitation-api]
  <!-- * Climatology Web Service -->
* [QC Segments Web Service][qcsegments-api]

### Metadata services

* [Metadata Web Service][metadata-api]
* [QC Types Web Service][qc-types-api]
* [Variables Web Service][variables-api]
* [Networks Web Service][networks-api]
* [Network Types Web Service][networktypes-api]

### Analytics & user services

* [Authentication Web Service][authentication-api]
* [Latency Web Service][latency-api]

## A little bit of context

<!-- Reword this when we get to Alerts -->

All of the APIs are [REST APIs][rest-api], meaning that each request is stateless and can be made via a simple HTTP request. The base of all the APIs is `https://api.synopticdata.com/v2/` and all requests require an API token. It's easy to get an API key and get going. We have a quick start guide here to get you up and running in just a few minutes.

Acquiring data from REST APIs requires certain parameters. When encoding URLs, all parameters are separated using the ampersand (&) character and their value is indicated by an equal sign (=). Below is a list of accepted parameters.

All of the SynopticLabs APIs also support JSONP callbacks. Just add the `callback` parameter to any request.

You can read more in our [Getting Started][getting-started] guide.

<!-- Refs and Such -->

[getting-started]: ./getting-started
[rest-api]: https://en.wikipedia.org/wiki/Representational_state_transfer
[timeseries-api]: ./timeseries
[latest-api]: ./latest
[getting-started]: ./getting-started
[authentication-api]: ./auth
[metadata-api]: ./metadata
[nearesttime-api]: ./nearesttime
[precipitation-api]: ./precipitation
[latency-api]: ./latency
[qc-types-api]: ./qctypes
[variables-api]: ./variables
[networks-api]: ./networks
[networktypes-api]: ./networktypes
[qcsegments-api]: ./qcsegments
[mesowest]: https://mesowest.utah.edu
[uutah]: https://www.utah.edu
[synoptic]: https://synopticlabs.org
