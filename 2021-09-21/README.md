# Ingesting Cloudwatch Metrics into Prometheus using YACE

### Target outcomes

* Get Cloudwatch Metrics into Prometheus and ideally alarms into AlertManager.
* Support Cloudwatch Alarms being routed to Pagerduty alerts via our current use of AlertManager
* Ingest Infrastructure metrics alongside application & other metrics (eg, Cloudflare)

### The approach

Use [YACE](https://github.com/nerdswords/yet-another-cloudwatch-exporter) to ingest content from Cloudwatch.

### Why

* Written in Go and open source - aligned with chosen languages and working approach strategy
* Actively seeking contributions - [https://medium.com/@IT_Supertramp/reorganizing-yace-79d7149b9584](https://medium.com/@IT_Supertramp/reorganizing-yace-79d7149b9584)
* Apparently more API-efficient than the ‘official' Prometheus exporter for Cloudwatch [https://sysdig.com/blog/improving-prometheus-cloudwatch-exporter/](https://sysdig.com/blog/improving-prometheus-cloudwatch-exporter/)


 ### What

* Spin up local Dockerised Prom & Alert Manager environment
* Spin up YACE alongside
* Connect and ingest
* Configure AlertManager to easy destination (email, ‘hello world’, pastebin, whatevers)
* See if alarms can be picked up
* If not, see if metrics can be picked up and then treated as alertable events in standard Prometheus fashion


## Getting started
These instructions will get you a copy of the project up and running on your local machine for development and testing purposes. See the deployment section for notes on how to deploy the project on a live system.



## Installing

* Ensure you have `docker` and `docker-compose` available.

# Running the tests

To bring up the cluster:
```
make up
```

Then, browse to:
* Prometheus: http://localhost:9090
* Grafana: http://localhost:3000. admin/admin is the username/password if prompted.
* Alertmanager: http://localhost:9093/
* Postgres (TimescaleDB): `postgres://postgres:password@db:5432/postgres?sslmode=allow`


To view logs in realtime:

```
make logs
```

If you've made changes to configuration files, you can type `make restart` to cycle the services.


Finally, to tear it all down:
```
make down
```

# Deployment

This environment is purely intended for local development and testing.

## Notes

* `/volumes/` contains any persistent files mounted into containers to keep
* `make shell` will launch a shell connecting to `psql`

# Contributing

Not applicable

# Contact details

Not applicable

# FAQs

_Why is this so badly implemented?_

This is a Lab Day exercise ; corners are cut, decisions are fast, and new tools are tried rather than learned. Don't @ me!

# Change log

