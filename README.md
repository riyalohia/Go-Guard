# Go-Guard Setup

* Launch 3 EC2 instances of AWS with a security group allowing the port numbers of all the exporters you want.
* Make a server/local host a centralised server having prometheus.
* Install prometheus by :
	./prometheus_install.sh
* Update the /etc/prometheus/prometheus.yml

global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    scrape_interval: 5s
    static_configs:
      - targets: ['localhost:9090']

	

* Make service of prometheus:
	vim /etc/systemd/system/prometheus.service
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \
    --config.file /etc/prometheus/prometheus.yml \
    --storage.tsdb.path /var/lib/prometheus/ \
    --web.console.templates=/etc/prometheus/consoles \
    --web.console.libraries=/etc/prometheus/console_libraries

[Install]
WantedBy=multi-user.target
* sudo systemctl daemon-reload
* sudo systemctl start prometheus
* sudo systemctl status prometheus

* Check for the http://localhost:9090
* Install the services you want to monitor in every instance.
* Run ansible roles to install exporters of every service and make it as a service.
	ansible-playbook -i hosts prometheus.yml --become

* Update the /etc/prometheus/prometheus.yml file.
* Check the matrics on prometheus .

* Install grafana and import prometheus as the data source.
* Create new dashboards for exporters and import json file for respective exporters from grafana-dashboards folder.
* Visualize the dashboards.

* Install alertmanager.
* Setup rules.yml and alertmanager.yml
* Receive alerts.
