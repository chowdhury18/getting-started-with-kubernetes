# Getting started with K8s

## What is K8s
 - Open source container orchestration tool
 - Developed by Google
 - Manage containerize applications in different environments (physical, cloud, virtual)

## What K8s offers
 - High availability or no downtime
 - Scalability or high performance
 - Disaster recovery or backup and restore

## K8s components

### Node and Pod
---
- A server, physical or virtual machine
- A basic component of K8s node is a Pod
- Pod is abstraction over container that means Pod can run any containerization technology and you just need to interact with Pod irrespect to container technology.
- In usual case, Pod runs only one container but you can run multiple containers in a Pod.
- Each pod gets own IP address for internal communication
- Problem with Pod IP address is if the Pod dies, a new IP address will be assigned to the newly created Pod

### Service and Ingress
---
- To solve the Pod IP issue, service comes into play
- Service has permanent/static IP address with can be attached to each Pod
- Lifecycle of Pod and Service is not connected, so if pod dies, service remains in place.
- There are two kinds of services:
    - Internal service: To communicate inside the K8s cluster
    - External service: To communicate with out side world (browser)
- Service has two functionalities:
    - permanent IP
    - load balance

- With external service, you can access the application through external IP and port number (e.g., 123.12.12.34:8001) which is not convenient.
- To solve the case, Ingress is used. It provides secure protocol and domain name to the application to access from outside world (e.g., https://my-app.com)


### Secret and configMap
---
- Usually the DB url and access credentials are stored in the application properties files. So, in case the url of the DB get changed, you need to rebuild the application, push the build application to repo and pull it in your Pod.
- ConfigMap solves this issue.
    - Stores external configurations
    - Pod gets the external configuration data from configMap

- To store the credential of DB
    - Secret component of K8s is used
    - Store in Base64 encoded

### Volumes
---
- To persist the data of a Pod, volume is used
- Could be stored locally or remotely


### Deployment and replica set
---
- To create a replica of a Pod, you don't need to create a Pod, rather you need to define the blueprints for the application Pod and specify the number of replicas you needed. Defining the blueprint of the application Pod is called deployment. Deployment is an abstraction of pod.

- In case of application, if any application pod dies, based on the deployment script, another application pod will be created. But in case of database, replicating database is not possible due to database state (may lead to inconsistant database state). To handle the issue, another k8s component `StatefulSet` comes into play. Stateful applications, such as MySql, Mongo, Elastic search pods should be created by StatefulSet but not by Deployment. Deploying StatefulSet is not simple and easy :disappointed:. One option could be to host database outside k8s cluster.

![K8s Components](https://github.com/chowdhury18/getting-started-with-kubernetes/blob/master/Diagrams/k8s-components.png)


## K8s Architecture
- Every node (`worker node`) must have 3 processes installed:
    - Container runtime (e.g., docker)
    - Kubelet - interact between the container and the worker node (physical machine). Kubelet starts the pod with a container inside.
    - Kube proxy - forwards the request. In multiple worker nodes setup, nodes communicate with the help of services. Kube proxy helps to forward the request to pods intelligently.

- `Kubernetes Cluster`: A Kubernetes cluster is a set of nodes that run containerized applications.

- How to manage and interact with the cluster?
    - How to schedule the pods
    - How to monitor the pods
    - How to re-schedule/re-start the pods
    - How to add a new node to the cluster

- `Master Node` manages all the interaction between the pods and the cluster. 
- Four processes run on every master node:
    - ***Api Server***: Works as cluster gateway. If any clients (UI, kubernetes dashboard, command line) wants to deploy an application to the cluster, it goes through Api Server of the master node. `Some request` -> `Api Server` -> `Validate Request` -> `Other Processes..` -> `Pod`
    - ***Scheduler***: `Schedule new pod request` -> `Api Server` -> `Scheduler` -> `Kubelet` -> `create new pod`. Scheduler decides on which node new pod should be scheduled.
    - ***Controller Manager***: Detect cluster state changes. If I pod dies, controller manager will detect and try to recover the situation. `Controller Manager` -> `Scheduler` -> `Kubelet` -> `create new pod`
    - ***etcd***: Key-Value store of cluster state. Cluster changes get stored in the key value store.

![k8s Architecture](https://github.com/chowdhury18/getting-started-with-kubernetes/blob/master/Diagrams/k8s-architecture.png)

## Minikube
- Minikube is one node cluster where `Master` and `Worker` nodes run on one Machine.
- Minikube will create avirtual box in your system and nodes run in that box.

### Commands:
```shell
# Starts kubernetes one node cluster (vm). 
# --vm-driver is specifing which hypervisor to use.
minikube start --vm-driver=hyperkit
```

## Kubectl
- Command line tool for kubernetes cluster
- To interact such as, deploy new pod, service with minikube virtual box

### Commands:
```sh
# get status of nodes
kubectl get nodes

# get pod status
kubectl get pod

# get services status
kubectl get services

# create deployment
# deployment plays abstraction role for pods
# 'nginx-deploy' is the name of the deployment
kubectl create deployment nginx-deploy --image=nginx

# get deployment status
kubectl get deployment

# get replicaset
kubectl get replicaset

# edit deployment
# 'nginx-deploy' is the name of the deployment
kubectl edit deployment nginx-deploy

# log pod
# 'nginx-deploy-12fr34tg24-1e3f5' name of the pod
kubectl logs nginx-deploy-12fr34tg24-1e3f5

# additional information regarding pod
# 'nginx-deploy-12fr34tg24-1e3f5' name of the pod
kubectl describe pod nginx-deploy-12fr34tg24-1e3f5

# interact with pod
# 'nginx-deploy-12fr34tg24-1e3f5' name of the pod
kubectlexec -it nginx-deploy-12fr34tg24-1e3f5 -- bin/bash

# delete deployment
# 'nginx-deploy' is the name of the deployment
kubectl delete deployment nginx-deploy

# apply the deployment config by file
# 'nginx-deploy.yaml' file name 
kubectl apply -f nginx-deploy.yaml
```

- Pod's naming by kubernetes:
	- name of replicaset (name of deployment + random-string) + random-string
	- Ex. ((nginx-deploy)-12fr34tg24)-1e3f5

## K8s configuation

Each configuration has three parts:
- metadata
- specification
- status (Etcd holds the current status of any K8s component)

## Blueprints of configuration file
Deployment manages pods.

In deployment-config file, pods configurations are described in `template` section. Template section has it's own `metadata` and `spec`.

## Connecting components

`Labels` and `Selectors`, a `key` : `value` pair, connect the dot among the deployment, service and pod.

Deployment metadata containers `labels` and Deployment and Service spec container `selector`.

To connect Deployment with Pod, same `labels` are given to metadata.

Selector matches the labels to connect the pod with deployment and service.

## Abstraction layer

- `Deployment` manages `Replicaset`

- `Replicaset` manages `Pod`

- `Pod` is an abstraction of `Container`


## References
- [Kubernetes Tutorial for Beginners - TechWorld with Nana](https://www.youtube.com/watch?v=X48VuDVv0do&t=1087s)