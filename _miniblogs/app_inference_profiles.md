---
title: "Application Inference Profiles - Tracking costs in Amazon Bedrock"
date: 2025-07-25
excerpt: ""
collection: miniblogs
---

Managing and tracking costs for large language models (LLMs) can be tricky — especially when you're using the same model across multiple use cases. AWS Bedrock's Inference Profiles help you address this challenge by giving you a way to tag and organize usage at the profile level, even if the underlying model remains the same.

This is a short blog on how to use Inference Profiles for cost attribution and tracking in your LLM-powered workloads using Terraform.

Creating a Bedrock Inference Profile with Terraform
You can define an inference profile that wraps a Bedrock model using the following Terraform resource:

~~~
resource "aws_bedrock_inference_profile" "my_inference_profile" {
  name        = "sample-usecase-profile"
  description = "Cost tracking for Use Case #1"

  model_source {
    copy_from = "model_arn"  # Replace this with the ARN of the foundation model
  }

  tags = {
    created_by = "gaurav.hoskote@gmail.com"
    module_key = "sample_module_key"
    application_key = "sample_application_key"
  }
}
~~~

Exporting the Inference Profile ARN
To use this profile in your applications, you’ll want to output the ARN:

~~~
output "inference_profile_arn" {
  value = aws_bedrock_inference_profile.my_inference_profile.arn
}
~~~

Using the Inference Profiles in Code
Once you've created the inference profile and have the ARN, you can use it in your Python code as a drop-in replacement for the model ARN.

Refer to below code sample that can be used for Langchain bedrock models:

~~~
from langchain_aws import ChatBedrockConverse

foundation_model = "<your-inference-profile-arn>"

llm = ChatBedrockConverse(
    model=foundation_model,
    temperature=0,
    max_tokens=None,
    region_name='us-east-1'
)
~~~

A simple yet effective way to track costs!