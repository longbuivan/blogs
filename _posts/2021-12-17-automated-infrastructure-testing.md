---
layout: post
title: "Automated Infrastructure Testing"
date: 2021-10-17 15:36:00 +0700
categories: Project
tags:
  - data-engineering
  - infrastructure
  - go
---
# Automated Infrastructure Testing

## Description

To run test on localstack whenever deployment

## Ongoing work

1. Build localstack with Docker Compose
2. Create and Deploy AWS Infrastructure with Terraform
3. Testing Terraform with Golang

## Requirement

- Docker
- Terraform
- Go

## How to run

- Install required software
- Run `sh build.sh` .

---

## Code Implementation

Folder Template:

    ├── README.md
    ├── build.sh
    ├── docker-compose.yml
    ├── terraform
    │   ├── main.tf
    │   ├── output.tf
    │   ├── s3.tf
    │   ├── terraform.tfstate
    │   ├── terraform.tfstate.backup
    │   └── variables.tf
    └── test
        ├── go.mod
        ├── go.sum
        └── s3_test.go

Run testing with **build.sh** file which would included in CICD pipeline

    #!/bin/bash

    echo "starting localstack on docker"
    docker-compose up -d


    echo "running Unit Test for Terraform"
    export GO111MODULE=on
    cd test
    go mod init terraform/test 
    go mod tidy
    go test -v -timeout 30m
