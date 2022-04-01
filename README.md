# DNS Firewall Blog Post

## Introduction

Supporting code and Cloudformation templates for DNS Firewall automation with Guarduty Blog post.  
The whole setup is self contained in the Cloudformation Template including Lambda code.

The goal of this demo is to demonstrate the automation of the DNS Firewall resolver, triggered by a Security Hub event.
The Security Hub event is triggered by a Guarduty event

The cloud formation templates deploys the following: 

* Two dummy test VPC
* DNS Resolver Firewall Groups, empty Domain Lists and VPC association
* Cloudwatch Log Group to be used by the DNS Resolver Firewall Service
* Three Lambda Functions and associated IAM Roles
* A Dynamo DB Table to store the some event info.
* A Step function
* An Event Bridge event rule
* SNS Topic

## Lambda Functions and Step Functions

The Workflow defined by the Steps function performs the following when the step funtions is triggered

* Extract the Domain name in the Security Hub Event and check if it is already in the Dynamo DB
* If the domain is not in the DynamoDB, add the Domain in the DNSFirewall  Domain List ( The domain list is selected based on the event severity). The Domain Lists are associated to different actions, Block or Alert.  
* The DynamoDB Table is then updated with some of the event information.
* If the domoin is already in the DynamoDB Table, we check if the domain is already in the domain list and updated Dynamo DB with the finding ID for that domain, if not then we update and DynamoDB.
* A Success or failure  Notification is finallly sent to the SNS Topic


## Testing the solution

The solution can be tested end to end by either:

* Generating the ad hoc activities in the created VPC more on this on [Amazon Guarduty Tester](https://github.com/awslabs/amazon-guardduty-tester)
* Or by triggering the Step function state machine manually. When you are triggering the step function you will have to feed it with a sample security hub event. Few security hub test events are provided in this repo to manually simulate the attacks.
