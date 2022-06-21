---
title: "Build monitoring system base on prometheus and grafana"
author: "rogers"
date: 2022-06-20T00:06:57+08:00
slug: "build-monitoring-system-base-on-prometheus-and-grafana"
summary: "quickly build your monitor system based on prometheus and grafana"
tags: ["monitoring", "prometheus", "grafana", "alertmanager"]
---

## source

[prometheus](https://prometheus.io/download/)

[grafana](https://grafana.com/grafana/download)

[telegraf](https://github.com/influxdata/telegraf)

## install

### alertmanager

```bash
wget https://github.com/prometheus/alertmanager/releases/download/v0.24.0/alertmanager-0.24.0.linux-amd64.tar.gz -O alertmanager.tar.gz
tar -zxvf alertmanager.tar.gz
cd alertmanager
#you will see the alertmanager binary and a simple yaml config
./alertmanager -h
#show usage of alertmanager
```

alertmanager.yml

```yaml
route:
  group_by: ["alertname"]
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 1h
  receiver: "web.hook"
receivers:
  - name: "web.hook"
    webhook_configs:
      - url: "http://127.0.0.1:5001/"
inhibit_rules:
  - source_match:
      severity: "critical"
    target_match:
      severity: "warning"
    equal: ["alertname", "dev", "instance"]
```

`./alertmanater` start alertmanager server on port 9093

### node exporter

Prometheus collects variable metrics from variable kinds of exporter

```bash
wget https://github.com/prometheus/node_exporter/releases/download/v1.3.1/node_exporter-1.3.1.linux-amd64.tar.gz -O node_exporter.tar.gz
tar -zxvf node_exporter.tar.gz
cd node_exporter
#you will see the node exporter binary
./node_exporter
#start node exporter on port 9100
```

### prometheus

```bash
wget https://github.com/prometheus/prometheus/releases/download/v2.36.2/prometheus-2.36.2.linux-amd64.tar.gz -O prometheus.tar.gz
tar -zxvf prometheus
cd prometheus
#you will see the prometheus binary and a simple yaml config
./prometheus -h
#show usage of prometheus
```

modify your prometheus.yml
prometheus.yml

```yaml
# my global config
global:
  scrape_interval: 15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).
# Alertmanager configuration
alerting:
  alertmanagers:
    - static_configs:
        - targets:
            - localhost:9093
# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  - "first_rules.yml"
  # - "second_rules.yml"
# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: "prometheus"
    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.
    static_configs:
      - targets: ["localhost:9090"]
  - job_name: "node"
    static_configs:
      - targets: ["localhost:9100"]
```

first_rules.yml

```yaml
groups:
  - name: high_cpu
    rules:
      - alert: HostHighCpuLoad
        expr: 100 - (avg by(instance) (rate(node_cpu_seconds_total{mode="idle"}[2m])) * 100) > 1
        for: 0m
        labels:
          severity: warning
        annotations:
          summary: Host high CPU load (instance {{ $labels.instance }})
          description: "CPU load is > 80%\n  VALUE = {{ $value }}\n  LABELS = {{ $labels }}"
```

start a simple web server receives the webhook

```python
from flask import Flask, request
app = Flask(__name__)
@app.route("/", methods=["POST"])
def index():
    """docstring for index"""
    print(request.data, "data")
    print(request.args, "args")
    print(request.form, "form")
    return "ok"
```

### grafana

```bash
sudo apt-get install -y adduser libfontconfig1
wget https://dl.grafana.com/oss/release/grafana_9.0.0_amd64.deb
sudo dpkg -i grafana_9.0.0_amd64.deb
sudo systemctl enable --now grafana-server.service
```

open you browser open [grafana dashboard](http://localhost:3000/)
login use admin/admin and change your default password
open setting datasource add prometheus datasource
find node dashboard in [grafana dashboard market](https://grafana.com/grafana/dashboards/)
import dashboard by id 1860
![screenshot](/images/grafana-prometheus.png)
