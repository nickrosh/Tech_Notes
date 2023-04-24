
MLOps (ML Operations) is the whole other side besides model development. It is the operations and deployment that allow Machine Learning models to be reproducible and professionally deployed. MLOps refers to the entire lifecycle starting at strong development practices, many taken from DevOps philosophy such as [CI & CD](../../Software%20Engineering/CI%20&%20CD.md) and other methods. The deployment and monitoring of models is also carefully planned.

![](../../Attachments/Pasted%20image%2020230418223255.png)

## Development

Model development requires a lot of operations assistance to make the process is repeatable and automated. This is the difference between turning a model around in a month and half a year.

#### Feature Store

One of the biggest problems in production ML is training-serving skew. Minor differences in the training and production environment can drastically change model results. These issues are also very difficult to detect. The main source of data skew is the difference in the data transformations between training and inference. While simple transformations can be handled within the model, complex transformations require external [Preprocessing](Preprocessing.md) logic.

![](../../Attachments/Pasted%20image%2020230418231701.png)

Use a *feature store* where you grab the same exact transformations for training and inference, eliminating training-serving skew. This is especially needed if you need to inject more features to client requests.

#### Experiment Tracking

Experiment Tracking is the process of saving all experiment related information that you care about for every experiment you run. This metadata can change based on the project characteristics, but the following should always be tracked:
- Code: preprocessing, training, and evaluation scripts, notebooks used for designing features, other utilities. All the code that is needed to run (and re-run) the experiment
- Environment: The easiest way to keep track of the environment is to save the environment configurations files like [Containers](../../Software%20Engineering/Containers.md) Dockerfile or [Python 101](../../Software%20Engineering/Python/Python%20101.md) pip requirements.txt.
- Data: saving data versions (as a hash or locations to data files) makes it easy to see what your model was trained on.
- Parameters: saving your hyperparameters and run configuration is critical.
- Metrics: logging [ML Metrics](../ML%20Metrics.md) for evaluation on train, validation, and test sets for every run is critical to actually ranking experiments.

#### Model Artifact Registry


![](../../Attachments/Pasted%20image%2020230411115834.png)

The *model registry* stores the validated trained model and the metadata and artifacts that go with it. This creates a central repository for models that can then be deployed to production. The best way to package model artifacts is as [Containers](../../Software%20Engineering/Containers.md), that way all the code, parameters, and environment can be cleanly packaged together.

![](../../Attachments/Pasted%20image%2020230418234342.png)


## Deployment

Putting your model into production is much more a question of operations than it is about ML. You must carefully choose how to deploy the model

#### Batch Inference

The easiest method of deployment is simply *batch inference* on an existing dataset. This way, you don't have to worry about client usage or [load balancing](../../Distributed%20Systems/Load%20Balancer.md), as all the computation will be done once and reused. The client simply queries the predictions from the database.

#### Online Inference

The more common and more difficult serving mode is *online inference*. This usually means the model is exposed as a [REST API](../../Internet%20&%20Networking/REST%20API.md) that is requested on-demand and must perform real-time  inference on single samples sent by the client. As with any service exposed as an API, we must be cognizant of the need to scale horizontally and use a [Load Balancer](../../Distributed%20Systems/Load%20Balancer.md) to share the load among multiple machines.

![](../../Attachments/Pasted%20image%2020230419000914.png)

The inference pipeline itself can be split up just like the training pipeline into preprocessing, transforming, etc. The idea is that whatever is done to the training data must be identically done to the data for inference. This will ensure there are no data leaks where the training set was "looking into the future" by looking at data it was not supposed to, which the inference data will not be able to do.


#### Deployment Strategies

Let's say you have a model in production. How can you test if a new model is better? ML metrics only tell you part of the story. The true metrics like user engagement and click-through can only be truly verified through real experimental data. To do this, we can use multiple deployment strategies to investigate and deploy new models when they beat existing ones. You can deploy multiple models to a single endpoint, and use a [reverse proxy](../../Internet%20&%20Networking/Proxy.md) to divert traffic to make special deployment schemes. The following are some of the most famous deployment strategies:

- **A/B Testing**: Compare the existing control model versus the new or multiple new experimental models. View the different metrics in your [Model Monitoring](Model%20Monitoring.md) scheme and decide which one is better
- **Blue/Green Testing**: Instead of a single endpoint with [Reverse Proxy](../../Internet%20&%20Networking/Proxy.md) routing like in A/B testing, Blue/Green testing uses a new endpoint for the new variant. You can use [DNS](../../Internet%20&%20Networking/DNS.md) switching to route user requests to the new endpoint.
- **Canary**: Similar to Blue/Green except it also has an application [Load Balancer](../../Distributed%20Systems/Load%20Balancer.md) to route a specific subset of users to the new variant. This can then be increased over time until all users are on the new variant.
- **Champion/Challenger**: A challenger model is compared with the current champion. User requests are processed by both, and the winner becomes the new Production variant. In the "Challenger" scheme, all requests to the champion are recorded and replayed to the challenger. In the "Shadow" scheme, all requests are sent in parallel to both the champion and the challenger.


## ML Workflow Management

The third pillar of MLOps refers to the orchestration and automation of the entire end-to-end system. This automation borrows many ideas from the DevOps movement.


#### Orchestration

*Orchestration* is the automated pipeline of individual workflows that can be orchestrated as needed. These workflows are defined by [Directed Acyclic Graphs](../../Data%20Structures%20&%20Algorithms/Data%20Structures/Graphs.md) (DAGs), whose nodes represent tasks and edges represent the data flow relationship between the tasks. Direct and acyclic implies that workflows can only execute in one direction and a previous upstream task cannot run again once a downstream task has started.

![](../../Attachments/Pasted%20image%2020230419004722.png)

#### ML CI/CD

[CI & CD](../../Software%20Engineering/CI%20&%20CD.md) is also crucial in ML. In the training pipeline, as soon as code is committed, the pipeline can begin. Continuous Integration [tests](../../Software%20Engineering/Test%20Driven%20Development.md) the code and allows us to integrate it easily. The code is then built into a [Container](../../Software%20Engineering/Containers.md) to then be picked up by the deployment phase. The training pipeline seeks to automate all workflows, including:
- Collecting data from the feature store
- Setting hyperparameter combinations for training
- Building and evaluating the model
- Retrieving the test data from the feature store component
- Testing the model and reviewing results to validate the model's quality
- If needed, updating the model parameters and repeating the entire process

When the deployment pipeline is initialized, the model registry is retrieved from the artifact registry, packages it as executable software, tests it for regression, and then deploys it to the production environment. 