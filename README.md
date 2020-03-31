# Prometheus Quickstart
https://github.com/cloudalchemy/ansible-prometheus

THIS SETUP IS ONLY FOR LINUX USERS

### Install Ansible:

	>> sudo apt insatll ansible python-pip

### Git clone Ansible-prometheus:
	
	>> git clone https://github.com/cloudalchemy/ansible-prometheus.git

### Install pip and tox:
	
	>>  install tox

### Setup role:

	>> cd ansible-prometheus
	>> mkdir -p roles/cloudalchemy.prometheus
	>> mv defaults/ handlers/ meta/ molecule/ tasks/ templates/ vars/ roles/cloudalchemy.prometheus/
		

### Create inventory file:
	>> vim inventory

	""" Add the below line to inventory file """

	host ansible_connection=local


### Create main.yaml playbook:

	>> vim main.yaml

	""" ADD FOLLOWING EXAMPLE PLAYBOOK TO main.yaml FILE """
	---
	- hosts: all
	  roles:
	  - cloudalchemy.prometheus
	  vars:
	    prometheus_targets:
	      node:
	      - targets:
		- localhost:9100
		- demo.cloudalchemy.org:9100
		labels:
		  env: demosite


### Run playbook:
	
	>> sudo ansible-playbook -i inventory main.yaml 

### Install node exporter and configure:

	>> wget https://github.com/prometheus/node_exporter/releases/download/v0.18.1/node_exporter-0.18.1.linux-amd64.tar.gz

	>> tar xvfz node_exporter-0.18.1.linux-amd64.tar.gz

	>> cd node_exporter-0.18.1.linux-amd64.tar.gz

	>> sudo mv node_exporter /usr/local/bin/

	>> sudo vim /etc/systemd/system/node_exporter.service 



	""" ADD FOLLOWING LINES TO THIS FILE """

	#
	#Node Exporter
	#

	[Unit]
	Description=Node Exporter
	After=network.target

	[Service]
	Type=simple
	User=node_exporter
	Group=node_exporter
	ExecStart=/usr/local/bin/node_exporter

	[Install]
	WantedBy=multi-user.target



	>> sudo systemctl daemon-reload
	>> sudo systemctl restart node_exporter
	>> ^restart^status

	>> sudo vim /etc/prometheus/prometheus.yml

	""" ADD A JOB NAME TO THIS FILE AT THE END """

	 - job_name: 'node_exporter_service'
	    scrape_interval: 5s
	    static_configs:
	      - targets: ['localhost:9100']


	

### Check web interface

	http://localhost:9090
	http://localhost:9100/metrics

### Adding Grafana
	
	>> curl -LO https://s3-us-west-2.amazonaws.com/grafana-releases/release/grafana_5.1.4_amd64.deb ;
	>> sudo apt-get install -y adduser libfontconfig ;
	>> sudo dpkg -i grafana_5.1.4_amd64.deb ;
	>> sudo systemctl start grafana-server ;
	>> sudo systemctl enable grafana-server

Grafana will be running on port 3000
Default username and password is : admin, admin

