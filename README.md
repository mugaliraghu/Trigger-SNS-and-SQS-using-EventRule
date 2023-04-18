# Trigger-SNS-and-SQS-using-EventRule
## Prerequisites
  1. install aws cli and configure it using access key and secret key id.
  2. install SAM cli.
  
## Objective

This template creates several resources, including an SNS topic, an SQS queue, and an EventBridge rule 
that filters for certain events and sends those events to the SNS topic and SQS queue. The resources are defined 
using the AWS Serverless Application Model (SAM) specification, which is an extension of CloudFormation that simplifies 
the process of building serverless applications on AWS.

This CloudFormation template is useful for creating a serverless application on AWS that uses EventBridge to filter and route events to 
an SNS topic and an SQS queue. 

SNS (Simple Notification Service) is a fully managed messaging service provided by AWS that enables the publishing and subscribing of messages 
from various services or systems. 
SQS (Simple Queue Service) is a fully managed message queuing service that enables decoupling and scaling of microservices, 
distributed systems, and serverless applications. 
EventBridge is a serverless event bus that can be used to integrate AWS services, SaaS applications, and custom applications.

Using this template, you can define an EventBridge rule that filters for certain events and sends those events to an SNS topic and an SQS queue. 
The SNS topic can be used to send notifications to subscribers via email, SMS, or other protocols, while the SQS queue can be used to store
 and process the messages asynchronously. This can be useful for building real-time monitoring and alerting systems, as well as decoupled and 
 scalable serverless architectures.

navigate to the file where  template.yaml file is presenet and do
```
sam build
```
after doing sam build you will get output as shown in below image.
![sam build1](https://user-images.githubusercontent.com/120295902/232377034-60c738eb-be1d-406e-b6ff-dfe0236e632a.png)

In the next step use this below command to deploy the application on aws,while giving this command it asks some information like name of the cloudformation stack, region, IAM role creation etc.
```
sam deploy --guided
```
after that it will create resource that you added in template.yaml file like creating SNS topic,SNS Subscription, SQS queue, Eventrule and required policies as shown in below image.
![Sam deploy1](https://user-images.githubusercontent.com/120295902/232377975-9dc604bf-9cc1-4b2f-af0f-84222a0e1b15.png)

if you go inside the aws management console cloudformation you can cross verify the resources that has been created.
![Screenshot (26)](https://user-images.githubusercontent.com/120295902/232379994-bfe49fdf-6527-450f-a75b-d0500de08657.png)

## validation steps
in this i created a eventrule that if is there any changes in the instance state i need to get a notification. i have been added a two targets here one is SNS with subscription as a my mail and another SQS queue.
How i will get the notification as shown in below images.
### The image it is in stopped state
<img width="842" alt="instance_stopped state" src="https://user-images.githubusercontent.com/120295902/232736384-cd47ba2d-4f33-4bc9-b4f9-3bd886d8f569.png">

### The screenshorts that i got a notification form SNS topic and Email subscription.
![Screenshot (28)](https://user-images.githubusercontent.com/120295902/232382197-761c170a-aed5-4af0-b4b2-6cfe8f4bbd97.png)

![Screenshot (32)](https://user-images.githubusercontent.com/120295902/232382345-afc8426a-4de6-43ff-a60d-47c503fbae0c.png)
### The screenshorts that i got form SQS queue.
![Screenshot (30)](https://user-images.githubusercontent.com/120295902/232382754-bfc0a90a-f4a4-4ef1-95c8-9ceb646f907d.png)

![Screenshot (29)](https://user-images.githubusercontent.com/120295902/232382572-6ea99cba-610c-4368-a24c-b7fc8bc80640.png)

if you wnat to delete the cloudformation stack, use the below command

```t
sam delete <name of the stack>
```
