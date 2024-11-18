---
title: "Use DataGrip to connect the production database via the AWS system manager"
layout: post
guid: 8ybkiU6ztJhtvuJR5Pda
date: 2023-05-23
tags:
  -
---


## What is DataGrip?

DataGrip is a database management tool developed by JetBrains. It's designed for database administrators, developers, and analysts who work with various databases including SQL, NoSQL, and cloud databases.



## How do people usually use the DataGrip to connect their production databases?

Connecting to production databases requires security and proper precautions, especially when you're dealing with sensitive data. A jumphost (sometimes called a "bastion host" or "jump server") is an intermediary host or a gateway between your local environment and the production database server, adding an extra layer of security.


## AWS System Manager

AWS Systems Manager (SSM) Session Manager has become a recommended way to securely manage EC2 instances and some on-premises instances without the need to open inbound ports, maintain bastion hosts, or manage SSH keys. This method is more secure and aligns better with AWS's best practices.

When using SSM Session Manager in conjunction with databases, typically you won't SSH directly into the database. Instead, you'd SSH into the EC2 instance where the database resides or a management instance in the same VPC, and then connect to the database locally.



## How to connect the AWS EC2 though the AWS System Manager?

Prerequisites:

- Ensure you have the AWS Command Line Interface (CLI) installed.
- The EC2 instance needs an IAM role with permissions for Systems Manager.
- The AmazonEC2RoleforSSM role can be attached to the EC2 instance to give it necessary permissions.
- Ensure the AWS Systems Manager Agent (SSM Agent) is installed and running on the EC2 instance. Newer Amazon Machine Images (AMIs) come with the agent pre-installed.

Using the AWS CLI:

Use the following command to start a session:

```bash
aws ssm start-session --target instance-id
```


## How to connect the production database though AWS system manager in the DataGrip?



### Step1: config the ProxyCommand in the ~/.ssh/config


```

Host ec2-server-1
  User ec2-user
  HostName i-H3A6J1iigi9zb*****
  ProxyCommand sh -c "/opt/homebrew/bin/aws ec2-instance-connect send-ssh-public-key --profile=aws_profile_name --instance-id %h --instance-os-user %r --ssh-public-key 'ssh-ed25519 the_content_of_public_key' --availability-zone '$(/opt/homebrew/bin/aws ec2 describe-instances --instance-ids %h --query 'Reservations[0].Instances[0].Placement.AvailabilityZone' --output text --profile=aws_profile_name)'  && /opt/homebrew/bin/aws ssm start-session --target %h --document-name AWS-StartSSHSession --parameters --profile=aws_profile_name 'portNumber=%p'"
```

Note:

`--profile`: aws profile name

`--ssh-public-key`: the public key you are using.




## How to debug the SSH problem for Datagrip?



2023-05-23_16-27-33.png




Specify the following paramter to enable the debug level log for ssh related operations.


```

#com.intellij.ssh
#com.jetbrains.plugins.webDeployment

```



2023-05-23_16-27-33.png



Then, you can find the log file though 


2023-05-23_16-31-33.png


tail the log 


You will find the errors in the log and fix it.

2023-05-23_16-35-58.png

2023-05-23_16-34-57.png




## Reference

1. [DataGrip: com.intellij.execution.ExecutionException: SSH: net.schmizz.sshj.transport.TransportException: Server closed connection during identification exchange](https://youtrack.jetbrains.com/issue/DBE-18134/DataGrip-com.intellij.execution.ExecutionException-SSH-net.schmizz.sshj.transport.TransportException-Server-closed-connection)
