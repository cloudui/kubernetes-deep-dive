# The Big Picture

What is Kubernetes?

- Open-source platform for running cloud-native applications

Start with infrastructure

- Need something on top of it to run your apps on
- K8s serves as a layer for you to deploy your apps

Cloud-native apps: Built from small interacting services to build a useful app

- Opposite of Monolithic apps
- High scalable

Kubernetes cluster

- Bunch of Linux nodes, VMs, cloud instances
- Some form control plane and some form the worker nodes

Control Plane

- Scheduler, persistent store, API server, controllers
- Stateful plane

All commands and system components talk to the API server

# The API

Kubernetes API is where everything is defined

RESTful API that uses HTTP methods to perform CRUD style actions

Define parts of app in YAML files as a overall desire of intent

- Change the current state to the desired state

Used to be defined in a monolithic group 

- Split up into smaller groups

![Untitled](The%20Big%20Picture%20d0fcba9c42514a1ca90756ab92b71675/Untitled.png)

SIGs look after the development of API groups

- feature development

## Steps of Development

Alpha

Beta

GA

# Kubernetes Objects

## Pods

- Kubernetes wraps containers in a high-level construct called a pod
    - Your atomic unit, smallest resource that can be deployed
- Object on a cluster, can run one or more containers
- Atomic unit of scheduling
- v1 API group

## Deployments

- Resource in Apps group, object on the cluster
- Makes things scalable, rolling updates, flexible

## DaemonSets

- Makes sure one and only one of a specific pod will work on every worker in the cluster