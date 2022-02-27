+++
draft = false
date = 2022-02-27T23:00:00+08:00
title = "Deploy k8s with kubeadm"
description = "begin learning golang"
slug = "deploy-k8s-by-kubeadm"
authors = ["rogerogers"]
tags = ["golang", "k8s", "kubeadm", "etcdadm"]
categories = ["k8s", "ops"]
externalLink = ""
series = []
+++

## 1. Install etcd

You can use etcd supply by kubeadm

Here i use etcdadm deploy etcd etcdcluster

```curl https://github.com/kubernetes-sigs/etcdadm/releases/download/v0.1.5/etcdadm-linux-amd64 -o ~/.local/bin/etcdadm && chmod u+x ~/.local/bin/etcdadm```

Full usage see [etcdadm](https://github.com/kubernetes-sigs/etcdadm)

## 2. Install kubeadm

I use ubuntu as in my computer

### First add repo and install

    1. sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
    2. echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
    3. sudo apt-get update && sudo apt-get install -y kubelet kubeadm kubectl && sudo apt-mark hold kubelet kubeadm kubectl 

### Turn swap off

```sudo swapoff -a```

### Start kubeadm with config

My simple config [kubeadm-config.yaml](https://gist.github.com/rogerogers/0fe483c961430ef0b61df373e3597058)

```sudo kubeadm init --skip-phases=etcd --config kubeadm-config.yml```

## 3. Install calico

You may use flannel, calico is my choice

```kubectl create -f https://projectcalico.docs.tigera.io/manifests/tigera-operator.yaml```

```kubectl create -f https://projectcalico.docs.tigera.io/manifests/custom-resources.yaml```

You may change the ippool address, Full usage see [projectcalico](https://projectcalico.docs.tigera.io/getting-started/kubernetes/quickstart)

## congratulations

Now you can see you k8s running by command `kubectl get pod -A`

Learn more about k8s on [kubernetes](https://kubernetes.io/zh/docs/setup/)
