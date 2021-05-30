# The Good Parts of AWS

Notes on The Good Parts of AWS by Daniel Vassallo and Josh Pschorr.

# The Good Parts

* Default Heuristic - have a basket of default choices for problems. It should be a reliable option,
  unlikely to fail you, that you can be confident in. Only deviate from your defaults when you
  absolutely must.
* DynamoDB - a highly-durable data structure in the cloud (partitioned B-tree). Like Redis, but
  immediately consistent and highly durable. Can replace a relational database. But much faster.
  But query processing gets pushed to the application side. Also more expensive.
* S3 - object storage. Highly durable, easy to use, practically infinite bandwidth. You cannot append
  to objects. Bucket names are globally unique, so one you want might be taken.
* EC2 - complete computer in the cloud, similar to your own server. You don't need to adapt your
  application to it. Good default for everything.
* EC2 Auto Scaling - decide on how much headroom, use it as a cost reduction tool. Ability to add
  or remove instances by updating desired capacity setting.
* Lambda - code runner in the cloud, without an operating system or file system. Abstracts everything
  away except for a function interface. Your application code will have to be adapted to use it.
  Good for small pieces of code that rarely need to be changed, especially in response to something
  that happens in your AWS account.
* ELB - ALB for application or NLB for network. ALBs are proper reverse proxies. NLBs work by
  routing network packets. Consider using an NLB first, but ALB is a great choice too if NLBs don't
  meet your specific needs.
* CloudFormation - create or update things in AWS without clicking around. You define AWS resources
  as YAML or JSON.
* Route 53 - DNS service. Integrates well with ELB.
* SQS - highly-durable queue in the cloud. Only one consumer.
* Kinesis - highly-durable linked list in the cloud. Similar to SQS, but with Kinesis you can have
  multiple consumers. Items consumed can stay in the queue until manually removed.

# Bootstrap Guide

Objective for this chapter is to get a web app running on a single EC2 instance. This chapter will
perform the steps manually. Automating will happen in later chapters.

The authors create a Hello World node application to be hosted on AWS.

1. go to the EC2 instances section of AWS console
2. click "Launch Instance"
3. select an AMI (Amazon Machine Image, or picking an operating system), use the default
4. select the default instance type (t2.micro within the free tier)
5. keep selecting defaults until you get to "Configure Security Group"
6. add a rule to allow traffic to port 8080, it should allow TCP on port 8080
7. click "Review and Launch" and proceed without a key pair
8. click "Launch Instance"

You can connect to your EC2 instance via the browser SSH session. In the EC2 instances view, select
your instance and press the "Connect" button. From the SSH session, you can install the authors'
Hello World node application and its prerequisites. Make sure you can curl `localhost:8080`.

# Infrastructure as Code

Objective for this chapter is to recreate our infrastructure using CloudFormation. This involves:

1. installing and configuring the AWS CLI
2. creating a CloudFormation Stack
3. deploying the CloudFormation Stack

Useful documentation:

* [install AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html)
* [configure AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html)

For the book, we'll use a profile named `awsbootstrap`:

```sh
$ aws configure --profile awsbootstrap
```

Enter your Access Key ID, Secret Access Key, default region name (`us-east-1`), and default output
format (`json`). Test out your configuration by listing EC2 instances:

```sh
$ aws ec2 describe-instances --profile awsbootstrap
```

Let's make a `deploy-inra.sh` script which uses the AWS CLI to setup your stack using CloudFormation.

```sh
#!/bin/bash

STACK_NAME=awsbootstrap # name used to refer to group of resources
REGION=us-east-1
CLI_PROFILE=awsbootstrap
EC2_INSTANCE_TYPE=t2.micro

# Deploy the CloudFormation template
echo -e "\n\n======== Deploying mian.yml ========"
aws cloudformation deploy \
  --region $REGION \
  --profile $CLI_PROFILE \
  --stack-name $STACK_NAME \
  --template-file main.yml \
  --no-fail-on-empty-changeset \
  --capabilities CAPABILITY_NAMED_IAM \
  --parameter-overrides \
    EC2InstanceType=$EC2_INSTANCE_TYPE

# if deploy succeeded, show DNS name of created instance
if [ $? -eq 0]; then
  aws cloudformation list-exports \
    --profile awsbootstrap \
    --query "Exports[?Name=='InstanceEndpoint'].Value
fi
```

The template file will have three sections:

* parameters - input parameters, giving some flexibility for modifications
* resources - bulk of template, where we define resources that CloudFormation will manage for us
* outputs - return values for template

```yml
Parameters:
  EC2InstanceType:
    Type: String
  EC2AMI:
    Type: "AWS::SSM::Parameter::Value<AWS::EC2::Image::Id>" # latest AMI without exact version string
    Default: "/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2"

Resources:
  SecurityGroup:
    type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: !Sub "Internal Security group for ${AWS::StackName}" # string interpolation
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 8080
          ToPort: 8080
          CidrIp: 0.0.0.0/0
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          CidrIp: 0.0.0.0/0
      Tags:
        - Key: Name
          Value: !Ref AWS::StackName # refer to resource in your stack

    InstanceRole:
      Type: "AWS::IAM::Role"
      Properties:
        AssumeRolePolicyDocument:
          Version: "2021-10-17"
          Statement:
            Effect: Allow
            Principal:
              Service:
                - "ec2.amazonaws.com"
            Action: sts:AssumeRole
        ManagedPolicyArns:
          - arn:aws:iam::aws:policy/CloudWatchFullAccess
        Tags:
          - Key: Name
            Value: !Ref AWS::StackName

    InstanceProfile:
      Type: "AWS::EC2::Instance"
      CreationPolicy:
        ResourceSignal:
          Timeout: PT15M
          Count: 1
      Metadata:
        AWS::CloudFormation::Init:
          config:
            packages:
              yum:
                wget: []
                unzip: []
      Properties:
        ImageId: !Ref EC2AMI
        InstanceType: !Ref EC2InstanceType
        IamInstanceProfile: !Ref InstanceProfile
        Monitoring: true
        Tags:
          - Key: Name
            Value: !Ref AWS::StackName

Outputs:
  InstanceEndpoint:
    Description: The DNS name for the created instance
    Value: !Sub "http://${Instance.PublicDnsName}:8080"
    Export:
      Name: InstanceEndpoint
```
