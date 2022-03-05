+++ 
draft = false
date = 2022-03-05T09:59:57+08:00
title = "protocol-buffers with golang"
description = "begin learn rust"
slug = "protocol-buffers-with-golang"
authors = ["rogerogers"]
tags = ["golang", "grpc"]
categories = ["golang", "grpc"]
externalLink = ""
series = []
+++

## 1.Install protoc

use apt

`sudo apt install protoc`

or download release from github direct, here i use ubuntu, for other platform, visit [protocol](https://github.com/protocolbuffers/protobuf/releases/tag/v3.19.4)

`wget https://github.com/protocolbuffers/protobuf/releases/download/v3.19.4/protoc-3.19.4-linux-x86_64.zip -o ${HOME}/.local/bin`

## 2.install protoc-gen-go

`go install google.golang.org/protobuf/cmd/protoc-gen-go@latest`

`go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@latest`

## 3.write example code

```c
syntax = "proto3";

option go_package = "proto/ecommerce";
package ecommerce;

message ProductID { string value = 1; }

message Product {
  string id = 1;
  string name = 2;
  string description = 3;
}

service ProductInfo {
  rpc addProduct(Product) returns (ProductID);
  rpc getProduct(ProductID) returns (Product);
}
```

and put this in a proto folder

## 4.Gen proto for golang and grpc

```shell
protoc --go_out=. --go_opt=paths=source_relative \
    --go-grpc_out=. --go-grpc_opt=paths=source_relative proto/product_info.proto
```

### Explains of this options

  1. go_out define where to put your code
  2. go_opt define go_out options paths=import mean create the full import path of option go_package in your proto file, and paths=source_relative will ignore this
  3. module in go_opt will sub the prefix of you go_package option

Now you can use it in you project

Just a record, i'm still learning
