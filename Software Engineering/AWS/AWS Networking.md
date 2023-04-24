
## API Gateway

API Gateway is a fully managed way to create, publish, monitor, and secure [REST APIs](../../Internet%20&%20Networking/REST%20API.md) and [WebSockets](../../Internet%20&%20Networking/WebSocket.md). It handles traffic management, [CORS](../../Internet%20&%20Networking/Security/CORS.md) authorization and access control, throttling, monitoring, and API version management. You can choose between a "HTTP API" and a "REST API" which is the same thing except that it includes more features such as per-client throttling, request validation, private endpoints, etc. You define the paths and methods for the API and deploy it as a "stage" which is the version you are setting. This allows you to deploy multiple versions to users to not disrupt usage.

You can authorize an API with [Token Authentication](../../Internet%20&%20Networking/Security/Token%20Authentication.md) or [Session Authentication](../../Internet%20&%20Networking/Security/Session%20Authentication.md) via AWS authentication service.

## Elastic Load Balancer

*Elastic Load Balancer* is a [Load Balancer](../../Distributed%20Systems/Load%20Balancer.md) service that automates the distribution of client requests. An *Application Load Balancer* is a [layer 7](../../Internet%20&%20Networking/OSI%20Model.md) load balancer that can route messages according to the contents of its [HTTP](../../Internet%20&%20Networking/HTTP.md) request. A *Network Load Balancer* is a layer 4 load balancer that routes requests based on its [TCP](../../Internet%20&%20Networking/TCP.md) data. The *Gateway Load Balancer* is a layer 3 load balancer that routes purely on IP data, mostly meant for packet inspection systems.

## CloudFront

CloudFront is the AWS [CDN](../../Distributed%20Systems/CDN.md) service. It allows you to deliver static content like images and videos with very low latency to local customers. Additionally, CloudFront can accelerate your API by using it as the [TLS](../../Internet%20&%20Networking/Security/TLS.md) termination point. TLS is resolved at the local CDN, and then the actual request is routed more quickly to the server.

#### CloudFront Functions

CloudFront functions actually allows you to have local compute, as well as static file serving. This means you can have sub-millisecond latency [Serverless Compute](AWS%20Compute.md) available to local customers.

## Route 53

*Route 53* is the AWS [DNS](../../Internet%20&%20Networking/DNS.md) service. This is the name server that will return your desired [IP](../../Internet%20&%20Networking/IP.md) address for the given domain name(s). Route 53 will also automatically scale with increased load.

## VPC

*Virtual Private Cloud* allows you to create logically isolated virtual networks inside AWS. You have full control over the network, its subnets, and routing tables. It's possible to create a public facing subnet with internet access for your web servers while keeping most of the backend infrastructure in a private subnet with no internet connection, making it much more secure. This is ideal for large enterprise backends that need to be isolated.