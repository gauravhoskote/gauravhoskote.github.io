---
title: "AWS Step Functions - Orchestrating Your Workflows in the Cloud"
date: 2025-08-09
excerpt: ""
collection: miniblogs
---

When you’re building modern cloud applications, there’s often a need to stitch together multiple AWS services and tasks into a single, reliable flow. AWS Step Functions is Amazon’s serverless orchestration service that lets you do exactly that — without writing glue code or worrying about scaling.

---

## What is AWS Step Functions?

AWS Step Functions is a fully managed workflow orchestration service. It allows you to coordinate multiple AWS services into serverless workflows, making it easier to build and manage complex applications.

You define workflows as **state machines**, where each step (or *state*) performs a specific task — like invoking a Lambda function, starting a Glue job, or running an ECS task.

The key benefits include:
- **No servers to manage** — it’s serverless.
- **Visual workflow editor** to easily design and debug.
- **Automatic error handling and retries**.
- **Built-in integrations** with over 200 AWS services.

---

## What Can You Use It For?

AWS Step Functions is widely used for:
1. **Data Processing Pipelines** — chaining together ETL jobs, transformations, and storage operations.
2. **Machine Learning Workflows** — automating training, evaluation, and deployment steps.
3. **Event-Driven Applications** — responding to incoming events with orchestrated actions.
4. **Long-Running Business Processes** — approvals, multi-step user workflows, etc.

---

## Two Flavors: Standard vs. Express

AWS Step Functions comes in **two execution modes**:

- **Standard Workflows**  
  - Best for long-running workflows (up to 1 year).
  - Durable and can handle complex retries and human approval steps.
  - Charged per state transition.

- **Express Workflows**  
  - Designed for high-volume, short-duration workflows (up to 5 minutes).  
  - Lower latency and cheaper for short-lived workloads.
  - Charged per request and execution duration.


---

## Pricing Basics

Pricing depends on the workflow type:
- **Standard**: pay per state transition.
- **Express**: pay per request and execution time.

Always choose the type based on your workload characteristics.

---

To make things easy, I have already created a Terraform repository that can be used to create AWS Step Functions.  

📦 **Repository link:** [aws-step-functions](https://github.com/gauravhoskote/aws-step-functions)

---

## Example: Calling the Step Functions Module

```hcl
module "hello_world_stepfunction" {
   source               = "./modules/stpfnc"
   name                 = "hello-world-stepfunction"
   role_arn             = aws_iam_role.stepfn_role.arn
   stepfunc_type        = "STANDARD"
   # log_group_arn       = aws_cloudwatch_log_group.stepfn_logs.arn
   definition_file_path = "statemachines/sample_hello_world.json"
}
```


| Field                            | Description                                                                                                                                                               |
| -------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **source**                       | The path to the Terraform module. In this case, `./modules/stpfnc` points to the locally stored Step Functions module within your repository.                             |
| **name**                         | The name of the Step Function state machine. This must be unique within your AWS account. Here it is set to `hello-world-stepfunction`.                                   |
| **role\_arn**                    | The ARN of the IAM role that AWS Step Functions will use to execute the state machine. This role must have the required permissions to invoke resources in your workflow. |
| **stepfunc\_type**               | The type of Step Function to create. Possible values are `"STANDARD"` (long-running, durable workflows) or `"EXPRESS"` (short-lived, high-throughput workflows).          |
| **log\_group\_arn** *(optional)* | The ARN of the CloudWatch Log Group to which execution history logs will be sent. This is currently commented out in the example.                                         |
| **definition\_file\_path**       | The relative path to the JSON or Amazon States Language file that defines the workflow steps. In this example, it points to `"statemachines/sample_hello_world.json"`.    |



Below is an example of what the json files in the 'statemachines' directory would look like:
```
{
  "Comment": "Hello World example",
  "StartAt": "Hello",
  "States": {
    "Hello": {
      "Type": "Pass",
      "Result": "Hello, World!",
      "End": true
    }
  }
}
```

Here is the [Syntax](https://docs.aws.amazon.com/step-functions/latest/dg/concepts-amazon-states-language.html) for the Amazon States Language which will help you write more complex workflows.

## My Take
Whenever you are able to model your problem into a state machine, you should seriously consider using Step Functions.

The first thing that came to my mind was the state machine I saw when I was working at PhonePe (a payments company in India). The transaction flow in every payments app follows a flow of the defined state machine:

Init → transaction is initiated.

In Progress → payment is being processed.

Cancelled or Completed → based on whether the fulfilment happened successfully or not.

Reconciliation → final step for financial accuracy.

This perfectly aligns with the philosophy of Step Functions—clearly defined states, transitions, and outcomes, all managed in a reliable, observable way.

I was reminded of this recently when working on a Glue job that started AWS Transcribe jobs in an async manner, with all resulting metadata (one metadata file for all the transcripts created by that Glue job) stored in S3. My plan was to use Step Functions to process these transcripts, orchestrating each step from retrieving the metadata to performing further analysis.

However, I hit a roadblock. Even though the S3 event could easily trigger a Lambda whenever a new transcript was uploaded to S3, the Lambda had no context about the bigger picture—it didn’t know where exactly to pick the metadata file from or what specifically to look for inside it. Without that context traveling along the workflow, the orchestration became harder to manage.

That’s when it really clicked for me — **Step Functions shine when you design your workflow so that context moves with execution**, either passed explicitly between states or retrieved deterministically at each step. Without that, you just end up with triggers firing blindly, and your state machine loses its true advantage.
