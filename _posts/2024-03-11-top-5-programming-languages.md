---
layout: post
title: "Top 5 Programming Languages I would learn in 2024"
date: 2024-03-01 11:31:00 +0700
modified: ""
description: Top 5 Programming Languages I would learn in 2024
categories: Learning
# author: Long Bui Van
# github_id: longbuivan
comments: true
tags:
    - data-engineering
    - programming-languages
    - coding

---

{% include _toc.html %}

## Introduction

Hey, tech enthusiasts! As we gear up for another exciting year in the ever-evolving tech landscape, it’s time to sharpen our programming skills to stay at the forefront. Here’s a curated list of the top 5 programming languages you should consider mastering in 2024.

Watch this video for getting know why: [Youtube](https://youtu.be/fDsaUJCDSic)


## SQL with dbt Practice
Why it Matters: SQL remains a cornerstone in the world of data, and with the rising popularity of dbt (data build tool), it’s essential for anyone working with data warehousing. dbt takes your SQL skills to the next level, enabling you to transform and model data efficiently.

Other than that, the dbt will lets you into how data warehouse structures, and how to build the better data warehousing, remember that don’t be fake by what is the name of the layers, it can be everthing things and decided by purpose/functionality/component or whatever.

Check this repo out: https://github.com/longbuivan/dbt-project-structuring/tree/main


How to Master it: Dive into the fundamentals of SQL, then explore dbt for streamlined data modeling and transformation. Real-world projects and case studies will enhance your proficiency.

## Python for Data Ingestion from Websites

Why it Matters: Python’s versatility shines, particularly in web scraping and data ingestion and data manipulation. As the internet continues to be a goldmine of data, mastering Python for ingesting and processing data information is invaluable.

As today, Python is one of the mandatory skills that Data Engineer/Data Analyst/ Data Scientist / Data Practitioner HAVE to know.

Check out this repo : https://github.com/longbuivan/tiki_data_scrapper/tree/master

How to Master it: Start with core Python skills, then delve into web scraping libraries like Beautiful Soup and Scrapy. Hands-on projects, perhaps extracting data from your favorite websites, will solidify your expertise.

## Node.js for Handling APIs and Backend Services

Why it Matters: Node.js has become a go-to choice for building scalable and high-performance backend services. Mastering it is crucial for anyone involved in web development, especially in handling APIs.

I’ve working with several project of data ingestion and reverting ETL project that required me to connect with database via APIs and exposed data via APIs using NodeJs.

Besides that, The AI revolution is raising up the Application need to integrate with ChatGPT using custom API, therefore I recommend you learn NodeJs.

```javascript
app.use(function (err, _req, res) {
  console.error(err.stack);
  res.status(500).send('Something broke!');
});
var ip = require('ip');

console.log(ip.address());
// perform a database connection when the server starts
dbo.connectToServer(function (err) {
  if (err) {
    console.error(err);
    process.exit();
  }

  // start the Express server
  app.listen(PORT, () => {
    console.log(`Server is running on port: ${PORT}`);
  });
});
```
Plus: even if you wanted to build your own website, applications using NodeJs and ReactJS.

Here is website I built: What I use – LongDataDevLog

How to Master it: Begin with the basics of Node.js and then explore frameworks like Express.js. Building RESTful APIs and integrating them into frontend applications will provide practical experience.

## CLI and Bash for Interacting with Systems

Why it Matters: Command Line Interface (CLI) and Bash scripting are powerful tools for system administrators, developers, and anyone dealing with servers. Automating tasks and interacting with systems via the command line is a must-have skill.

Building Docker images, containers or K8s if you are involved in data infrastructure project and nothing there (build from scratch). Every tools and services have their APIs that we need to work along side with SDKs

This is dotfile, to Setup Development Environment as Data Engineer (github.com)

In the real world, the engineer mostly use Makefile to structure there bash and scripts, example:

```bash
#!/bin/bash

PROJECT_NAME=stringx
.PHONY: build up rebuild start stop
ps:

 docker-compose -p $(PROJECT_NAME) ps

build:

 docker-compose -p $(PROJECT_NAME) build

data-core-up:

 docker-compose -p $(PROJECT_NAME) up frontend backend postgres_db postgres_warehouse minio-service miniosetup

data-infra-up:

 docker-compose -p $(PROJECT_NAME) up monitoring_grafana collector_prometheus postgres-exporter server_node_exporter cadvisor

data-ops-up:

 sh ./../dataops/start-service.sh

 docker-compose -p $(PROJECT_NAME) up frontend backend postgres_db postgres_warehouse minio-service miniosetup

up:

 docker-compose -p $(PROJECT_NAME) up

rebuild:

 docker-compose -p $(PROJECT_NAME) up --force-recreate --build

start:

 docker-compose -p $(PROJECT_NAME) up -d

stop:

 docker-compose -p $(PROJECT_NAME) down

```

# Starting the StringX Platform
make data-core-up
How to Master it: Start with the basics of the command line, then dive into Bash scripting. Create scripts to automate routine tasks and manage server configurations.

## Go for Backend, Infrastructure, and Web3 Blockchain
Why it Matters: Go (or Golang) has gained traction for its efficiency and simplicity, making it ideal for backend development and building scalable infrastructure. Additionally, with the rise of Web3 blockchain technology, Go is becoming a key language for decentralized applications (dApps).

Why Go is the last one I highly recommend you to learn as data engineer:

Avoid Out of Memory during data processing
Automation to optimize workflow
Reduce complexiy of data transfer
Concurence to optimize execution
Support for infrastructure building and testing
Lets figure out by you

```go
func TestAwsS3Raw(t *testing.T) {
	t.Parallel()

	expectedName := fmt.Sprintf("test-raw-data-s3")

	terraformOptions := terraform.WithDefaultRetryableErrors(t, &terraform.Options{
		TerraformDir: "../terraform",

	})

	defer terraform.Destroy(t, terraformOptions)

    terraform.InitAndApply(t, terraformOptions)


	bucketID := terraform.Output(t, terraformOptions, "raw_data_s3_id")
	assert.Equal(t, expectedName, bucketID)
	
}
```

How to Master it: Begin by understanding the fundamentals of Go, then explore its applications in backend development and infrastructure. Delve into blockchain development with Go to grasp the intricacies of Web3.

## Conclusion
Embrace the new year with a strategic approach to your programming skills. Whether you’re into data, web development, system administration, or the exciting world of blockchain, these top 5 programming languages will set you on a path to success in 2024.

Which language are you most excited to master? Share your thoughts and let’s embark on this learning journey together!