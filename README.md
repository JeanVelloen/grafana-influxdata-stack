# Grafana & Influxdata TICK Stack

- [grafana/grafana](https://hub.docker.com/r/grafana/grafana/)
- [telegraf](https://hub.docker.com/_/telegraf/)
- [influxdb](https://hub.docker.com/_/influxdb/)
- [chronograf](https://hub.docker.com/_/chronograf/)
- [kapacitor](https://hub.docker.com/_/kapacitor/)

Grafana is the open platform for beautiful analytics and monitoring, supporting 44 data sources.
InfluxData's Modern Time Series Platform consists of Telegraf, InfluxDB, Chronograf, and Kapacitor; collectively called the TICK Stack.

## Usage

Start all the images as follows:

    # cd into the Grafana, Influxdata project
    cd grafana-influxdata-stack/
    # Start all images in the background
    docker-compose up -d
    Creating network "grafana-influxdata-stack_default" with the default driver
    Creating influxdb ... done
    Creating telegraf ... done
    Creating influxdb-cli ... done
    Creating kapacitor    ... done
    Creating grafana       ... done
    Creating kapacitor-cli ... done
    Creating chronograf    ... done

### Check that InfluxDB works:

InfluxDB is running if the following curl command returns no errors:

    curl http://localhost:8086/ping

#### The `InfluxDB` client

Use the built-in InfluxDB cli service:

    docker-compose run influxdb-cli
    Starting influxdb ... done
    Connected to http://influxdb:8086 version 1.5.4
    InfluxDB shell version: 1.5.4
    > 

### Check that Telegraf works

By default, the included Telegraf config creates `vm_metrics` & `docker_metrics` databases.
Check that InfluxDB has such a databases in addition to the `_internal` database.

    docker-compose run influxdb-cli
    Starting influxdb ... done
    Connected to http://influxdb:8086 version 1.5.4
    InfluxDB shell version: 1.5.4
    > show databases
    name: databases
    name
    ----
    _internal
    vm_metrics
    docker_metrics

### Check that Chronograf works

Access the Chronograf inteface, [http://localhost:8888](http://localhost:8888)

### Check Kapacitor works

First, run this curl command, if no errors occur Kapacitor is running:

    curl http://localhost:9092/kapacitor/v1/ping

Use the built-in kapacitor cli service:

    docker-compose run kapacitor-cli
    Starting influxdb ... done
    Starting kapacitor ... done
    / # 
    ID Type      Status    Executing Databases and Retention Policies

Confirm Kapacitor is subscribed to all databases in InfluxDB

    docker-compose run influxdb-cli
    Starting influxdb ... done
    Connected to http://influxdb:8086 version 1.5.4
    InfluxDB shell version: 1.5.4
    > show subscriptions
    name: _internal
    retention_policy name                                           mode destinations
    ---------------- ----                                           ---- ------------
    monitor          kapacitor-147fd61d-f822-4826-821a-548d8401b7e3 ANY  [http://e934765f33ac:9092]
     
    name: vm_metrics
    retention_policy name                                           mode destinations
    ---------------- ----                                           ---- ------------
    autogen          kapacitor-147fd61d-f822-4826-821a-548d8401b7e3 ANY  [http://e934765f33ac:9092]
     
    name: docker_metrics
    retention_policy name                                           mode destinations
    ---------------- ----                                           ---- ------------
    autogen          kapacitor-147fd61d-f822-4826-821a-548d8401b7e3 ANY  [http://e934765f33ac:9092]

## References

- https://grafana.com/
- https://www.influxdata.com/time-series-platform/