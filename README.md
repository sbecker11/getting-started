# Getting Started With Kubernetes on AWS Tutorial (2023 Update)

see https://www.sitepoint.com/kubernetes-aws-tutorial/

Welcome to this beginner’s tutorial on using Kubernetes with AWS. In this tutorial, we’ll walk you through the process of setting up a Kubernetes cluster on AWS, deploying a sample application, and managing your cluster. By the end of this tutorial, you’ll have a solid understanding of the basics of Kubernetes and how to use it with AWS.

Contents:

1. Introduction to Kubernetes
1. Setting up a Kubernetes Cluster on AWS
1. Deploying a Sample Application
1. Managing Your Cluster
1. Common Questions about Kubernetes

## Introduction to Kubernetes  

Kubernetes is an open-source container orchestration platform that automates the deployment, scaling, and management of containerized applications. It was originally developed by Google and is now maintained by the Cloud Native Computing Foundation (CNCF). Kubernetes provides a powerful and flexible framework for managing containers, making it an essential tool for modern application development and deployment.

## Setting up a Kubernetes Cluster on AWS

In this section, we’ll guide you through the process of setting up a Kubernetes cluster on AWS using the managed Kubernetes service, Amazon Elastic Kubernetes Service (EKS).

### Prerequissites  

Before we begin, make sure you have the following:

* an AWS account
* AWS CLI installed and configured
* kubectl installed
* eksctl installed

### Creating a Kubernetes Cluster with eksctl  


eksctl is a command-line tool that simplifies the process of creating and managing Kubernetes clusters on AWS. To create a new cluster, run the following command: (p.s. Don't forget to set your default profile in ~/.aws/credentials)  

    eksctl create cluster --name my-cluster --region us-west-2 --nodes 3

This command creates a new Kubernetes cluster named my-cluster in the us-west-2 region with three worker nodes.  Be patient, it may take a few minutes (like 7+) for the cluster to be created.  

### Configuring kubectl to Use Your Cluster  

Once your cluster is created, you need to configure kubectl to use it. Run the following command:  

    aws eks update-kubeconfig --region us-west-2 --name my-cluster

This command updates your kubectl configuration to use the new cluster. 

## Deploying a Sample Application  

In this section, we will deploy a simple “Hello, World!” application to your Kubernetes cluster.  

### Creating a Deployment  
see https://github.com/skynet86/hello-world-k8s/blob/master/hello-world.yaml


Create a file named hello-world.yaml with the following contents:  


    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: hello-world
      labels:
        app: hello-world
    spec:
      selector:
        matchLabels:
          app: hello-world
      replicas: 3
      template:
        metadata:
          labels:
            app: hello-world
        spec:
          containers:
          - name: hello-world
            image: gcr.io/google-samples/node-hello:1.0
            ports:
            - containerPort: 8080

This YAML file defines a Kubernetes Deployment that creates three replicas of a “Hello, World!” application. Save the file and run the following command to create the Deployment:  

    kubectl apply -f hello-world.yaml

### Exposing the Application

To access the application from outside the cluster, you need to create a Kubernetes Service. Run the following command:  

    kubectl expose deployment hello-world --type=LoadBalancer --port=80 --target-port=8080

This command creates a LoadBalancer Service that exposes the “Hello, World!” application on port 80.

### Accessing the Application

To access the “Hello, World!” application, you need to find the load balancer’s address. Run the following command:  

    kubectl get services

Look for the EXTERNAL-IP of the hello-world service. Once you have the external IP, open a web browser and navigate to http://EXTERNAL-IP. You should see the “Hello, World!” message displayed.

## Managing Your Cluster

In this section, we’ll cover some basic tasks for managing your Kubernetes cluster, such as scaling your application and updating your deployment.  

### Scaling Your Application

To scale your application, you can update the number of replicas in your Deployment. For example, to increase the number of replicas to 5, run the following command:  

    kubectl scale deployment hello-world --replicas=5

### Updating Your Deployment

If you need to update your application, you can update the container image in your Deployment. For example, to update the “Hello, World!” application to version 2.0, run the following command:  

    kubectl set image deployment hello-world hello-world=gcr.io/google-samples/node-hello:2.0  

This command updates the container image in the Deployment, and Kubernetes will automatically perform a rolling update to replace the old Pods with new ones.

### Monitoring Your Cluster

To monitor the status of your cluster, you can use the Kubernetes Dashboard, a web-based user interface for managing your cluster. 

## [Deploy and Access the Kubernetes Dashboard](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)

Dashboard is a web-based Kubernetes user interface. You can use Dashboard to deploy containerized applications to a Kubernetes cluster, troubleshoot your containerized application, and manage the cluster resources. You can use Dashboard to get an overview of applications running on your cluster, as well as for creating or modifying individual Kubernetes resources (such as Deployments, Jobs, DaemonSets, etc). For example, you can scale a Deployment, initiate a rolling update, restart a pod or deploy new applications using a deploy wizard.

Dashboard also provides information on the state of Kubernetes resources in your cluster and on any errors that may have occurred.

### Deploying the Dashboard UI

The Dashboard UI is not deployed by default. To deploy it, run the following command:

    kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml


Accessing the Dashboard UI
To protect your cluster data, Dashboard deploys with a minimal role-based access control (RBAC) configuration by default. Currently, Dashboard only supports logging in with a Bearer Token. To create a token for this demo, you can follow our guide on creating a sample user.

Warning: The sample user created in the tutorial will have administrative privileges and is for educational purposes only.

### [How To Create Admin User to Access Kubernetes Dashboard](https://computingforgeeks.com/create-admin-user-to-access-kubernetes-dashboard/)

This page describes the following:  
Step 1: Create Admin service account  
Step 2: Create a Cluster Role Binding  
Step 3: Obtain admin user token  
Step 4: Accessing Kubernetes Dashboard  
Step 5: Creating non admin user account  

### Command line proxy
You can enable access to the Dashboard using the kubectl command-line tool, by running the following command:

    kubectl proxy

Kubectl will make Dashboard available at http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/.

The UI can only be accessed from the machine where the command is executed. See kubectl proxy --help for more options.

Note: The kubeconfig authentication method does not support external identity providers or X.509 certificate-based authentication.

### Welcome view
When you access Dashboard on an empty cluster, you'll see the welcome page. This page contains a link to this document as well as a button to deploy your first application. In addition, you can view which system applications are running by default in the kube-system namespace of your cluster, for example the Dashboard itself.

Other sections:
* Deploying containerized applications
* Using Dashboard 


