---
aliases: ["posts", "articles", "blog", "ops", "docs", "kubernetes"]
title: "Quickly Deploy Grafana to Kubernetes"
author: "rogers"
date: 2023-03-29T11:06:57+08:00
slug: "helm-grafana"
summary: "Quickly Deploy Grafana to Kubernetes"
tags: ["k8s", "kubernetes", "helm", "grafana"]
---

Grafana is a cross-platform, open-source tool for metric analysis and visualization, primarily used for querying and displaying collected data.

It provides a wealth of visualization options, including rapidly adaptable client-side charts, panel plugins for visualizing metrics and logs in different ways, and rich dashboard plugins, such as heat maps, line charts, and graphs.

Grafana helps users quickly view and edit the front-end of their dashboards, supporting multiple data sources simultaneously, and is capable of transforming time series database (TSDB) data into stunning charts.

## Preparation

- [Kubernetes](https://kubernetes.io/docs/tasks/tools/)
- [Helm](https://helm.sh/docs/intro/install/)
- [Postgresql](https://www.postgresql.org/) (optional)
- [Kubeapps](https://github.com/vmware-tanzu/kubeapps) (optional)

## Installation

### Import Bitnami Repository

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
```

### Prepare Chart Values

The complete original values can be viewed using the helm command:

```bash
helm show values bitnami/grafana
```

Areas to be modified:

```yaml
config:
  useGrafanaIniFile: true
  grafanaIniConfigMap: "grafana-config"
grafana:
  # Store additional environment variables, such as database password environment variables
  extraEnvVarsSecret: "grafana"
  # Change to the desired value, here set to AliCloud
  storageClass: "alicloud-disk-available"
  # Modify the PV size, AliCloud's minimum is 20Gi, anything smaller cannot be created
  size: 20Gi
```

grafana-config

```yaml
apiVersion: v1
data:
  grafana.ini: |-
    [database]
    type = postgres
    # Change to the actual value
    host = "YOUR_PG_HOST"
    # Change to the actual database username
    name = grafana
    # Change to the actual database password
    user = grafana
    # ConfigMaps are not suitable for storing password-related information. Here, env is used to store the database password, which can be used with extraEnvVarsSecret
    password  = $__env{PG_PASSWORD}
kind: ConfigMap
metadata:
  name: grafana-config
  namespace: monitor
```

### LDAP

If you need to use LDAP for unified login, LDAP configuration needs to be set up:

```yaml
ldap:
  ##  ldap.enabled Enable LDAP for Grafana
  ##
  enabled: true
  ##  ldap.secretName Name of the Secret with the ldap.toml configuration file for Grafana
  ## NOTE: When it's set the ldap.configuration parameter is ignored
  ##
  secretName: "grafana-ldap"
```

ldap.toml

```toml
[[servers]]
# Ldap server host (specify multiple hosts space separated)
host = "ldap.my_secure_remote_server.org"
# Default port is 389 or 636 if use_ssl = true
port = 636
# Set to true if LDAP server should use an encrypted TLS connection (either with STARTTLS or LDAPS)
use_ssl = true
# If set to true, use LDAP with STARTTLS instead of LDAPS
start_tls = false
# set to true if you want to skip SSL cert validation
ssl_skip_verify = false
# set to the path to your root CA certificate or leave unset to use system defaults
# root_ca_cert = "/path/to/certificate.crt"
# Authentication against LDAP servers requiring client certificates
# client_cert = "/path/to/client.crt"
# client_key = "/path/to/client.key"

# Search user bind dn
bind_dn = "cn=admin,dc=grafana,dc=org"
# Search user bind password
# If the password contains # or ; you have to wrap it with triple quotes. Ex """#password;"""
bind_password = "grafana"
# We recommend using variable expansion for the bind_password, for more info https://grafana.com/docs/grafana/latest/setup-grafana/configure-grafana/#variable-expansion
# bind_password = '$__env{LDAP_BIND_PASSWORD}'

# Timeout in seconds. Applies to each host specified in the 'host' entry (space separated).
timeout = 10

# User search filter, for example "(cn=%s)" or "(sAMAccountName=%s)" or "(uid=%s)"
# Allow login from email or username, example "(|(sAMAccountName=%s)(userPrincipalName=%s))"
search_filter = "(cn=%s)"

# An array of base dns to search through
search_base_dns = ["dc=grafana,dc=org"]

# group_search_filter = "(&(objectClass=posixGroup)(memberUid=%s))"
# group_search_filter_user_attribute = "distinguishedName"
# group_search_base_dns = ["ou=groups,dc=grafana,dc=org"]

# Specify names of the LDAP attributes your LDAP uses
[servers.attributes]
member_of = "memberOf"
email =  "email"
```

```
kubectl create secret generic grafana-ldap --from-file=ldap.toml=ldap.toml
```

### 执行安装

```bash
helm install ${RELEASE_NAME} bitnami/grafana -f values.yaml
```

可以看到下面这段输出

```text
CHART NAME: grafana
CHART VERSION: x.x.x
APP VERSION: x.x.x

** Please be patient while the chart is being deployed **

1.  Get the application URL by running these commands:
    echo "Browse to [http://127.0.0.1:8080](http://127.0.0.1:8080/)"
    kubectl port-forward svc/grafana 8080:3000 &

1.  Get the admin credentials:

    echo "User: admin"
    echo "Password: $(kubectl get secret grafana-admin --namespace monitor -o jsonpath="{.data.GF_SECURITY_ADMIN_PASSWORD}" | base64 -d)"
```

如果使用了 ldap，可以直接使用 ldap 账号密码登录了。

## 结果

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7a85b94728ff4c57af2489eb5cb73a1c~tplv-k3u1fbpfcp-watermark.image?)
