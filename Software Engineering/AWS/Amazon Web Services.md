
*Amazon Web Services* is the first and by far the most used cloud computing service in the world. It encompasses services for almost any possible use case and is the de-facto choice for deploying software in the modern era. I thought it would be best to go through actual AWS services rather than a generic "Cloud" overview. For other services like GCP and Azure, you can generally find 1:1  equivalents to AWS services. I recognize this section more than most will need to be updated regularly.

AWS in essence at its core is [cloud computing](AWS%20Compute.md), [cloud storage](AWS%20Data%20&%20Storage.md), and [networking](AWS%20Networking.md) for quickly creating applications that can scale massively. AWS also comes with many [integration](AWS%20Integration.md) services that allow you to have durable microservice architectures and complex applications.

## IAM

A fundamental concept is Identity and Access Management (IAM). This is how different users and service accounts are given access to different services and resources. A key rule in cloud environments is to give as little permissions as possible to users and applications. This minimizes security risk in case an application is compromised.

#### Principal
A *Principal* is a human user or workload that can make a request for an action or operation on an AWS resource. After authentication, the principal can be granted either permanent or temporary credentials to make requests to AWS, depending on the principal type. IAM users and root user are granted permanent credentials, while roles are granted temporary credentials. As a best practice, it's recommended that you require human users and workloads to access AWS resources using temporary credentials

#### Policies and Permissions
You manage access in AWS by creating policies and attaching them to IAM identities (users, groups of users, or roles) or AWS resources. A *policy* is an object in AWS that, when associated with an identity or resource, defines their permissions. AWS evaluates these policies when an IAM principal (user or role) makes a request. Permissions in the policies determine whether the request is allowed or denied. Most policies are stored in AWS as [JSON](../../Internet%20&%20Networking/Serialization.md) documents. 


## CloudFormation

CloudFormation is an Infrastructure-as-Code method of defining resources. They are defined in JSON or YAML CloudFormation Templates (CFTs). This simplifies resource management by having all necessary resources defined in one document rather than manually creating each one from the console. This service also gives you repeatability as you can reuse the templates for duplication or backup. CFTs also allow you to dynamically generate multiple resources with parameters, and delete them all at once via the template. Additionally, as the CFT is code, you can more easily track changes and version control the infrastructure itself. A CFT has several sections:
- **Mapping**: mapping of common AWS URI's and definitions to variables e.g. m5.xlarge to its long form definition
- **Parameters**: Where user variables are defined. These can then be used when defining resources
- **Resources**: The actual definition of what resources to create and what settings and connections to establish. This field is the only mandatory one.
- **Outputs**: Information about what is being created, like `instance_id` of the created EC2 instance. Also other metadata like Availability Zone, etc.

It is best practice to make as much of your resources with CFT's as possible. Sometimes, certain resources are very complex and are easier to just do in console, but for the most part, CFTs are preferable.


## CloudWatch

All AWS services are connected to the internal logging service, *CloudWatch*. CloudWatch collects and visualized real-time logs, metrics, and event data in automated dashboards. There are several sections available in CloudWatch:
- **Metrics**: Utilization metrics and and custom evaluation metrics you can set like on [ML Model](../../Machine%20Learning/ML%20Metrics.md)
- **Logs**: The actual logs being generated from the services. Aggregated logs become metrics.
- **Alarms**: You can set an automatic alarm if a metric hits a target number or any other event. You can set an alarm to trigger a [SNS](AWS%20Integration.md) topic to notify people.

In keeping with the idea of having all resources on CloudFormation, you can set metrics and alarms in the CloudFormation Template alongside resources.


## Tags

You can put *tags* on every resources to group them. This is mostly done for billing purposes to know where certain use cases are going and how to charge them.