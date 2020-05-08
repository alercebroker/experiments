# Monitoring
Within the environment of tests and experiments we use different tools to be able to monitor the behavior of the architecture. Below is the list of the applications used.

### Applications list
* Grafana 
* Prometheus
* Node_exporter
* ElasticSearch - Kibana (in construction)

## Grafana [Link](https://grafana.com/)
### Definition

Grafana is an open-source application that allows metrics to be loaded and displayed with different data sources

### Case of use
#### - Description

In the following dashboard we observe the health of the kafka service and the platform that supports it. Among the most important items, we see the health status of the server, kafka own metrics such as: topics, partitions, broker, etc.

![1](https://i.ibb.co/F7F9qGr/Captura-de-Pantalla-2020-05-07-a-la-s-17-11-15.png)
![2](https://i.ibb.co/6yrX4XL/Captura-de-Pantalla-2020-05-07-a-la-s-17-11-32.png)

#### - installation
* Download and installation
```
wget https://dl.grafana.com/oss/release/grafana-6.7.3.linux-amd64.tar.gz
tar -zxvf grafana-6.7.3.linux-amd64.tar.gz
```
* Services configurations
```
sudo systemctl daemon-reload
sudo systemctl start grafana-server
sudo systemctl status grafana-server
sudo systemctl enable grafana-server.service
```
* Start services
```
sudo service grafana-server start
sudo service grafana-server status
```
* boot start
```
./bin/grafana-server web
```

## Prometheus [Link](https://prometheus.io/)

### Definition

Prometheus is an open-source application that captures data in time series in a database. the data is collected and can be consumed by different visualization tools, in this case Grafana.

### Case of use
#### - Descriptions

In the attached image we can see the configuration file prometheus.yml which is the main element with which the user is going to interact. In this file we must configure global options such as update time and scan intervals. Then we can declare "Jobs" that refer to an item to scan. Here we can declare the name of the Job, the ips and ports of entry to receive the metrics. We must follow the structure of the YAML format for Prometheus to work properly.

![3](https://i.ibb.co/F5R42k6/Captura-de-Pantalla-2020-05-08-a-la-s-09-29-07.png)

#### - Installation
* Create new 'prometheus' user using the command below.
```
useradd -m -s /bin/bash prometheus
```
* A new user has been created. Now log in to the user and download prometheus using the wget command.
```
su - prometheus
wget https://github.com/prometheus/prometheus/releases/download/v2.13.1/prometheus-2.13.1.linux-amd64.tar.gz
```
* Extract the prometheus-xx.x.x.tar.gz file and rename the directory to 'prometheus'.

```
tar -xf prometheus-2.13.1.linux-amd64.tar.gz
mv prometheus-2.13.1.linux-amd64/ prometheus/
```
* After that, create a new 'data' directory that will be used as a 'tsdb' storage.

```
mkdir -p ~/prometheus/data
```
* In this step, we will configure prometheus as a systemd service. We will create a new service file prometheus.service on the '/etc/systemd/system' directory. Go to the '/etc/systemd/system/' directory and create new service file 'prometheus.service' using vim editor.

```
cd /etc/systemd/system/
vim prometheus.service
```
* Paste the prometheus service configuration below.
```
[Unit]
Description=Prometheus Server
Documentation=https://prometheus.io/docs/introduction/overview/
After=network-online.target

[Service]
User=prometheus
Restart=on-failure

#Change this line if you download the 
#Prometheus on different path user
ExecStart=/home/prometheus/prometheus/prometheus \
  --config.file=/home/prometheus/prometheus/prometheus.yml \
  --storage.tsdb.path=/home/prometheus/prometheus/data

[Install]
WantedBy=multi-user.target
```
* Now reload the systemd system using the systemctl command below and start the prometheus service and enable it to launch everytime at system startup.

```
systemctl daemon-reload
systemctl start prometheus
systemctl enable prometheus
systemctl status prometheus
netstat -plntu
```
* Make sure you get the result as shown below.

![4](https://www.howtoforge.com/images/how_to_install_prometheus_and_node_exporter_on_centos_8/big/4.png)


## Node_exporter [Link](https://github.com/prometheus/node_exporter)
### Definition

Node_Exporter is an open-source application that aims to capture metrics of the hardware (disk, cpu, ram, network, etc.) of the server where it is installed. These metrics are sent to the Prometheus server and stored for future viewing.

### Case of use

In the experiments carried out we use Node_exporter to measure the hardware metrics of all the servers involved (Kafka, Postgres, Pipeline). 

#### - Description

Below you can see metrics for CPU, RAM, Disks, Network, etc.

![5](https://i.ibb.co/nRRsJH2/Captura-de-Pantalla-2020-05-08-a-la-s-10-08-05.png)

By double clicking on the configuration of the CPU panel, you can see how it is configured and the sentences that Grafana offers us.

![6](https://i.ibb.co/W0S8623/Captura-de-Pantalla-2020-05-08-a-la-s-10-10-02.png)

#### - Installation

* Login to the prometheus user and download the 'node_exporter' using the wget command.
```
su - prometheus
wget https://github.com/prometheus/node_exporter/releases/download/v0.18.1/node_exporter-0.18.1.linux-amd64.tar.gz
```
* Extract the node_exporter and rename the directory to 'node_exporter'.
```
tar -xf node_exporter-0.18.1.linux-amd64.tar.gz
mv node_exporter-0.18.1.linux-amd64 node_exporter
Install and Configure node_exporter
````

* Next, we will create new service file for the node_exporter. Back to the root shell, goto the '/etc/systemd/system' directory and create new node_exporter service file 'node_exporter.service' using vim.
```
cd /etc/systemd/system/
vim node_exporter.service
Paste the following configuration.
```
```
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
ExecStart=/home/prometheus/node_exporter/node_exporter

[Install]
WantedBy=default.target
Save and exit.
````

* Now reload the systemd system.
```
systemctl daemon-reload
```

* Then start the node_exporter service and enable it to launch everytime at system startup.
```
systemctl start node_exporter
systemctl enable node_exporter
Start node_exporter
```
* The node_exporter service is now running on the server - check it using the netstat command.
```
systemctl status node_exporter
netstat -plntu
```

* Add node_exporter to the Prometheus Server. In this step, we will add the node_exporter to the prometheus server. Login to the prometheus user and edit the configuration 'prometheus.yml' file.
```
su - prometheus
```
```
cd ~/prometheus/
vim prometheus.yml
```
* Under the 'scrape_config' line, add new job_name node_exporter by copy-pasting the configuration below.
```
  - job_name: 'node_exporter'
    static_configs:
    - targets: ['localhost:9100']
```
Save and exit.

* Now restart the prometheus service.
```
systemctl restart prometheus
```
