

## SQS

*Simple Queue Service* is a managed [Message Queue](../../Distributed%20Systems/Message%20Queue.md). This lets you send, store, and receive messages between microservices and serverless applications. It comes with two variants: standard queues are best for maximum throughput and at-least-once delivery, while FIFO guarantees that messages are processed exactly once in the exact sending order

## SNS

*Simple Notification Service*  provides high-throughput [Pub-Sub Model](../../Distributed%20Systems/Pub-Sub%20Model.md) fanout messaging to many subscribers, including AWS SQS, [AWS Lambda](AWS%20Compute.md), [HTTP](../../Internet%20&%20Networking/HTTP.md) endpoints, etc. It can also send messages to users via email, mobile push notifications, and SMS.


## Step Functions

*Step Functions* is a serverless orchestration service that lets you define a number of acyclic steps to complete in a defined order. This allows you to make sure actions are completed in a order you define, like [Preprocessing](../../Machine%20Learning/ML%20Engineering/Preprocessing.md) prior to [Neural Networks](../../Machine%20Learning/Deep%20Learning/Neural%20Networks.md) training. There are two types of workflows: Standard and Express. Standard workflows are ideal for long-running, auditable workflows, as they show execution history and visual debugging. Express workflows are ideal for high-event-rate workloads, such as [Stream Data Processing](../../Distributed%20Systems/Stream%20Processing.md) and IoT data ingestion. Below are some of the typical use cases:
- Function orchestration: a workflow for completing one function at a time
- Branching: based on the output of one function, you can choose one of two or more possible functions
- Error Handling: If an error is caught in a step, we can Retry that step several times until it works
- Human in the Loop: we can have a manual review step where a human must step in and provide their response.
- Parallel Processing: We can take one function, and process five more in parallel, e.g. processing five different video resolutions to upload

Step Functions can be defined one of three ways. The classic way is to define the JSON or YAML file that is the actual step function definition. There is also a visual editor that auto-generates this JSON file. Lastly, there is a [Python](../Python/Python%20101.md) SDK that lets you define steps within Python.

## Secrets Manager

Secretes Manager gives you a secure and safe place to store sensitive information like API Keys. rather than storing sensitive information in code, you can simply make a [HTTP](../../Internet%20&%20Networking/HTTP.md) request to Secrets manager and get the information you are looking for. In other words, you can programmatically retrieve encrypted secret values at runtime. You can also integrate with [AWS RDS](AWS%20Data%20&%20Storage.md) to have the encrypted data callable from the database for key rotations and other actions.