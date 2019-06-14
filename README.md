Workshop: Container-based Workloads on Amazon ECS and AWS Fargate
===

In this workshop, we'll explore how you can run workloads using containers
by using either Amazon ECS or AWS Fargate. In the process, we'll look into some common
use cases that make practical sense in real operations, like autoscaling, designing for 
sidecar patterns, and continuous deployment using an automated CI/CD pipeline.

The services we'll primarily use in this workshop include (but are not limited to):

- [Amazon Elastic Container Service (ECS)](https://aws.amazon.com/ecs)
- [AWS Fargate](https://aws.amazon.com/fargate)
- [Amazon Elastic Container Registry (ECR)](https://aws.amazon.com/ecr)

Some services we'll use for auxiliary purposes are:

- [Amazon EC2](https://aws.amazon.com/ec2)
- [Amazon VPC](https://aws.amazon.com/vpc)
- [Amazon IAM](https://https://aws.amazon.com/iam)
- [Services under the AWS Developer Tools](https://aws.amazon.com/products/developer-tools/)

---

## Prerequisites

### AWS Account

In order to complete this workshop, you'll need an AWS account, 
and have login credentials that have access to the services listed above.

- If you're going through a classroom workshop, 
  your facilitator may already have credentials for you to use.
  Consult with them to confirm.

- If you need an AWS account of your own, 
  [you can create an AWS account here](https://portal.aws.amazon.com/gp/aws/developer/registration/index.html).

This workshop will try to stay within the 
[AWS Free Tier](https://aws.amazon.com/free) as much as possible.
If your account is less than 12 months old, you are eligible for the first year Free Tier.


### Network

Your resources will need to be deployed into an Amazon VPC.
[More information on that here](./AUXILIARY.md#creating-a-vpc-for-networking).


### Workspace

> **NOTE:** Instead of setting up a local development environment for this workshop,
> it might be easier to just set up an [AWS Cloud9](https://aws.amazon.com/cloud9) 
> workspace instead.
>
> An **AWS Cloud9** workspace is automatically set up for software development,
> and is pre-installed with all the CLI tools required in this workshop.
> 
> [More information on this here.](./AUXILIARY.md#setting-up-an-aws-cloud9-workspace)

Working with Docker (and scripting on the AWS platform) usually requires you to work in a terminal shell.

Additionally, this workshop will use [Node.js](https://nodejs.org) as a development platform.
You're free to use any language you want, but the examples in this workshop will use Node.js.

In summary, you will need the following on your workspace available:

- `docker`  (Docker CE)
- `git`
- `node` and `npm` (**v10.16+** preferred)
    - (Alternatively) use `nvm` to manage Node.js versions on your machine.
- (optionally) the [AWS CLI](https://aws.amazon.com/cli)
    - Installing the [AWS CLI](https://aws.amazon.com/cli) requires that you have `pip` installed.
