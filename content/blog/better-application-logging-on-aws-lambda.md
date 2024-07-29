+++
date = 2021-04-14T14:38:33+02:00
description = 'Learn about logging best practices for AWS Lambda'
draft = false
images = ['images/better-application-logging.webp']
title = 'Better application logging on AWS Lambda'
type = 'blog'
+++
---

In this post, I will cover the best practices for producing useful logs when deploying to AWS Lambda. I'll explain how 
AWS Lambda Powertools, an Open-source client library, can help do much of the heavy lifting for you. This post is aimed 
at developers, DevOps engineers, and anyone involved in the production or operation of systems using Lambda.

I started my tech career as a build engineer; I guess we'd call this a junior Ops role now. It was my responsibility to 
build other people's code, get it onto the servers and understand if it wasn't working correctly. You can imagine that 
logs of every kind became integral to my working life. Ant, Maven, Hudson (now Jenkins), WebLogic, Tomcat, all produced 
logs and could be a source of information on issues affecting the applications I supported.

Fast-forward to today and although the build and continuous integration tooling has changed over time, the biggest 
difference I've seen is with application logs. I used to deploy applications to two servers in production and one server
in the dev and test environments. I used to know all the server ip addresses by heart and had scripts to help me with 
common tasks. Working on a single server most of the time made life simple. If I had a problem, connect to the machine 
over ssh, grep the application log for ERROR and start debugging. I can't tell you how many times I went to the first 
production server, did the same, found nothing and scratched my head, only to then remember I needed to check the second
machine. So what does this have to do with AWS Lambda?

Well as a user of Lambda I have no idea what server my code is running on or what is happening with my application logs.
It turns out both of these are huge advantages. The placement of my code onto a server spread across multiple 
availability zones, load balanced automatically and health checked is all work I no longer have to do. Zipping log 
files, shipping them to a central location and managing storage is no longer a pain for me.

Lambda automatically integrates with Amazon CloudWatch for a fully managed logging solution which makes working with 
ephemeral execution environments easy. As an application developer all I have to do is produce my log message to 
standard out and Lambda does the rest. So in this new world what are the best practices when producing application logs?

---

Include [Lambda context](https://docs.aws.amazon.com/lambda/latest/dg/java-context.html) information. The function name, its memory allocation, its version is all useful information to 
help debug problems. Has a problem started after a new version has been released? Did the function get more expensive 
after a memory allocation change?

Include AWS Identifiers. As well as the AWS request ID, include the X-Ray trace id if you have tracing enabled. Help 
yourself join information for multiple sources by including all the IDs you'll need. This is also true for IDs for 
external or integration systems.

Structure your logs in JSON. Take advantage of the powerful querying in 
[CloudWatch Insights](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/AnalyzingLogData.html) by structuring 
your logs into JSON.

Log requests and responses. Where the workload allows, log requests to and responses from your function. This isn't 
always possible if you're working with particularly big requests such as encode images.

Include business identifiers. If the log message is about a customer, include the customer ID. If the log message is 
about a transaction failure, include the transaction ID.

Runtime configurability. Have the ability to change your logging level and sampling without having to re-release your 
function code. The ability to enable and disable DEBUG logging when you have a problem is fantastic. Since CloudWatch 
charges are based on amount ingested as well as storage it is a good cost optimization do be able to control both 
easily.

This may all sound like a lot of additional work, and it can be, but AWS Lambda Powertools supports all the 
functionality I listed above and more. Powertools currently supports 
[Java](https://awslabs.github.io/aws-lambda-powertools-java/core/logging/) and 
[Python](https://awslabs.github.io/aws-lambda-powertools-python/core/logger/), with more language support coming.

For examples of Java Lambda function handlers and the log messages they produce, you can read the 
[AWS blog post](https://aws.amazon.com/blogs/opensource/simplifying-serverless-best-practices-with-aws-lambda-powertools-java/).