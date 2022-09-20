# AWS ECS
### Elastic Container Service
Amazon Elastic Container Service (Amazon ECS) is a highly scalable and fast container management service. You can use it to run, stop, and manage containers on a cluster. With Amazon ECS, your containers are defined in a task definition that you use to run an individual task or task within a service. In this context, a service is a configuration that you can use to run and maintain a specified number of tasks simultaneously in a cluster. You can run your tasks and services on a serverless infrastructure that's managed by AWS Fargate. Alternatively, for more control over your infrastructure, you can run your tasks and services on a cluster of Amazon EC2 instances that you manage.

### Blue Green Deployment

<p align="center"><a href="https://github.com/aws-samples/ecs-blue-green-deployment"><img src="https://user-images.githubusercontent.com/86287920/189579005-cf687792-b03d-4549-a5d3-08bc5dae6fda.png" width="800" /></a></p>

This Image is a Sample of Blue Green Deployment connect to a LoadBalancer, Application Load Balancer to be precisely

### ECS on EC2

<p align="center"><img src="https://user-images.githubusercontent.com/86287920/191167487-51ae4c52-fbfd-4cba-87f0-6e129227a2d0.jpg" width="800" /></p>

Amazon ECS on EC2 is a highly scalable, high perfomrmance container management service for Docker containers running on EC2 instances. <br />
Collect ECS metrics automatically from CloudWatch using the Amazon ECS Datadog integration. Expand on those metrics by querying the ECS API for ECS events, tags, and the status of container instances, taks, and services.

### ECS on Fargate

AWS Faragte is a technology that you can use with Amazon ECS to run <a href="https://aws.amazon.com/containers/">containers</a> without having to manage **servers or clusters** of Amazon EC2 instances. With AWS Fargate, you no longer have to provision, configure, or scale clusters of virtual machines to run containers. This removes the need to choose server types, decide when to scale your cluters, or optimize cluster packing.
