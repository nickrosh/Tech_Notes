
## EC2

*Elastic Compute Cloud* is a virtual server service. You can select any amount of [CPU](../../Electrical%20Engineering/Digital/CPU.md) cores or [Memory & Cache](../../Electrical%20Engineering/Digital/Memory%20&%20Cache.md) depending on your needs and use case. The cost scales with the resources of the server. You can also select any supported [Operating System](../../Systems%20Software/Operating%20System.md) through the usual choice is enterprise Linux. When creating an EC2 instance, you can define a machine image template to copy off of. You can also copy existing instances to make new ones. Each EC2 instance comes with a [EBS or EFS](AWS%20Data%20&%20Storage.md) file storage. This allows you to also replicate an EBS to any number of instances. You can horizontally scale your instances to meet increased demand with Auto-Scaling Groups.


## ECS & EKS

*Elastic Container Service* is a fully managed [Containers](../Containers.md) orchestration service. It is a high level API for orchestration that lets you set rules for scaling and deployments. There is also *Elastic Kubernetes Service* which is a managed Kubernetes service which gives you the full flexibility and complexity that comes with Kubernetes. In both, you have to manage the underlying EC2 servers that the containers are running on.


## Lambda

Lambda Functions allow you to execute code serverless. This means you don't have to worry about any of the underlying infrastructure. You simply add in the code and its dependencies, or packaged together in a [Container](../Containers.md) and Lambda will handle all scaling. Dependencies can be added with "layers" which is a zip file containing the packages and dependencies. You can also just upload a container image containing the dependencies. You get double the max allowed storage size when using containers.

## Fargate

*Fargate* is a serverless version of ECS and EKS. This removes the need for managing the underlying servers and scaling rules. This makes it so you only have to set the container orchestration and deployment configuration

## Elastic Beanstalk

*Elastic Beanstalk* is a high-level web server creator, inspired by Heroku. This service allows you to easily create web servers and other applications without worrying about setting up all the settings and downloading dependencies on a server.