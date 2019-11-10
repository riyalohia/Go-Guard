# Go-Guard Setup

1.Launch 3 EC2 instances of AWS with a security group allowing the port numbers of all the exporters you want.
2. Make a server/local host a centralised server having prometheus.
3. Install prometheus by :
	./prometheus_install.sh
4. Update the /etc/prometheus/prometheus.yml

global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    scrape_interval: 5s
    static_configs:
      - targets: ['localhost:9090']

	

5. Make service of prometheus:
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
6. sudo systemctl daemon-reload
7. sudo systemctl start prometheus
8. sudo systemctl status prometheus

9. Check for the http://localhost:9090
10. Install the services you want to monitor in every instance.
11. Run ansible roles to install exporters of every service and make it as a service.
	ansible-playbook -i hosts prometheus.yml --become

12. Update the /etc/prometheus/prometheus.yml file.
13. Check the matrics on prometheus .

14. Install grafana and import prometheus as the data source.
15. Create new dashboards for exporters and import json file for respective exporters from grafana-dashboards folder.
16. Visualize the dashboards.

17. Install alertmanager.
18. Setup rules.yml and alertmanager.yml
19. Receive alerts.
