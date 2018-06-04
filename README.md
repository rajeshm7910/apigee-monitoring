# apigee-monitoring
Apigee Edge Private Cloud Monitoring can really be a dauting task. Apigee Supports JMX and you can use any of the JMX tools like JConsole to monitor system parameters. This projects brings you the monitoring with Grafana, Influx and Telegraf. 


# How it works



# Quick Start

### Install influxdb
Influxdb is a time series database. Please refer https://docs.influxdata.com/influxdb/v1.5/introduction/installation/  for installing influxdb. We are supporting influxdb 1.2.2+.

* Requirements
- CPU: 2 Core
- RAM: 4 GB
- Disk: 500 GB SDD, 3000 IOPS.

In case you are using RHEL/Centos, you can install with following commands -

```
    curl -O https://dl.influxdata.com/influxdb/releases/influxdb-1.5.3.x86_64.rpm
    sudo yum localinstall influxdb-1.5.3.x86_64.rpm
```


### Configure Influxdb

Once influxdb is installed, Go to the influx terminal to create initial databases

```
influx
Connected to http://localhost:8086 version 1.5.3
InfluxDB shell version: 1.5.3
> 
```
- Show databases
```
> SHOW databases
name: databases
name
----
_internal

```
- Create databases - system,events, application
```
> CREATE database application
> CREATE database system
> CREATE database events
> SHOW databases
name: databases
name
----
_internal
system
events
application
```


