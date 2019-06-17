Module 05: Using AWS Fargate
===

Amazon ECS gives you very fine-grained control over how your containers are 
run, as well as on the infrastructure that maintains all your container-based
workloads. However, sometimes, you don't need that much control, or you just
want to be able to run workloads much quicker, or you just want to be costed
as closely to what you use as possible.

This is where [AWS Fargate](https://aws.amazon.com/fargate) really shines.
AWS Fargate functions very similarly to how we've used 
[Amazon ECS](https://aws.amazon.com/ecs) in the following modules, with one
very important distinction: in AWS Fargate, you don't manage any underlying
infrastructure to run your containers.

You essentially just tell AWS Fargate what containers / tasks / services to run,
and how much resources they need, and it'll just silently handle the rest.
There is no need to maintain a cluster of host EC2 instances.

Let's play around with it a bit.

---

## Implementation Details

### 1. Create an AWS Fargate cluster.

Creating one is very simple.

#### High-level instructions

Create an AWS Fargate cluster. Ensure that your cluster uses the same VPC that
you've been using for the workshop.

<details>
  <summary><strong>Step-by-step instructions (click to expand):</strong></summary>
  <p>
  
  1. Go to your **Clusters** dashboard on [Amazon ECS](https://console.aws.amazon.com/ecs).
     Create a new cluster.
  2. When opted for a cluster template, choose **Networking only**.
     Specifying this option launches a cluster under AWS Fargate.
  3. In the next step, give your cluster a name. (`nickname-cluster-fargate` is great).
  4. Do **not** opt to create a new VPC.
  5. Click **Create** to finalize creation.
  </p>
</details>

That's all. You should now have a fully functioning AWS Fargate cluster.
Let's look at running some containers in it.


### 2. Create a Fargate-compatible task definition.

Since AWS Fargate clusters don't run containers in EC2 instances you manage,
the task definitions that are configured to run containers in EC2 
(the ones we created in the last module) won't work with our new cluster.

We'll need to create a new task definition that is specifically for use 
in AWS Fargate clusters. 

#### High-level instructions

Create a new Fargate-compatible task definition, using the Docker image we pushed
earlier to our [Amazon ECR](https://console.aws.amazon.com/ecr) registry.

<details>
  <summary><strong>Step-by-step instructions (click to expand):</strong></summary>
  <p>
  
  1. Go to your **Task Definitions** dashboard, and create a new task definition.
  2. Opt to use the **Fargate** launch type. Click **Next step**.
  3. Enter the following values:
     - **Task Definition Name**: _<< your choice >>_ (`nickname-app-fargate` is good)
     - **Task Role**: None
     - **Task Execution Role**: Create a new Role 
     - **Task memory**: `0.5GB`
     - **Task CPU**: `0.25 vCPU`
     - Setup the rest of the options as you did the previous task definition.
  4. Under **Container Definitions**, click **Add container**.
  5. Set up the container definition as you did before, with one exception:
     for **Port mappings** you only need to specify the container port.
     (Remember that we set up our Docker image earlier to use `TCP 8080`.)
  6. Click **Create** to complete creation.
  </p>
</details>

Excellent. Not a lot of differences, right?
Let's create a service next.


### 3. Create a service in our AWS Fargate cluster

By now it should be pretty apparent that there's really not a lot of changes
to how you'd work with AWS Fargate than with how it is with Amazon ECS. 🤪

#### High-level instructions

Run a service in your AWS Fargate cluster containing only 1 task,
using the task definition you just created.

<details>
  <summary><strong>Step-by-step instructions (click to expand):</strong></summary>
  <p>
  
  1. In your cluster's detail page, under the **Services** tab, opt to
     create a new service.
  2. Specify that the new service is for the **Fargate** launch type.
     Select the task definition you just created, with the latest revision.
     (There should only be one revision now.)
  3. Fill in the inputs, specifying that you're only running **1 task** for now.
  4. In the next step, select the VPC that you're using for this workshop.
     Also select all 3 subnets you're using. 
  5. For security group, click **Edit**.
     Opt to create a new security group.
     Give your security group a name. (`nickname-fargate-sg` is good)
     **Ensure that your security group allows inbound network traffic to your container port.**
     
     Click **Save**.
  6. Ensure **Auto-assign public IP** is `ENABLED`.
  7. For **Load Balancing**, don't use a load balancer yet.
  8. Click **Next Step**. 
  9. Don't set **Auto Scaling** yet. Click **Next Step**.
  10. If you're OK with the configuration, click **Create Service** to 
      finalize your settings.
  </p>
</details>

Like before, the service creation will run through its tasks, and will be 
ready in a few moments.

Go into the service detail page, and wait for the single task to become ready
(status should be `RUNNING`).

Like before, verify that you can reach the task by retrieving its external URL,
and visiting it directly from your browser. (You will need to append the container
port to the external URL!)

---

## Objectives ⭐️

Now that you've tried working with an AWS Fargate cluster, do the following
objectives without us telling you how!

1. Scale the service to `30 tasks` running at the same time.
2. Modify the task definition so that the container uses:
   - `1 GB` memory
   - `0.5 vCPU`
3. Create a new service that places tasks / containers behind an
   Application Load Balancer.
   (**Hint:** You will need to create a new Target Group, with type `IP`
   instead of `Instance`.)
4. Change the message your container makes from `Hello from {HOSTNAME}` to 
   `{HOSTNAME} wishes you a good day!`. 
   Make the necessary adjustments / deployments so that your services show
   this change.
5. Don't forget to scale back down to `1 task`.

---

## Summary

AWS Fargate allows you to run container-based workloads, all without having
to do the heavy lifting associated with maintaining server / host 
infrastructure. Just create a cluster, tell it how you want it to run your
containers, and off you go.

In the next modules, let's look at how else we can work with Amazon ECS
and AWS Fargate.
