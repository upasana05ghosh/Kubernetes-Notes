# Kubernetes-Notes
Kubernetes-Notes from "Kubernetes in Action"


- [Kubernetes-Notes](#kubernetes-notes)
  - [What is Kubernetes](#what-is-kubernetes)
  - [Kubernetes Term](#kubernetes-term)
  - [Cluster](#cluster)
    - [Architecture of Kubernetes Cluster](#architecture-of-kubernetes-cluster)
    - [To see the clusters running](#to-see-the-clusters-running)
    - [To see the number of cluster running](#to-see-the-number-of-cluster-running)
  - [Pods](#pods)
    - [Creating a Pod](#creating-a-pod)
    - [To create the pod using yaml file](#to-create-the-pod-using-yaml-file)
    - [To get whole definition of a running pod](#to-get-whole-definition-of-a-running-pod)
    - [To get a list of all the pod](#to-get-a-list-of-all-the-pod)
    - [To get logs of the container](#to-get-logs-of-the-container)
    - [If there are multiple pods in the container](#if-there-are-multiple-pods-in-the-container)
    - [To port-forward](#to-port-forward)
    - [Namespaces](#namespaces)
    - [Stopping and removing pods](#stopping-and-removing-pods)
  - [Replication and other controllers](#replication-and-other-controllers)
    - [Keeping pods healthy](#keeping-pods-healthy)
    - [Replication Controller](#replication-controller)
    - [Using ReplicaSets instead of ReplicationController](#using-replicasets-instead-of-replicationcontroller)
    - [DaemonSets](#daemonsets)
    - [Job](#job)
    - [Cron Job](#cron-job)
  - [Services](#services)
    - [Kubernetes Services](#kubernetes-services)
    - [Connecting to services living outside the cluster](#connecting-to-services-living-outside-the-cluster)

## What is Kubernetes
 - open source
 - orchestrator
 - for deploying containerized app

## Kubernetes Term
1. Cluster -> is a set of nodes
2. Nodes -> virtual CPUs
   - contains set of pods
3. Pods -> Kubernetes basic unit
   - contains one of few containers
     
## Cluster
### Architecture of Kubernetes Cluster
<img width="870" alt="image" src="https://github.com/upasana05ghosh/Kubernetes-Notes/assets/17885669/a051be3d-48ba-4cf1-bb61-37c4a7d3f742">

* API Server - We and other control plane component communicates with it.
* Scheduler - Which schedule your app (assign a worker node to each deployable component of your app)
* Controller Manager - performs cluster level functions like replicating components, handling node failure
* etcd - store cluster config

### To see the clusters running
```
kubectl config get-contexts
```
### To see the number of cluster running
```
kubectl config get-contexts --no-headers | wc -l
```

## Pods
- A pod is a group of one or more closely related containers that will always run together on same worker node. 
- All containers in a pod share the same IP address and port space, so a container can communicate to other containers in the same pod through localhost. 
- Pods (same node or different node) can communicate with other pods via IP address like a computer on LAN. 
<img width="855" alt="image" src="https://github.com/upasana05ghosh/Kubernetes-Notes/assets/17885669/360520b4-4373-4482-b767-8a5b82501f23">

### Creating a Pod
<img width="678" alt="image" src="https://github.com/upasana05ghosh/Kubernetes-Notes/assets/17885669/cb40d188-6f28-4ef5-a983-cfb2a0850d6b">

### To create the pod using yaml file
```
kubectl create -f kubia-manual.yaml
pod "kubia-manual" created
```
### To get whole definition of a running pod
```
kubectl get po kubia-manual -o yaml
```
### To get a list of all the pod
```
kubectl get pods
```
### To get logs of the container
```
kubectl logs kubia-manual
```
### If there are multiple pods in the container
```
 kubectl logs kubia-manual -c <container-name>
```
### To port-forward
```
kubectl port-forward kubia-manual 8888:8080
```

### Namespaces
- Help to split resources into separate, non-overlapping groups.
- To get list of namespace in a cluster
  ```
  kubectl get ns
  ```
- If we don't specify a namespace, it will pick `default` namespace. 
- Create a namespace
  1. via Yaml file
     1. //todo attach pic
  2. via command
      ```
      kubectl create namespace custom-namespace
      ```
- Namespace doesn't provide network isolation. There's nothing preventing a pod to send HTTP request to other pod in different namespace

### Stopping and removing pods
- Deleting a pod
  ```
  kubectl delete po <pod-name>
  ```
- If a pod is created by ReplicationController, RC will create a new pod. To delete the pod, we need to delete the RC
- To delete all resources 
   ```
   kubectl delete all -all
   ```

## Replication and other controllers

### Keeping pods healthy
- Liveness Probes
  - Kubernetes will periodically execute the probe and restart the container if the probe fails. 
  - 3 probing mechanism:
    - HTTP Get
    - TCP Socket
    - Exec
  - //todo add img for liveness check
  - //todo add img of Last state
  - Options
    - delay=0s -> probing begins immediately
    - timeout=1s -> container must return a response in 1s or probe is failed
    - period=10s -> container is probed every 10s
    - failure=3 -> container is restarted after 3 failed probes

### Replication Controller
- Kubernetes resource that ensure its pods are always kept running. 
- It makes sure the actual number of pods of a "type" always matches the desired number. 
- kind: ReplicationController

### Using ReplicaSets instead of ReplicationController
- ReplictionControllers are replaced by ReplicaSet.
- ReplicaSet are usually not created directly, but are created automatically by Deployment resource.
- kind: ReplicaSet
- // TODO: Add replicat set image
  
### DaemonSets
- When you want a pod to run on each and every node in the cluster and run exactly once. 
- Ex - Run a log collector and a resource monitor. 
- If a node goes down, the DaemonSet doesn't cause the pod to be created elsewhere. But when a new node is added to the cluster, the DaemonSet deploys a new pod instance in it. 
- We can specify DaemonSet to run on a certain nodes.
- // TODO - add daemon Set image

### Job
- Used when we want to run a task that terminates after completing it's work. 
- ReplicaSets, DaemonSets run task continuously. If process exist, pod will get restarted. 
- // TODO - add image
- completion - If we need a job to run more than once. 
- parallelism - #no. of pods that can run in parallel. 
  
### Cron Job
- Batch jobs that needs to run at a specific time in the future or repeatedly in a specified interval. 
- //Todo - attach image
- schedule: "0,15,30,45 * * * *"
  - min, hr, day of month, month, day of week
  - 0,15,30,45 -> min mark of every hr of every day of month, every month and on every day of the week. 
  - ie. run every 15 min
- It should be idempotent.

## Services
- Pods needs a way of finding other pods if they want to consume the services they provide
- Specifying exact IP address or hostname wouldn't work in kubernetes: 
  - Pods are ephemeral - They may come and go at any time. 
  - Kubernetes assigns an IP address to a pod after it has been scheduled to a node
  - Horizontal scaling means multiple pods can provide the same service.
  - Solution? - Kubernetes Service
  
### Kubernetes Services
- is a resource to make a single, constant point of entry to a group of pods providing the same service. 
- Each service has an IP and a port that never changes while the service exist. 
- Todo - add a service image
- kubectl get svc // give the list of services
- It will give cluster-ip, it's only accessible from inside the cluster. (inside group of pods)
  
### Connecting to services living outside the cluster
1. Service endpoint
   1. Manually configuring service endpoints
   2. Creating an alias for external service
2. Exposing services to external clients
   1. Setting the service type to NodePort
      1. Each cluster node opens a port on the node itself. 
   2. Setting the service type to LoadBalancer
      1. Access the service via a dedicated load balancer. 
   3. Creating an Ingress resource

