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
- To create a replica of a Pod, you don't need to create a Pod, rather you need to define the blueprints for the application Pod and specify the number of replicas you needed. Defining the blueprint of the application Pod is called deployment.
