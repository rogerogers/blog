---
title: "Kratos use nacos as config center"
author: "rogers"
date: 2022-06-28T12:56:57+08:00
slug: "kratos-use-nacos-as-config-center"
summary: "kratos is your ultimate Go microservices framework for the cloud-native era. nacos is an easy-to-use dynamic service discovery, configuration and service management platform for building cloud native applications."
tags: ["dev", "golang", "kratos", "nacos"]
---

```go
package main

import (
        "flag"
        "os"

        "ldap-manager/internal/conf"

        nacos_config "github.com/go-kratos/kratos/contrib/config/nacos/v2"
        "github.com/go-kratos/kratos/v2"
        "github.com/go-kratos/kratos/v2/config"
        "github.com/go-kratos/kratos/v2/log"
        "github.com/go-kratos/kratos/v2/middleware/tracing"
        "github.com/go-kratos/kratos/v2/transport/grpc"
        "github.com/go-kratos/kratos/v2/transport/http"
        "github.com/nacos-group/nacos-sdk-go/clients"
        "github.com/nacos-group/nacos-sdk-go/common/constant"
        "github.com/nacos-group/nacos-sdk-go/vo"
)

func main() {
        nacosHost := os.Getenv("NACOS_HOST")
        if nacosHost == "" {
                nacosHost = "127.0.0.1"
        }
        sc := []constant.ServerConfig{
                *constant.NewServerConfig(nacosHost, 8848),
        }

        namespaceId := os.Getenv("NAMESPACE_ID")

        cc := &constant.ClientConfig{
                NamespaceId:         namespaceId, //namespace id
                TimeoutMs:           5000,
                NotLoadCacheAtStart: true,
                LogLevel:            "error",
        }

        // a more graceful way to create naming client
        client, err := clients.NewConfigClient(
                vo.NacosClientParam{
                        ClientConfig:  cc,
                        ServerConfigs: sc,
                },
        )
        if err != nil {
                panic(err)
        }

        c := config.New(
                config.WithSource(
                        nacos_config.NewConfigSource(
                                client,
                                nacos_config.WithDataID("test.yaml"),
                                nacos_config.WithGroup("test"),
                                //info level is noisy, always mess your console
                                nacos_config.WithLogLevel("error"),
                        ),
                ),
        )
        //always remember close
        defer c.Close()
        //load config
        if err := c.Load(); err != nil {
                panic(err)
        }

        var bc conf.Bootstrap
        //config covert to go struct
        if err := c.Scan(&bc); err != nil {
                panic(err)
        }
        //keep sync from server
        c.Watch("data", func(key string, value config.Value) {
                value.Scan(&bc.Data)
        })
}
```
