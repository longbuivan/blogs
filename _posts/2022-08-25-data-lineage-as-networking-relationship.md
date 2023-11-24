---
layout: post
title: "Data Lineage as Networking Relationship"
date: 2022-08-25 16:06:00 +0700
categories: blog
comments: true
---
{% include _toc.html %}

As previous post, we were walk through why and principles we should follow during design and implement data pipeline. In this topic I will let you go to how data lineage be implemented as network.

## Introduction

Data lineage is one of the most critical components of a data governance strategy for data lakes. Data lineage helps ensure that accurate, complete and trustworthy data is being used to drive business decisions.

While a data catalog provides metadata management features and search capabilities, data lineage shows the full context of your data by capturing in greater detail the true relationships between data sources, where the data originated from and how it gets transformed and converged.

Different personas in the data lake benefit from data lineage:

- **For data scientists**, the ability to view and track data flow as it moves from source to destination helps you easily understand the quality and origin of a particular metric or dataset
- **Data platform engineers** can get more insights into the data pipelines and the interdependencies between datasets. Any Changes in data pipelines are easier to apply and validate because engineers can identify a job’s upstream dependencies and downstream usage to properly evaluate service impacts

## Solution and Technical Stack

### 1. Solution Scope

- Apache Spark is one of the most popular data processing framework in data lake and being used in most of data platform. The solution is send Spark Job data and Job detail data into network database via API during job runs internally.
- Graph Database is used for optimized storing and querying connected datasets.
- Graphing data modeling is used for illustrate complexity of connected datasets, the dataset are job, job_run, table, database, spark execution plan, actual execution event,etc... The result of data modeling is well understanding the complex and deep network of relationship within the data.

### 2. Technical Scope

#### 2.1. Physical Design

The follow diagram illustrates how Spark Runtime data be sending to Graph Database with installed Agent on Spark Job instance.

![alt text](/images/post/architecture.png "Architecture Diagram")

#### 2.2. Implementation

Amazon Web Service is one of favorite Cloud services in data industry because large of benefit from service. All technical service are using AWS as native solution for this project.

##### **Components**

- API Gateway supports HTTP request with routes
  - POST request for sending data to Graph Database from Spark Agent
    - stats/
    - health/
    - jobs/
    - job/{id}/
    - dags/{id}/
    - execution/{id}/
  - GET request for query data from Graph Databse to BI/App
- Lambda Serverless Function support HTTP call
  - Producer App: Be called from Agent and send data to database
  - Consumer App: Be called from BI/App
- Glue Jobs using `--extra-jar` with installed Agent and `user-jar-first` be true for trigger Glue Job using `.jar` app as the initially class.
- Neptune Database is use as network database and stores all relationship of all entity as vertex and edge.

##### **Data Modeling**

The diagram shows how vertexes and edges has a relationship together.

- Spark Job meta: Job, Job_Run, Execution_plan, Optimization
- Table meta: Database, Data source, Columns, Schema
  ![alt text](/images/post/data-modeling.png "Architecture Diagram")

#### 2.3 Technical Stack

- Workflow Management: GitHub Action
- IaC: Terraform
- Cloud Native: AWS
- Application: API, NodeJS

#### 2.4 Code Snippet

##### **API Swagger**

```json
{
  "openapi": "3.0.1",
  "info": {
    "title": "data_lineage",
    "version": "2022-08-23 04:05:10UTC"
  },
  "servers": [
    {
      "url": "https://xxxxxxx.execute-api.eu-west-2.amazonaws.com/{basePath}",
      "variables": {
        "basePath": {
          "default": "dev"
        }
      }
    }
  ],
  "paths": {
    "/dag/{id}": {
      "get": {
        "responses": {
          "default": {
            "description": "Default response for GET /dag/{id}"
          }
        },
        "x-amazon-apigateway-integration": {
          "payloadFormatVersion": "2.0",
          "type": "aws_proxy",
          "httpMethod": "POST",
          "uri": "arn:aws:apigateway:eu-west-2:lambda:path/2015-03-31/functions/arn:aws:lambda:eu-west-2:085882858205:function:data_lineage/invocations",
          "connectionType": "INTERNET"
        }
      },
      "parameters": [
        {
          "name": "id",
          "in": "path",
          "description": "Generated path parameter for id",
          "required": true,
          "schema": {
            "type": "string"
          }
        }
      ]
    },
    "/job/{id}": {
      "get": {
        "responses": {
          "default": {
            "description": "Default response for GET /job/{id}"
          }
        },
        "x-amazon-apigateway-integration": {
          "payloadFormatVersion": "2.0",
          "type": "aws_proxy",
          "httpMethod": "POST",
          "uri": "arn:aws:apigateway:eu-west-2:lambda:path/2015-03-31/functions/arn:aws:lambda:eu-west-2:085882858205:function:data_lineage/invocations",
          "connectionType": "INTERNET"
        }
      },
      "parameters": [
        {
          "name": "id",
          "in": "path",
          "description": "Generated path parameter for id",
          "required": true,
          "schema": {
            "type": "string"
          }
        }
      ]
    },
    "/jobs": {
      "get": {
        "responses": {
          "default": {
            "description": "Default response for GET /jobs"
          }
        },
        "x-amazon-apigateway-integration": {
          "payloadFormatVersion": "2.0",
          "type": "aws_proxy",
          "httpMethod": "POST",
          "uri": "arn:aws:apigateway:eu-west-2:lambda:path/2015-03-31/functions/arn:aws:lambda:eu-west-2:085882858205:function:data_lineage/invocations",
          "connectionType": "INTERNET"
        }
      }
    },
    "/status": {
      "get": {
        "responses": {
          "default": {
            "description": "Default response for GET /status"
          }
        },
        "x-amazon-apigateway-integration": {
          "payloadFormatVersion": "2.0",
          "type": "aws_proxy",
          "httpMethod": "POST",
          "uri": "arn:aws:apigateway:eu-west-2:lambda:path/2015-03-31/functions/arn:aws:lambda:eu-west-2:085882858205:function:data_lineage/invocations",
          "connectionType": "INTERNET"
        }
      },
      "head": {
        "responses": {
          "default": {
            "description": "Default response for HEAD /status"
          }
        },
        "x-amazon-apigateway-integration": {
          "payloadFormatVersion": "2.0",
          "type": "aws_proxy",
          "httpMethod": "POST",
          "uri": "arn:aws:apigateway:eu-west-2:lambda:path/2015-03-31/functions/arn:aws:lambda:eu-west-2:085882858205:function:DataLineageProducer/invocations",
          "connectionType": "INTERNET"
        }
      }
    },
    "/execution-events": {
      "post": {
        "responses": {
          "default": {
            "description": "Default response for POST /execution-events"
          }
        },
        "x-amazon-apigateway-integration": {
          "payloadFormatVersion": "2.0",
          "type": "aws_proxy",
          "httpMethod": "POST",
          "uri": "arn:aws:apigateway:eu-west-2:lambda:path/2015-03-31/functions/arn:aws:lambda:eu-west-2:085882858205:function:DataLineageProducer/invocations",
          "connectionType": "INTERNET"
        }
      }
    },
    "/execution-failure": {
      "post": {
        "responses": {
          "default": {
            "description": "Default response for POST /execution-failure"
          }
        },
        "x-amazon-apigateway-integration": {
          "payloadFormatVersion": "2.0",
          "type": "aws_proxy",
          "httpMethod": "POST",
          "uri": "arn:aws:apigateway:eu-west-2:lambda:path/2015-03-31/functions/arn:aws:lambda:eu-west-2:085882858205:function:DataLineageProducer/invocations",
          "connectionType": "INTERNET"
        }
      }
    },
    "/execution-plans": {
      "post": {
        "responses": {
          "default": {
            "description": "Default response for POST /execution-plans"
          }
        },
        "x-amazon-apigateway-integration": {
          "payloadFormatVersion": "2.0",
          "type": "aws_proxy",
          "httpMethod": "POST",
          "uri": "arn:aws:apigateway:eu-west-2:lambda:path/2015-03-31/functions/arn:aws:lambda:eu-west-2:225656930256:function:data_lineage/invocations",
          "connectionType": "INTERNET"
        }
      }
    }
  },
  "x-amazon-apigateway-importexport-version": "1.0"
}
```

##### **Producer**

```python
# Init Environment, Fast App API, Graph Database Connection
API_STAGE = os.environ["API_STAGE"]
NEPTUNE_CLUSTER_ENDPOINT = os.environ["NEPTUNE_CLUSTER_ENDPOINT"]
NEPTUNE_CLUSTER_PORT = os.environ["NEPTUNE_CLUSTER_PORT"]
logger.info(f"Neptune endpoint: {NEPTUNE_CLUSTER_ENDPOINT}:{NEPTUNE_CLUSTER_PORT}")

app = FastAPI(root_path=f"/{API_STAGE}")
GremlinUtils.init_statics(globals())
gremlin_utils = GremlinUtils()

# Create route for parsing JOB/DAG data and write into Graph Database
@app.post("/execution-plans")
def execution_plans(body: Dict[str, Any] = Body(...)):
    conn = gremlin_utils.remote_connection()
    g = gremlin_utils.traversal_source(connection=conn)
    logger.info(f"Connected to Graph DB and init traersal_source")
    execution_id = body["id"]

    # Job
    job = parseJob(body)

    jobName = job["name"]
    jobDetails = getGlueJob(jobName)
    logger.info(f"Got job details from Glue API: {jobDetails}")
    job["details"] = json.dumps(jobDetails, default=str)

    jobV = upsertVertex(
        g.V().has(VERTEX_LABEL_JOB, "name", jobName),
        VERTEX_LABEL_JOB,
        job
    ).next()

    # Job run
    jobRun = parseJobRun(body)

    jobRunDetails = getGlueJobRun(job["name"], jobRun["id"])
    logger.info(f"Got job run details from Glue API: {jobRunDetails}")
    jobRun["details"] = json.dumps(jobRunDetails, default=str)

    jobRunV = upsertVertex(
        g.V().has(VERTEX_LABEL_JOB_RUN, "run_id", jobRun["id"]),
        VERTEX_LABEL_JOB_RUN,
        jobRun
    ).next()
    logger.info(f"Performed upsert for job run {jobRunV}")

    # add if not exist a "schedule" edge from "job" to "job run"
    g.V(jobV).as_("v") \
        .V(jobRunV) \
        .coalesce(
        __.inE("schedule").where(__.outV().as_("v")),
        __.addE("schedule").from_("v")
    ).iterate()
    logger.info(f"Updated 'schedule' edge from 'job' to 'job run'")

    # Execution plan
    executionPlan = parseExecutionPlan(body)
    logger.info(f"Parsed execution plan: {executionPlan}")

    executionPlanV = addVertex(g, VERTEX_LABEL_EXECUTION_PLAN, executionPlan).next()
    logger.info(f"Added execution plan {executionPlanV}")

    g.V(jobRunV).addE("has").to(executionPlanV).next()
    logger.info(f"Updated 'has' edge from 'job run' to 'execution plan'")

    ## Parse DAG
    operations = parseDAG(body)
    logger.info(f"Parsed DAG, operations: {operations}")

    operationVertices = {}
    for opId, operation in operations.items():
        operationType = operation["type"]
        operationProps = dict(operation)
        operationProps.pop("table", None)
        operationProps.pop("children", None)
        operationV = addVertex(g, operationType, operationProps).next()
        operationVertices[opId] = operationV

        if "table" in operation:  # we have table for read & write ops
            table = operation["table"]
            databaseName = table["database"]
            databaseV = upsertVertex(
                g.V().has(VERTEX_LABEL_DATABASE, "name", databaseName),
                VERTEX_LABEL_DATABASE,
                {"name": databaseName}
            ).next()
            tableV = upsertVertex(
                g.V().has(VERTEX_LABEL_TABLE, "name", table["name"]).has(VERTEX_LABEL_TABLE, "database", databaseName),
                VERTEX_LABEL_TABLE,
                table
            ).next()

            g.V(tableV).as_("v") \
                .V(databaseV) \
                .coalesce(
                __.inE("belongs_to").where(__.outV().as_("v")),
                __.addE("belongs_to").from_("v")
            ).iterate()

            g.V(tableV).addE("used_by").to(operationV).next()
            if operation["type"] == "WRITE_OP":
                g.V(executionPlanV).addE("produce").to(tableV).next()
            elif operation["type"] == "READ_OP":
                g.V(tableV).addE("consumed_by").to(executionPlanV).next()

    for opID, operation in operations.items():
        if operation["type"] == "WRITE_OP":
            g.V(executionPlanV).addE("has").to(operationVertices[opID]).next()
        if "children" in operation:
            for child in operation["children"]:
                g.V(operationVertices[child]).addE("parent_of").to(operationVertices[opID]).next()

    logger.info("Finished execution_plans")
    conn.close()
    return execution_id
```

##### **Terraform Code for Glue Job**

```terraform
module "glue_job_ingest_sap_data" {
  source           = "./modules/etl_glue_job"
  name             = "ingesting_sap_raw_data"
  role_arn         = aws_iam_role.glue_execution_role.arn
  script_file_path = "src/glue/ingest_sap_data.py"
  s3_bucket_name   = aws_s3_bucket.glue.bucket
  lineage_endpoint = aws_apigatewayv2_stage.data_lineage.invoke_url
  jars             = "s3://${aws_s3_bucket_object.agent_jar.bucket}/${aws_s3_bucket_object.agent_jar.key}"
  job_parameters = {
    "--OUTPUT_LOCATION": "s3://${module.s3_bucket.bucket}/employee/"
  }
}
```

##### **Tarraform Code for Docker Image**

```terraform
resource "null_resource" "ecr_image" {
  triggers = {
    src_hash = data.archive_file.lambda.output_sha
  }
  provisioner "local-exec" {
    command = <<EOF
      aws ecr get-login-password --region ${local.region} \
        | docker login --username AWS --password-stdin ${local.account_id}.dkr.ecr.${local.region}.amazonaws.com
      cd ${path.module}/src/lambda
      docker build -t ${aws_ecr_repository.repo.repository_url}:${local.ecr_image_tag} --platform=linux/amd64 .
      docker push ${aws_ecr_repository.repo.repository_url}:${local.ecr_image_tag}
    EOF
  }
}
```

#### 2.4 Limitation

- Current API Application are using Lambda function as quickly response Request from Agent.
- Lambda Function limitation of execution duration, therefore it can be failed, lost data from Glue runs over 15 minutes.
- Glue Job still in public network.
- Application still using ECR, it difficult to develop and trace logs.
- Agent are open-source and need effort to develop and maintain.
- HTTP request are still slow.

## Conclusion

- As architect designs to automatically collect end-to-end data lineage of Spark ETL jobs across a data lake.
- The approach enables an understanding of metadata not only Spark Job but also for Table, helps to visualize insights and able to tune Job performance.
- The proposed solution are using Serverless and Cloud Native Solution, which are scalable and configurable for high availability and performance.
