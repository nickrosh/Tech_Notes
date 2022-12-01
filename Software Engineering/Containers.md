

Containers are a lightweight method to package code and dependencies in a dependable and scalable way. Virtual machines are the traditional method, but each VM requires its own entire virtual OS. Containers run on top of the system OS, and you can have many more containers per machine than comparable VMs.



![](Pasted%20image%2020220830225314.png)


## Docker

The most popular (2022) container software and engine is docker. You create your instructions on how to construct the image from a **Dockerfile**. An **Image** is the template for a container that can also be pushes to a public repository, very similarly to a git repo.

With an image, you can create containers and attach **Volumes** of existing data to each new container if you wish. Although it is best for containers to be stateless, and to leave database to other solutions.


## Orchestration

The management of a large number of containers on a large number of nodes (machines) must be done by an orchestration software. The most popular current one is **Kubernetes**. You can write rules on how to create, delete, and maintain the containers to meet oncoming load.