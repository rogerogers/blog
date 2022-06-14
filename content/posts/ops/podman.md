---
draft: false
date: 2022-04-02T23:00:00+08:00
title: "Using podman instead docker"
description: "using podman instead docker"
slug: "using-podman-instead-docker"
authors: ["rogers"]
tags: ["docker", "ops"]
categories: ["k8s", "ops"]
externalLink: ""
series: []
---

## Install

`apt install podman`

## config

On linux platform, podman put it's config in /etc/containers

we need config registries.yaml first

fill the unqualified-search-registries config so you can searching or pulling by short names

use a local register to speed your pulling image
insecure=true allow you skipping TLS verification

```toml
[[registry]]
location="localhost:5000"
insecure=true
```

you can blocking a registry, namespace, or image by set blocked=true

### config a mirror

```toml
[[registry]]
location="registry.access.redhat.com"
[[registry.mirror]]
location="internal.registry.mirror"
```

## Run

podman has many of docker command, simplest you can just alias your docker command to podman

always run on when system reboot

use systemd

podman provide simple command to generate a systemd config

`podman generate systemd --new --name redis > /etc/systemd/system/redis.service`

`systemctl enable --now redis`

this will keep redis on when system reboot
