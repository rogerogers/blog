---
aliases: ["posts", "articles", "blog", "ops", "docs", "kubernetes"]
title: "Crictl usage"
author: "rogers"
date: 2022-06-18T00:06:57+08:00
slug: "crictl-usage"
summary: "crictl usage, simple config and run you first pod and container"
tags: ["linux", "k8s", "kubernetes", "cni", "containerd"]
---

## Install

```bash
# ubuntu debain and other debain base distribution
sudo apt install crictl -y
# centos fedora and other red hat base distribution
sudo yum install crictl -y
```

> You can download directly from [github release page](https://github.com/kubernetes-sigs/cri-tools/releases/)

### Create /etc/crictl.yaml

```yaml
runtime-endpoint: "unix:///run/containerd/containerd.sock"
image-endpoint: ""
timeout: 0
debug: false
pull-image-on-create: false
disable-pull-on-run: false
```

### Create pod config pod-config.yaml

```yaml
metadata:
  attempt: 1
  name: nginx-sandbox
  namespace: default
  uid: hdishd83djaidwnduwk28bcsb
log_directory: /tmp
linux:
  namespaces:
    options: {}
```

### Create container config container-config.yaml

```yaml
metadata:
  name: nginx
image:
  image: docker.io/nginx:alpine
log_path: nginx.log
```

```bash
crictl run container-config.json pod-config.json
crictl ps -a
#CONTAINER           IMAGE               CREATED             STATE               NAME                ATTEMPT             POD ID              POD
#86f1c7a2aaffe       nginx               23 minutes ago      Running             nginx               0                   dd6d7d4ea70cd       unknown
crictl start 86f1c7a2aaffe
crictl ps
crictl pods
#POD ID              CREATED             STATE               NAME                NAMESPACE           ATTEMPT             RUNTIME
#dd6d7d4ea70cd       About an hour ago   Ready               nginx               default             1                   (default)
crictl port-forward dd6d7d4ea70cd 80:80
curl localhost
#welcome to nginx
```
