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

<p align="center"><img src="https://user-images.githubusercontent.com/86287920/191633137-32d5719a-cac6-4c4d-9c30-f7cd411b485d.jpeg" width="800" /></p>

AWS Faragte is a technology that you can use with Amazon ECS to run <a href="https://aws.amazon.com/containers/">containers</a> without having to manage **servers or clusters** of Amazon EC2 instances. With AWS Fargate, you no longer have to provision, configure, or scale clusters of virtual machines to run containers. This removes the need to choose server types, decide when to scale your cluters, or optimize cluster packing.


### Blue/Green deployment with CodeDeploy
The blue/green deployment type uses the blue/green deployment model controlled by CodeDeploy. This deployment type enables you to verify a new deployment of a service before sending production traffic to it. For more information, see <a href="https://docs.aws.amazon.com/codedeploy/latest/userguide/welcome.html">What Is CodeDeploy? </a>in the AWS CodeDeploy User Guide.

There are three ways traﬃc can shift during a blue/green deployment:
- **Canary** — Traﬃc is shifted in two increments. You can choose from predeﬁned canary options that specify the percentage of traﬃc shifted to your updated task set in the ﬁrst increment and the interval, in minutes, before the remaining traﬃc is shifted in the second increment.
 - **Linear** — Traﬃc is shifted in equal increments with an equal number of minutes between each increment. You can choose from predeﬁned linear options that specify the percentage of traﬃc shifted in each increment and the number of minutes between each increment.
- **All-at-once** — All traﬃc is shifted from the original task set to the updated task set all at once.
The following are components of CodeDeploy that Amazon ECS uses when a service uses the blue/green deployment type:
#### CodeDeploy application
A collection of CodeDeploy resources. This consists of one or more deployment groups.
#### CodeDeploy deployment group
The deployment settings. This consists of the following:
- Amazon ECS cluster and service
- Load balancer target group and listener information
- Deployment roll back strategy
- Traffic rerouting settings
- Original revision termination settings
- Deployment configuration
- CloudWatch alarms configuration that can be set up to stop deployments
- SNS or CloudWatch Events settings for notifications
For more information, see <a href="https://docs.aws.amazon.com/codedeploy/latest/userguide/deployment-groups.html">Working with Deployment Groups</a> in the AWS CodeDeploy User Guide.
#### CodeDeploy deployment configuration
Specifies how CodeDeploy routes production traffic to your replacement task set during a deployment. The following pre-defined linear and canary deployment configuration are available. You can also create custom defined linear and canary deployments as well. For more information, see <a href="https://docs.aws.amazon.com/codedeploy/latest/userguide/deployment-configurations.html">Working with Deployment Configurations</a> in the AWS CodeDeploy User Guide.

| Deployment configuration    | Description |
| -------------------------------------------------- | --------------------- |
| `CodeDeployDefault.ECSLinear10PercentEvery1Minutes` | Shifts 10 percent of traﬃc every minute until all traﬃc is shifted. |
| `CodeDeployDefault.ECSLinear10PercentEvery3Minutes` | Shifts 10 percent of traﬃc every three minutes until all traﬃc is shifted. |
| `CodeDeployDefault.ECSLinear10PercentEvery5Minutes` | Shifts 10 percent of traﬃc in the ﬁrst increment. The remaining 90 percent is deployed ﬁve minutes later. |
| `CodeDeployDefault.ECSLinear10PercentEvery15Minutes` | Shifts 10 percent of traﬃc in the ﬁrst increment. The remaining 90 percent is deployed 15 minutes later. |
| `CodeDeployDefault.ECSAllatOnce` | Shifts all traﬃc to the updated Amazon ECS container at once. |

Revision
A revision is the CodeDeploy application specification file (AppSpec file). In the AppSpec file, you specify the full ARN of the task definition and the container and port of your replacement task set where traffic is to be routed when a new deployment is created. The container name must be one of the container names referenced in your task definition. If the network configuration or platform version has been updated in the service definition, you must also specify those details in the AppSpec file. You can also specify the Lambda functions to run during the deployment lifecycle events. The Lambda functions allow you to run tests and return metrics during the deployment. For more information, see <a href="https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file.html">AppSpec File Reference</a> in the AWS CodeDeploy User Guide.

## buildspec.yml file
```
version: 0.2

phases:
  pre_build:
    commands:
      - echo Logging in to Amazon ECR...
      - aws --version
      - aws ecr get-login-password --region $AWS_DEFAULT_REGION | docker login --username AWS --password-stdin <your-account-id>>.dkr.ecr.ap-northeast-2.amazonaws.com
      - REPOSITORY_URI=<<your-account-id>>.dkr.ecr.eu-central-1.amazonaws.com/<<your-ecr>>
      - IMAGE_TAG=$(date "+%Y-%m-%d.%H.%M.%S")
  build:
    commands:
      - echo Build started on `date`
      - echo Building the Docker image...
      - docker build -t $REPOSITORY_URI:latest .
      - docker tag $REPOSITORY_URI:latest $REPOSITORY_URI:$IMAGE_TAG
  post_build:
    commands:
      - echo Build completed on `date`
      - echo Pushing the Docker images...
      - docker push $REPOSITORY_URI:$IMAGE_TAG
      - echo Writing image definition file...
      - echo "$(jq .containerDefinitions[].image=\"$REPOSITORY_URI:$IMAGE_TAG\" taskdef.json)" > taskdef.json
artifacts:
  files:
    - 'appspec.yml'
    - 'taskdef.json'
```
## appspec.yml file
```
version: 0.0

Resources:
  - TargetService:
      Type: AWS::ECS::Service
      Properties:
        TaskDefinition: "<TASK_DEFINITION>"
        LoadBalancerInfo:
          ContainerName: "이름"
          ContainerPort: 포트번호
```
## imagedefinitions.json
```
[
    {
      "name": "national-wsi-infra-master-app",
      "imageUri": "635272395604.dkr.ecr.ap-northeast-2.amazonaws.com/national-wsi-infra-ecr-repo:master"
    }
]
```
## taskdef.json
```
{
 "executionRoleArn": "arn:aws:iam::accound_id:role/ecsTaskExecutionRole",
 "containerDefinitions": [{
  "name": "task-td-ctn",
  "image": "<IMAGE>",
  "essential": "true",
  "portMappings": [{
    "protocol": "tcp",
    "containerPort": 80
  }]
 }],
 "requiresCompatibilitiess": [
   "EC2"
 ],
 "cpu": "512",
 "memory": "512",
 "family": "skills-td"
}
```
