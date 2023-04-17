# Trigger-SNS-and-SQS-using-EventRule

## What it is?
This template creates several resources, including an SNS topic, an SQS queue, and an EventBridge rule 
that filters for certain events and sends those events to the SNS topic and SQS queue. The resources are defined 
using the AWS Serverless Application Model (SAM) specification, which is an extension of CloudFormation that simplifies 
the process of building serverless applications on AWS.

## Where it is useful?
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

### This CloudFormation template creates an EventBridge rule that filters for EC2 instance state-change notifications and sends the filtered events to an SNS topic and an SQS queue. The SNS topic has a subscription for email notifications.

```t
AWSTemplateFormatVersion: '2010-09-09'
Transform: AWS::Serverless-2016-10-31
Description: Serverless patterns - EventBridge to SNS

Resources:

  # Define the SNS topic
  MySnsTopic:
    Type: AWS::SNS::Topic
    Properties:
      DisplayName: "My SNS Topic"
      TopicName: "my-sns-topic"
      Subscription:
        - Protocol: "email"
          Endpoint: "raghavendra.m@zapcg.com"

  MySqsQueue:
    Type: AWS::SQS::Queue
    Properties:
      QueueName : "MYSQSQUEUE"


  # Define the event rule to filter for events
  EventRule:
    Type: AWS::Events::Rule
    Properties:
      Description: "EventRule"
      EventPattern:
        source:
          - "aws.ec2"
        detail-type:
          - "EC2 Instance State-change Notification"
      Targets:
        - Arn: !Ref MySnsTopic
          Id: "SNStopic"
          InputTransformer:
            InputPathsMap:
              "instance" : "$.detail.instance-id"
              "state" : "$.detail.state"
            InputTemplate: |
              "instance <instance> is in <state>"
        - Arn: !GetAtt MySqsQueue.Arn
          Id: "SQSqueue"
          InputTransformer:
            InputPathsMap:
              "instance" : "$.detail.instance-id"
              "state" : "$.detail.state"
            InputTemplate: |
              "instance <instance> is in <state>"
            
          
          
  # Allow EventBridge to invoke SNS
  EventBridgeToToSnsPolicy:
    Type: AWS::SNS::TopicPolicy
    Properties:
      PolicyDocument:
        Statement:
        - Effect: Allow
          Principal:
            Service: events.amazonaws.com
          Action: sns:Publish
          Resource: !Ref MySnsTopic
      Topics:
        - !Ref MySnsTopic
        
  EventBridgeToToSqsPolicy:
    Type: AWS::SQS::QueuePolicy
    Properties:
      PolicyDocument:
        Statement:
        - Effect: Allow
          Principal:
            Service: events.amazonaws.com
          Action: SQS:SendMessage
          Resource:  !GetAtt MySqsQueue.Arn
      Queues:
        - Ref: MySqsQueue
   ```

after that navigate to the file where  template.yaml file is presenet and do
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

![Screenshot (28)](https://user-images.githubusercontent.com/120295902/232382197-761c170a-aed5-4af0-b4b2-6cfe8f4bbd97.png)

![Screenshot (32)](https://user-images.githubusercontent.com/120295902/232382345-afc8426a-4de6-43ff-a60d-47c503fbae0c.png)

![Screenshot (30)](https://user-images.githubusercontent.com/120295902/232382524-87640ffa-b937-4877-96ab-f01b648d54f8.png)

![Screenshot (29)](https://user-images.githubusercontent.com/120295902/232382572-6ea99cba-610c-4368-a24c-b7fc8bc80640.png)
