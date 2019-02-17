+++ 
draft = true
date = 2019-02-16T07:09:58+01:00
title = "Evaluating options for a bi-temportal time series database"
description = ""
slug = "" 
tags = []
categories = []
externalLink = ""
+++

Time series databases are on the rise!

![Time series popularity past 24 months](https://lh6.googleusercontent.com/3ilNt7AbjM0ahQd10tFn41s8ux_9rRIiDtVOUSUCKPqcvwc-5RZfKhA7SVD6RRlXVc2eMjncTtYliL1wIXHy1LNBN0HsDM4kSCHjbDXvvbgl7inygKEazHrf68gsEx-2eSzmqLXT)
<center>*From [DB-engines](https://db-engines.com/en/ranking_categories)*</center>



From the [ranked databases at DB-engines](https://db-engines.com/en/ranking/time+series+dbms), you find

From what I see, most of this is driven by logging of service metrics, IoT and other "real time" analytics applications. This means that values are usually only appended, and the HA/DR requirements aren't that high.

In the industry that I work, utilities, we have a bit of a different need from a timeseries database.

Write at any point in time back in time.

## Storage engines

When looking and TSDBs it is quite interesting to look at the underlying storage engines.

### TimescaleDB

[Timescale](https://www.timescale.com/learn) is engineered up from PostgreSQL, packaged as an extension. This basically means that under the hood, there is a Postgres database that is tweeked, adjustend and modified to support large amounts of timeseries data.

Has [three licences](https://www.timescale.com/pricing), but you get just about all you need (except support) from the free ones.

### InfluxDB

[InfluxDB](https://www.influxdata.com/) has its own storage engine based on what they call a [Time-Structured Merge Tree (TSM)](https://docs.influxdata.com/influxdb/v1.7/concepts/storage_engine/) and pair this with a [Time Series Index (TSI)](https://docs.influxdata.com/influxdb/v1.7/concepts/time-series-index/).

Influx is open source, but once you start talking about HA and DR, then you move to the enterprise version.

### kdb+

[Kdb+](https://code.kx.com/q/learn/) commecial licence only, so who knows!

### Prometheus
open-source monitoring solution. [Custom storage format](https://github.com/prometheus/tsdb/blob/master/docs/format/README.md).



### OpenTSDB

[http://opentsdb.net/overview.html](http://opentsdb.net/) stores data using [HBase](https://en.wikipedia.org/wiki/Apache_HBase)

### KairosDB
[KairosDB](http://kairosdb.github.io/) stores data using [Cassandra](http://cassandra.apache.org/)

### RRDtool

[RRDtool](https://oss.oetiker.ch/rrdtool/index.en.html) data logging and graphing system for time series data.