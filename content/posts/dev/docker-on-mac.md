---
aliases: ["posts", "articles", "blog", "ops", "docs", "kubernetes", "mac"]
title: "Run Docker on mac use multipass"
author: "rogers"
date: 2022-06-17T12:56:57+08:00
slug: "run-docker-on-mac-use-multipass"
summary: "run you container on mac, use linux vm by multipass"
tags: ["linux", "mac", "multipass"]
---

Docker desktop costs so much to run

## Install

[multipass](https://multipass.run/docs/installing-on-macos)

```bash
brew install --cask multipass
# specific resource, default cpu 1, default disk 5G
multipass launch --name linux -d 10G -m 4
# shortcut, alias make command easy
alias m=multipass
m launch linux
```

> Here i use [homebrew](https://brew.sh/). You can download directly [multipass release](https://github.com/canonical/multipass/releases)

Now you are in linux env, you login by ubuntu with sudo permission

```bash
sudo apt install docker docker.io
#or you prefer containerd
sudo apt install containerd crictl
```

Use Docker command to manage your container
You can mount your local path into instance

```bash
# pwd $HOME
m mount -u 501:1000 $HOME/Public linux01:/tmp/public
# pay attention on the policy of you mount dir
```
