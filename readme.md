---
title: Mesonet API Web Services
permalink: /v2/
layout: default
---

Gain access to over 60,000 surface weather stations mostly in North America. The Mesonet API is an easy to use REST API that supports real-time and historical data.

If you are new to the Mesonet API please take a second to read our [getting started section][getting-started].

## Available Web Services

---

### Data

* [Time Series][timeseries-service]
* [Latest][latest-service]
* [Nearest Time][nearesttime-service]
* [Precipitation][precipitation-service]
* [QC Segments][qcsegments-service]

### Metadata

* [Metadata][metadata-service]
* [QC Types][qc-types-service]
* [Variables][variables-service]
* [Networks][networks-service]
* [Network Types][networktypes-service]

### Analytics & user

* [Authentication][authentication-service]
* [Latency][latency-service]

## A little bit of context

<!-- Reword this when we get to Alerts -->

All of the web services are [REST APIs][rest-service], meaning that each request is stateless and can be made via a simple HTTP request. The base url of all the Mesonet services is `https://api.synopticdata.com/v2/` and all requests require an API token. It's easy to get an API key and get going. We have a quick start guide here to get you up and running in just a few minutes [here][getting-started]

The Mesonet API also support JSONP callbacks. Just add the `callback` parameter to any request.

You can read more in our [Getting Started][getting-started] guide.

<!-- Refs and Such -->

[getting-started]: ./getting-started/
[rest-service]: https://en.wikipedia.org/wiki/Representational_state_transfer
[timeseries-service]: ./timeseries/
[latest-service]: ./latest/
[getting-started]: ./getting-started/
[authentication-service]: ./auth/
[metadata-service]: ./metadata/
[nearesttime-service]: ./nearesttime/
[precipitation-service]: ./precipitation/
[latency-service]: ./latency/
[qc-types-service]: ./qctypes/
[variables-service]: ./variables/
[networks-service]: ./networks/
[networktypes-service]: ./networktypes/
[qcsegments-service]: ./qcsegments/
[mesowest]: https://mesowest.utah.edu
[uutah]: https://www.utah.edu
[synoptic]: https://synopticlabs.org
