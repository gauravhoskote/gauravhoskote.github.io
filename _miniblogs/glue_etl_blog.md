---
title: "AWS Glue ETL jobs"
date: 2025-08-25
excerpt: ""
collection: miniblogs
---

# Building Scalable ETL Pipelines with AWS Glue and Terraform

Data pipelines are the backbone of modern data platforms. With AWS Glue, you can run fully managed ETL (Extract, Transform, Load) jobs without worrying about provisioning servers or managing clusters. And with Terraform modules, you can codify, version, and automate your Glue jobs for reliable deployment.

In this blog, we’ll walk through two types of Glue jobs implemented using Terraform in the following repo: [Code Repository](https://github.com/gauravhoskote/glue_etl_terraform/)

- **Glue ETL Job (Spark-based)**
- **Glue Shell Job (Lightweight Python/Unix tasks)**

---

## 1. Glue ETL Job Module (Spark-based)

This module provisions a standard Glue job that runs on top of Apache Spark. It’s best for heavy-duty data transformations, such as joining multiple datasets, cleaning large-scale raw data, or writing to data warehouses like Redshift, Snowflake, or S3 in Parquet format.

### Key Parameters Explained

- **glue_version** → Defines the runtime (e.g., 4.0 for Spark 3).
- **admin_bucket** → S3 bucket where Glue logs, scripts, and artifacts are stored.
- **role_arn** → IAM role with permissions to access data sources/destinations.
- **job_name** → Logical identifier for the Glue job.
- **connections** → Any JDBC or networking connections Glue requires.
- **deploy_key & path_to_script** → The Python ETL script (stored in S3 + local path for development).
- **number_of_workers & worker_type** → Defines compute resources (e.g., Standard vs. G.1X).
- **timeout** → Job execution limit (e.g., 14400 sec = 4 hours).
- **default_arguments** → Extra Glue configs (bookmarks, job parameters, etc.).
- **repository & created_by** → Metadata tags for governance.

**Use case**: Transforming terabytes of clickstream data from S3 into partitioned Parquet datasets for analytics.

---

## 2. Glue Shell Job Module (Lightweight jobs)

Sometimes, you don’t need a full Spark cluster to run your task. Glue Shell jobs are perfect for lightweight Python or Unix shell scripts, such as invoking APIs, loading data into Postgres, or preprocessing small datasets.

### Key Parameters Explained

- **admin_bucket** → Same as above, used for logs and artifacts.
- **role_arn** → Execution role for AWS access.
- **job_name** → Identifier for this shell job.
- **deploy_key & path_to_script** → Python/shell script location.
- **max_capacity** → Minimum resource allocation (as low as 0.0625 DPU).
- **connections** → Optional database/network connections.
- **default_arguments** → Pass dependencies (e.g., `"--additional-python-modules" = "psycopg2-binary"` for Postgres).
- **created_by** → Audit metadata.

**Use case**: A small Python job that extracts data from Postgres nightly and writes it to S3 before being processed by a larger Spark ETL job.

---

## 3. Using the Modules

The two modules serve different purposes and can be used independently based on your workload:

- **Glue Shell Job** → Best for lightweight tasks such as API calls, database extracts, or running small Python scripts with minimal infrastructure.
- **Glue ETL Job (Spark-based)** → Suited for large-scale transformations, joins, and heavy ETL workloads where distributed compute is required.

Both modules follow the same design principles:

- Parameterized inputs (`job_name`, `worker_type`, `path_to_script`, etc.)
- Reusable Terraform definitions
- Consistent metadata tagging (`repository`, `created_by`) for governance

This approach lets you mix and match Glue jobs based on your needs without being forced into a specific sequence. Some workflows may only require a Spark-based ETL job, while others may only need a lightweight shell job — or you might use both in different contexts within the same data platform.

---

## Final Thoughts

Terraform makes Glue ETL jobs reproducible and scalable, while separating concerns between lightweight shell jobs and full Spark ETL jobs. Whether you’re building a real-time ingestion pipeline or nightly batch processing system, this modular approach helps you design pipelines that are maintainable, cost-optimized, and cloud-native.
