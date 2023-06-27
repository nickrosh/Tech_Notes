

Containers are a lightweight method to package code and dependencies in a dependable and scalable way. Virtual machines are the traditional method, but each VM requires its own entire virtual OS. Containers run on top of the system [Operating System](../Systems%20Software/Operating%20System.md), and you can have many more containers per machine than comparable VMs.



![](../Attachments/Pasted%20image%2020220830225314.png)


## Docker

The most popular (2022) container software and engine is docker. You create your instructions on how to construct the image from a **Dockerfile**. An **Image** is the template for a container that can also be pushes to a public repository, very similarly to a git repo.

With an image, you can create containers and attach **Volumes** of existing data to each new container if you wish. Although it is best for containers to be stateless, and to leave database to other solutions.

#### Common Docker Tasks

| Task                                                 | Code                                 |
| ---------------------------------------------------- | ------------------------------------ |
| Pull a Docker Image from the Hub                     | `docker pull <IMAGE NAME>:<VERSION>` |
| Create a docker container from an image and start it | `docker run <IMAGE NAME>`            |
| Build a new image from the source Code (Dockerfile)  | `docker build <PATH TO DOCKERFILE>`  |
| Create a docker container without starting it        | `docker create <IMAGE NAME>`         |
| Run a container                                      | `docker start <CONTAINER NAME>`      |
| List all active containers (add -a for inactive too) | `docker ps <-a>`                     |
| List all images                                      | `docker images`                      |
| Stop an active container                             | `docker stop <CONTAINER ID>`         |

