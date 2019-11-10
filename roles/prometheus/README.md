# Automation of Innovaccer's Infra Monitoring


List of Services, automated for monitoring.

  - MySQL
  - Postgres
  - MongoDB
  - Redis
  - ElasticSearch
  - Host
  - Namenode
  - Datanode
  - yarn
  - Zookeeper
  - Greenplum
  - Blackbox
  - Process
  
# Ansible Role: Prometheus Exporter

  - An ansible role which contains multiple exporters of prometheus for scrapping data and for enhancing your monitoring stack. 

### Installation

All the exporters are extracted from thier Go images build and have been put in binary file.
Link(for all prometheus exporter): https://github.com/innovaccer/Devops/tree/develop/prometheus_exporters

 ### Dependencies
 - None
### Steps for Setup of each service which are been monitored via prometheus :

Exporters are installed/configured on the clients in order to convert and expose their metrics in a Prometheus format.For every services to be monitored on particular project,we are making service for that exporter, which is run by every unique user made by running ansible roles.

### Exporter Version Used
**Needs to be change depending upon environment**

|**Variables**| **Default Values**| **Description**|
|----------|---------|---------------|
|**node_exporter_version** | 0.17.0 | Version of Node Exporter|
|**es_exporter** | 1.0.4 | Version of Elasticsearch Exporter|
|**mongodb_exporter** | 1.0.0 | Version of MongoDB Exporter|
|**nginx_exporter** | 1.3.0 | Version of Nginx Exporter|
|**mysql_exporter_version** | 0.11.0 | Version of Mysql Exporter|
|**zookeeper_exporter** | 0.1.0 | Version of Zookeeper Exporter|
|**postgres_exporter** | 0.4.7 | Version of Postgres Exporter|
|**redis** | 0.27.0 | Version of Redis Exporter|
|**process_exporter** | 0.4.0 | Version of Process Exporter|
|**blackbox_exporter** | 0.14.0 | Version of Balckbox Exporter|
|**postgres_exporter** | 0.5.0 | Version of Postgres Exporter(Monitoring Greenplum)|


### 1) Datanode Monitoring:

#### - Service

```sh
$ vim /etc/systemd/system/datanode-exporter.service
```
> [Unit]
> Description=Datanode Exporter
> After=network.target
> [Service]
> User=datanode_exporter
> Group=datanode_exporter
> Type=simple
> ExecStart=/usr/local/bin/datanode-exporter.sh
> [Install]
> WantedBy=multi-user.target
#### - shell file for running datanode binary exporter
> #!/usr/bin/bash
/usr/local/bin/datanode_exporter -datanode.jmx.url "http://machine_ip:50075/jmx"
#### - Address on which to expose metrics and web interface.
-web.listen-address string (default ":9072")
```sh
$ curl machine_ip:9072/metrics
```

#### 2) ElasticSearch Monitoring:
#### - Service

```sh
$ vim /etc/systemd/system/elasticsearch-exporter.service
```
> [Unit]
> Description=Elasticsearch Exporter
> After=network.target
> [Service]
> User=elasticsearch_exporter
> Group=elasticsearch_exporter
> Type=simple
> ExecStart=/usr/local/bin/elasticsearch_exporter
> [Install]
> WantedBy=multi-user.target
#### - Address on which to expose metrics and web interface.
-web.listen-address string (default ":9108")
```sh
$ curl machine_ip:9108/metrics
```
#### 3) MongoDB Monitoring
#### - Service

```sh
$ vim /etc/systemd/system/mongo-exporter.service
```
> [Unit]
> Description=Mongodb Exporter
> After=network.target
> [Service]
> User=mongodb_exporter
> Group=mongodb_exporter
> Type=simple
> ExecStart=/usr/local/bin/mongodb_exporter
> [Install]
> WantedBy=multi-user.target

#### - Address on which to expose metrics and web interface.
-web.listen-address string (default ":9001")
```sh
$ curl machine_ip:9001/metrics
```

#### 4) MySQL Monitoring
#### - Service

```sh
$ vim /etc/systemd/system/mysqld-exporter.service
```
> [Unit]
> Description=Mysqld Exporter
> After=network.target
> [Service]
> User=root
> Group=root
> Type=simple
> ExecStart=/usr/local/bin/mysqld_exporter
> [Install]
> WantedBy=multi-user.target 

#### - Address on which to expose metrics and web interface.
-web.listen-address string (default ":9104")
```sh
$ curl machine_ip:9104/metrics
```

#### 5) Host Monitoring
#### - Service

```sh
$ vim /etc/systemd/system/node-exporter.service
```
> [Unit]
> Description=Node Exporter
> After=network.target
> [Service]
> User=node_exporter
> Group=node_exporter
> Type=simple
> ExecStart=/usr/local/bin/node_exporter
> [Install]
> WantedBy=multi-user.target

#### - Address on which to expose metrics and web interface.
-web.listen-address string (default ":9100")
```sh
$ curl machine_ip:9100/metrics
```

### 6) Namenode Monitoring:

#### - Service

```sh
$ vim /etc/systemd/system/namenode-exporter.service
```
> [Unit]
> Description=Namenode Exporter
> After=network.target
> [Service]
> User=namenode_exporter
> Group=namenode_exporter
> Type=simple
> ExecStart=/usr/local/bin/namenode-exporter.sh
> [Install]
> WantedBy=multi-user.target
#### - shell file for running namenode binary exporter
> #!/usr/bin/bash
/usr/local/bin/namenode_exporter -namenode.jmx.url "http://10.10.14.249:50070/jmx"


#### - Address on which to expose metrics and web interface.
-web.listen-address string (default ":9070")
```sh
$ curl machine_ip:9070/metrics
```

### 7) Nginx Log Monitoring:

#### - Service

```sh
$ vim /etc/systemd/system/nginxlog-exporter.service
```
> [Unit]
> Description=Nginx Log Exporter
> After=network.target
> [Service]
> User=root
> Group=root
> Type=simple
> ExecStart=/usr/local/bin/nginxlog_exporter -config-file  /etc/nginxlog_exporter/nginxlog_config.yml
> [Install]
> WantedBy=multi-user.target
#### - config file(for passing metrices value) for running nginx binary exporter
> listen:
  port: 8188
  address: "0.0.0.0"
  
>namespaces:
>  -- name: nginx_access
>    format: '[$time_local] "$http_x_forwarded_for" "$remote_addr" "$upstream_addr" "$http_host" "$remote_user" "$request" "$http_referer" "$http_user_agent" "$upstream_status" "$status" "$body_bytes_sent" "$bytes_sent" "$upstream_response_time" "$request_time"'
>    source_files:
      - /var/log/nginx/access.log
>    labels:
      app: "nginx_log"
      environment: "production"
      logtype: "access.log"
>    histogram_buckets: [.005, .01, .025, .05, .1, .25, .5, 1, 2.5, 5, 10]
#### - Address on which to expose metrics and web interface.
-web.listen-address string (setting the listen port":8188"){by changing the config file}
```sh
$ curl machine_ip:8188/metrics
```

#### 8) Postgres Monitoring
#### - Service

```sh
$ vim /etc/systemd/system/postgres-exporter.service
```
> [Unit]
> Description=Postgres Exporter
> After=network.target
> [Service]
> User=postgres_exporter
> Group=postgres_exporter
> Type=simple
> ExecStart=/usr/local/bin/run_postgres_exporter.sh
> [Install]
> WantedBy=multi-user.target
#### - shell file for running postgres binary exporter
>#!/bin/bash
> DATA_SOURCE_NAME="postgresql://{{ postgres_user }}:{{ postgres_password }}@{{ postgres_hostname }}:5432/{{ postgres_db }}?sslmode=disable" /usr/local/bin/postgres_exporter

#### - Address on which to expose metrics and web interface.
-web.listen-address string (default ":9187")
```sh
$ curl machine_ip:9187/metrics
```

#### 9) Yarn Monitoring:
#### - Service

```sh
$ vim /etc/systemd/system/yarn-exporter.service
```
> [Unit]
> Description=Yarn Exporter
> After=network.target
> [Service]
> Environment="YARN_PROMETHEUS_LISTEN_ADDR=:9113"
> Environment="YARN_PROMETHEUS_ENDPOINT_SCHEME=http"
> Environment="YARN_PROMETHEUS_ENDPOINT_HOST=localhost"
> Environment="YARN_PROMETHEUS_ENDPOINT_PORT=8088"
> User=yarn_exporter
> Group=yarn_exporter
> Type=simple
> ExecStart=/usr/local/bin/yarn-prometheus-exporter
> [Install]
> WantedBy=multi-user.target
#### - Address on which to expose metrics and web interface.
-listen tcp (default ":9113")
```sh
$ curl machine_ip:9113/metrics
```

#### 10) Zookeeper Monitoring:
#### - Service

```sh
$ vim /etc/systemd/system/zookeeper-exporter.service
```
> [Unit]
> Description=Zookeeper Exporter
> After=network.target
> [Service]
> User=zookeeper_exporter
> Group=zookeeper_exporter
> Type=simple
> ExecStart=/usr/local/bin/zookeeper_exporter
> [Install]
> WantedBy=multi-user.target

#### - Address on which to expose metrics and web interface.
- bind-addr string (default ":9141")
```sh
$ curl machine_ip:9141/metrics
```
#### 11) Redis Monitoring:
#### - Service

```sh
$ vim /etc/systemd/system/redis-exporter.service
```
> [Unit]
> Description=Redis Exporter
> After=network.target
> [Service]
> User=redis_exporter
> Group=redis_exporter
> Type=simple
> ExecStart=/usr/local/bin/redis_exporter
> [Install]
> WantedBy=multi-user.target

#### - Address on which to expose metrics and web interface.
-web.listen-address string (default ":9121")
```sh
$ curl machine_ip:9121/metrics
```

### 12) Process Monitoring:

#### - Service

```sh
$ vim /etc/systemd/system/process-exporter.service
```
> [Unit]
> Description=process Exporter
> After=network.target
> [Service]
> User=process_exporter
> Group=process_exporter
> Type=simple
> ExecStart=/usr/local/bin/process_exporter -config.path /etc/process_exporter/process_exporter.yml
> [Install]
> WantedBy=multi-user.target
#### - config file for process exporter
> process_names:
>  - name: "{{.ExeFull}}"
>    cmdline:
>    - '.+'


#### - Address on which to expose metrics and web interface.
-web.listen-address string (default ":9256")
```sh
$ curl machine_ip:9256/metrics
```

> Extra Exporter Reference:
> Namenode,Datanode : https://github.com/Datatamer/hadoop_exporter
> Yarn : https://github.com/PBWebMedia/yarn-prometheus-exporter 





