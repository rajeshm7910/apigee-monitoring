# apigee-monitoring
Apigee Edge Private Cloud Monitoring can really be a dauting task. Apigee Supports JMX and you can use any of the JMX tools like JConsole to monitor system parameters. This projects brings you the monitoring with Grafana, Influx and Telegraf. 


# How it works



# Quick Start

## Influxdb
Influxdb is a time series database. 

### Install Influxdb
Please refer https://docs.influxdata.com/influxdb/v1.5/introduction/installation/  for installing influxdb. We are supporting influxdb 1.2.2+.

Requirements
- CPU: 2 Core
- RAM: 4 GB
- Disk: 500 GB SDD, 3000 IOPS.

In case you are using RHEL/Centos, you can install with following commands -

```
    curl -O https://dl.influxdata.com/influxdb/releases/influxdb-1.5.3.x86_64.rpm
    sudo yum localinstall influxdb-1.5.3.x86_64.rpm
```

Enable and Start influxdb

```
systemctl enable influxdb
systemctl start influxdb
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

> exit
```

## Grafana

Grafana UI allows visual representation of events and metrics. In this case we will import some pre baked dashboards that will give some useful insights into Apigee.


### Install Grafana

Please refer to http://docs.grafana.org/installation/rpm/  for installing grafana. 
You can install grafana on management server node or any other nodes.

- Add the following to a new file at /etc/yum.repos.d/grafana.repo

```
[grafana]
name=grafana
baseurl=https://packagecloud.io/grafana/stable/el/7/$basearch
repo_gpgcheck=1
enabled=1
gpgcheck=1
gpgkey=https://packagecloud.io/gpg.key https://grafanarel.s3.amazonaws.com/RPM-GPG-KEY-grafana
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt

```

- Install Grafana as follows

```
sudo yum install grafana
```

- Start the server (via systemd)
```
sudo systemctl daemon-reload
sudo systemctl start grafana-server
sudo systemctl status grafana-server
```

- Enable the systemd service to start at boot
```
sudo systemctl enable grafana-server.service
```

### Configure Grafana

Create 3 datasources : application-dc-1 , system-dc-1 and events-dc-1 and point them to application, system and events database of influx. 

- name: application-dc-1
- type :  Choose the type to influxDB
- HTTP URL : This is the influxdb  machines ip address. The default port is 8086. 
- Influx DB Details:
    -- Database : application
    -- User: root
    -- Password: root

Application Data Source
![Application Data Source](/docs/images/application-dc-1.png)

Events Data Source
![Events Data Source](/docs/images/events-dc-1.png)

System Data Source
![System Data Source](/docs/images/system-dc-1.png)



### Import Dasboards

Import All the Pre built dashboards provided this sample. You can also build your own dashoards.Upload the json file from the UI as given below.

Import Dasboards
![Import Dasboard](/docs/images/importDashboard.png)


## Telegraf

Telegraf is the collection agent that collects all metrics from all apigee components. 

### Install Telegraf

Install telegraf agent on all apigee nodes which needs monitoring. 


```
 cat <<EOF | sudo tee /etc/yum.repos.d/influxdb.repo
  [influxdb]
  name = InfluxDB Repository - RHEL \$releasever
  baseurl = https://repos.influxdata.com/rhel/\$releasever/\$basearch/stable
  enabled = 1
  gpgcheck = 1
  gpgkey = https://repos.influxdata.com/influxdb.key
  EOF
```

### Configure Telegraf - This needs to be configured in all nodes.

- Copy telegraf.conf from samples provided telegraf/telegraf.conf to /etc/telegraf/telegraf.conf in the host.
- Edit /etc/telegraf/telegraf.conf and replace 127.0.0.1 with ip address of influxdb server. 
- Copy relevant toml files present in telegraf/telegraf.d/ to /etc/telegraf/telegraf.d/ directories based on the apigee component running on that node.
For ex: If  node is running  apigee cassandra and apigee zookeeper only, copy apigee-cassandra.toml and apigee-zookeeper.toml to /etc/telegraf/telegraf.d/
If management server is running copy  edge-management-server.toml.


### Start Telegraf 


```
  sudo yum install telegraf
  sudo systemctl start telegraf
  sudo systemctl enable telegraf
  sudo systemctl status telegraf
```


## Finishing Up

You can now browse the UI through http://<grafanaIPorDNS>:3000 

Login Creds
- user : admin
- password: admin

Hurry!!! You have just setup basic monitoring. 

## Whats Next -

#### Alerting 

[here](http://docs.grafana.org/alerting/rules/)





