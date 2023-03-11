---
aliases: ["posts", "articles", "blog", "ops", "docs", "kubernetes"]
title: "Quickly deploy a prometheus cluster via helm"
author: "rogers"
date: 2023-03-11T15:40:57+08:00
slug: "kube-prometheus"
summary: "quickly deploy a prometheus cluster via helm, using bitnami chart repo"
tags: ["helm", "k8s", "kubernetes", "prometheus"]
---

## Source

- [Helm](https://helm.sh/docs/intro/install/#helm)
- [Kubernetes](https://kubernetes.io/docs/home/)
- [Bitnami](https://github.com/bitnami/charts)

### Optional

- [Kubeapps](https://github.com/vmware-tanzu/kubeapps)

## Install

Example lines you may need change, find the full values by `helm show values bitnami/kube-prometheus`

```yaml
# kube-prometheus.yaml
global:
  storageClass: "alicloud-disk-available"
prometheus:
  # set url or use ingress
  externalUrl: ""
  ruleSelector: { matchLabels: { prometheus: "example" } }
  persistence:
    enable: true
    # alibabacloud require at least 20Gi per pv
    size: 20Gi
  thanos:
    # if you need update date to object store, eg monio, s3
    objectStorageConfig:
      secretName: thanos.yaml
      # thanos compactor externalconfig need filename to be objstore.yml
      secretKey: objstore.yml
    create: true
alertmanager:
  config:
    global:
      resolve_timeout: 5m
    route:
      group_by: ["alertname"]
      group_wait: 30s
      group_interval: 5m
      repeat_interval: 1h
      receiver: "webhook"
      routes:
        - match:
            alertname: Watchdog
          receiver: "webhook"
    receivers:
      - name: "webhook"
        webhook_configs:
          - url: "http://webhook.com/notify/prom"
  configSelector: { matchLabels: { alertmanagerConfig: "example" } }
```

```bash
# Here i use prom namespace
kubectl create ns prom
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install prom  bitnami/kube-prometheus -n prom -f kube-prometheus.yaml
```

A prometheus cluster with basic feature will finish after severial
minutes base on your network
