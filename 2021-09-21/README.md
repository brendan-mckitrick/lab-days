# Ingesting Cloudwatch Metrics into Prometheus using YACE

## Target outcomes

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
* `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY` and (optionally) `AWS_SESSION_TOKEN` set and exported in your environment
  ** eg, `export env AWS_ACCESS_KEY_ID=$(aws configure get aws_access_key_id) AWS_SECRET_ACCESS_KEY=$(aws configure get aws_secret_access_key) AWS_SESSION_TOKEN=$(aws configure get aws_session_token)`

## Running the tests

To bring up the cluster:
```
make up
```

Then, browse to:
* Prometheus: http://localhost:9090
* Grafana: http://localhost:3000. admin/admin is the username/password if prompted.
* Alertmanager: http://localhost:9093/


To view logs in realtime:

```
make logs
```

If you've made changes to configuration files, you can type `make restart` to cycle the services.


Log in to Prometheus - http://localhost:9090 - and run some queries to see the data:

```
# count healthy hosts group by Load Balancer Name
count by (dimension_LoadBalancerName) (aws_elb_healthy_host_count_minimum)

# count healthy hosts grouped by AZ
count by (dimension_AvailabilityZone) (aws_elb_healthy_host_count_minimum)



```



Finally, to tear it all down:
```
make down
```

# Deployment

This environment is purely intended for local development and testing.

## Notes

* `/volumes/` contains any persistent files mounted into containers to keep
* It's not apparent (or I didn't RTFM properly) but there is a relationship between the `scraping-interval` setting and the `length` option for [Auto-discovery jobs](https://github.com/nerdswords/yet-another-cloudwatch-exporter#auto-discovery-job) ; if `length` of these jobs is greater than `scraping-interval`, then the [former will be preferred](https://github.com/nerdswords/yet-another-cloudwatch-exporter/blob/0958dd880d9fae2f6367fa18382b616eafa38c19/cmd/yace/main.go#L90)
* Custom metrics (eg, CWAgent) are not supported for auto-discovery ; these can be configured as `static` entries. That said, you _must_ supply all required dimensions when rrequesting these - this is how Cloudwatch works - same for API as for Console. This means hard-coding in things like `InstanceID` values which isn't ideal, but you could solve this by rendering a `config.yml` file from a dynamic source. [There is an open issue](https://github.com/nerdswords/yet-another-cloudwatch-exporter/issues/325) to look at adding discovery functionality for custom metrics.

# Contributing

Not applicable

# Contact details

Not applicable

# FAQs

_Why is this so badly implemented?_

This is a Lab Day exercise ; corners are cut, decisions are fast, and new tools are tried rather than learned. Don't @ me!

# Change log

