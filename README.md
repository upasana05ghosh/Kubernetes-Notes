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
    - [Ingress Resource](#ingress-resource)
    - [Signaling when a pod is ready to accept connections](#signaling-when-a-pod-is-ready-to-accept-connections)
  - [Understanding Kubernetes internals](#understanding-kubernetes-internals)
    - [Understanding the scheduler](#understanding-the-scheduler)
    - [Cluster events](#cluster-events)
  - [Managing pod's compuational resources](#managing-pods-compuational-resources)
    - [Resource request](#resource-request)
    - [Limit](#limit)
    - [Exceedign the limits](#exceedign-the-limits)
    - [Understanding how apps in container see limits](#understanding-how-apps-in-container-see-limits)
  - [Automatic scaling of pods and cluster nodes](#automatic-scaling-of-pods-and-cluster-nodes)
  - [Kubernetes best practices](#kubernetes-best-practices)

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
     1. <img width="421" alt="image" src="https://github.com/upasana05ghosh/Kubernetes-Notes/assets/17885669/ac637f65-4fed-4438-bb6c-66c22b3b2a3e">
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
  <img width="743" alt="image" src="https://github.com/upasana05ghosh/Kubernetes-Notes/assets/17885669/c4e12c76-0bb5-422a-8d21-42d7553405bf">
  <img width="908" alt="image" src="https://github.com/upasana05ghosh/Kubernetes-Notes/assets/17885669/5e8d460b-bcb3-409b-9c05-4c1b9142e110">
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
<img width="632" alt="image" src="https://github.com/upasana05ghosh/Kubernetes-Notes/assets/17885669/72b7fe97-a9ea-4bd8-ac8a-910f4622d046">

### DaemonSets
- When you want a pod to run on each and every node in the cluster and run exactly once. 
- Ex - Run a log collector and a resource monitor. 
- If a node goes down, the DaemonSet doesn't cause the pod to be created elsewhere. But when a new node is added to the cluster, the DaemonSet deploys a new pod instance in it. 
- We can specify DaemonSet to run on a certain nodes.
<img width="662" alt="image" src="https://github.com/upasana05ghosh/Kubernetes-Notes/assets/17885669/dbe1a3cd-5e9f-4880-ab3c-f57fd96981fd">

### Job
- Used when we want to run a task that terminates after completing it's work. 
- ReplicaSets, DaemonSets run task continuously. If process exist, pod will get restarted. 
<img width="643" alt="image" src="https://github.com/upasana05ghosh/Kubernetes-Notes/assets/17885669/d5b375ca-af66-4d1f-aace-e9bcc33afaa8">

- completion - If we need a job to run more than once. 
- parallelism - #no. of pods that can run in parallel. 
  
### Cron Job
- Batch jobs that needs to run at a specific time in the future or repeatedly in a specified interval. 
<img width="678" alt="image" src="https://github.com/upasana05ghosh/Kubernetes-Notes/assets/17885669/13e11a11-c81a-4a90-94aa-3a4cf8a2b37d">

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
<img width="443" alt="image" src="https://github.com/upasana05ghosh/Kubernetes-Notes/assets/17885669/e09e3c8c-f05b-4284-b606-fef11e014297">
- kubectl get svc // give the list of services
<img width="708" alt="image" src="https://github.com/upasana05ghosh/Kubernetes-Notes/assets/17885669/00f15b0a-8679-4b3f-b588-77786a521e7b">
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

### Ingress Resource
- Create an Ingress controller runnig in a cluster
- <img width="601" alt="image" src="https://github.com/upasana05ghosh/Kubernetes-Notes/assets/17885669/10d72e7d-ec6c-4370-a745-0b14eda7a598">
- kubectl get ingresses // list of ingresses
- We can map different services on different parths and ports
- Configure Ingress to handle TLS traffic (HTTPS)
  - Create a TLS Certifiate for the ingress
  - <img width="635" alt="image" src="https://github.com/upasana05ghosh/Kubernetes-Notes/assets/17885669/05796d03-a052-4ae8-891f-07ed17b70f42">
  <img width="609" alt="image" src="https://github.com/upasana05ghosh/Kubernetes-Notes/assets/17885669/3616b287-175a-42bd-8817-a51258b0edec">

### Signaling when a pod is ready to accept connections
- Pod during start up may need time to load configuration or data or may need to perform a warm-up to prevent the first user request from taking too long. 
- Readiness Probe
  - Invoked periodically and determies whether the specific pod should receive request or not. 
  - Types of Readiness Probes
    - Exec probe - a process is executed and status is determined by the process exit status code. 
    - HTTP Get probe - sends an HTTP Get requet and status code determine status of container. 
    - TCP Socket Probe - opens a TCP connection to a port of the container. If connection is established, the container is ready. 
- In liveness probes, if a container fails, it will be killed or restarted. 
- In Readiness probes, if a container fails the check, it won't be killed or restarted. Instead it will removed from the service. If the pod becomes ready again, it's re-added. 
- <img width="585" alt="image" src="https://github.com/upasana05ghosh/Kubernetes-Notes/assets/17885669/9e1466c7-f414-47da-a7e6-25d2327ac24e">

## Understanding Kubernetes internals

### Understanding the scheduler
- kubectl get pods --watch
- kubectl get pods -o yaml --watch
- Scheduler specify which cluster node a pod should run on. 

### Cluster events
- Both the Control Plane component and Kubelet emit events to the API server as they perform these actions. 
- kubectl get events --watch

## Managing pod's compuational resources
- Request - specify the amount of CPU and memory that a container needs. Min limit
- Limit - Hard limit on what it may consume. Max limit
- Specific to each containers and not for the pod as a whole. 
- <img width="712" alt="image" src="https://github.com/upasana05ghosh/Kubernetes-Notes/assets/17885669/a7ef0d4c-cd50-4e4e-b1dc-aee3c0fb9beb">
- We can see CPU consumption by running `top` command inside the contianer
- ```
  kubectl exec -it <pod-name> top
  ```

### Resource request
- resource requests -  min amount of resources required by the pod. 
- When scheduling a pod, the Scheduler will only consider nodes with enough unallocated resources to meet the pod's resource requiremnt. 

### Limit 
- Containers are allowed to use up all the CPU if other process are sitting idle. 
- If we want to prevent certain containers from using up more than a specific amount of CPU, we need to limit the amoun of memory/cpu a container can consume. 
- <img width="529" alt="image" src="https://github.com/upasana05ghosh/Kubernetes-Notes/assets/17885669/fc7d5860-2fb7-4d86-b5d5-51e5b45c57da">
- If we only specify limit and not request, the value of the limit will be set as requested. 
- The sum of resource limits of all pods on a node can exceed 100% of the node's capactiy. 

### Exceedign the limits
- When a CPU limit is set for a container, the process isn't given more CPU time than the configured limit. 
- When a process tries to allocate memory over its limit, the process is killed. (OOMKilled - OOM - out of Memory)
- CrashLoopBackOff - it means kubectl is restarting in exponential backoff time. 
  - 0s, 20s, 40s, 160, 300s .. 300s..300s (5 min)

### Understanding how apps in container see limits
- top command shows the memroy amount of the whole node the container is running on. 
- Even though we set a limit on how much memory is avilable to a container, the container will not be aware of the limit. 
- Container will also see all node's CPUs regardless of the CPU limit configured for the container. 

## Automatic scaling of pods and cluster nodes
- Kubenetes can monitor pods and scale them up automatically as soon as it detects an increase in the CPU usage or some other metric. 
- Horizontal pod autoscaling
  - performed by Horizontal controller, which is enabled and configured by creating a HorizontalPodAutoscaler (HPA) resource. 
  - Steps:
    - Obtain metrics
    - Calculate no. of pods requried to bring the metric.
    - Update the replicas field
1. Obtain metrics - Pods and node metrics are collected by an agent called cAdvisor, (runs in Kubelet)
   - They are aggregated by cluster-wide component called Heapster. 
2. Autoscalar - It sums up the metric values of all the pods, dividing that by the target value set on the HorizontalPodAutoscaler. 
   1. <img width="674" alt="image" src="https://github.com/upasana05ghosh/Kubernetes-Notes/assets/17885669/c379a73a-1b46-4b99-8ed7-6e23eddabdb6">
3. Updating the desired replica count

- Create a HPA object
```
kubectl autoscale deployment <pod-name> --cpu-percent=30 --min=1 --max=5
```
It's limiting cpu usage by 30%, there should be at least 1 replica and at most 5. 
- List HPA object
```
kubectl get hpa
```
- Scaling based on memory consumption
  - Issue - After scaling up, the old pods would somehow need to be forced to release memory. 
    - This needs to be done by the app itself - it can't be done by the system. 

## Kubernetes best practices
- Starting pod in a specific order
  - Init containers
  - Pods can include init container - Used to initialize the pod
  - <img width="688" alt="image" src="https://github.com/upasana05ghosh/Kubernetes-Notes/assets/17885669/582b0b4e-0004-4a33-b1a6-e06906dd1ca7">
  - When we deploy this pod, only it's init container is started. 
  - Best practice - it's much better to write apps that don't require every service that rely on. 
    - If there are depenedencies, make sure to include readiness probes. 

- Adding Lifecycle hooks
  - Post-start hooks
    - executed immediately after the container's main process is started. 
    - We can add it in the code, but if the code is developed by someone else, we dont' want to modify the code. 
    - Post-start hooks allow us to run additional commands without touching the app
    - The hook runs in async mode. 
    - Until the hook completes, container stay in Waiting state instead of Running. 
    - <img width="764" alt="image" src="https://github.com/upasana05ghosh/Kubernetes-Notes/assets/17885669/32c8f4ac-2716-40de-97a4-517bb9854285">
  - Pre-stop hooks
    - Executed immediately before a container is terminated.
    - <img width="625" alt="image" src="https://github.com/upasana05ghosh/Kubernetes-Notes/assets/17885669/ccd4ce26-0839-4867-9b11-18948ab5b500">

- Providing information on why the process terminated
  - We can do it by having the process write the reason why a container terminated in the pod's status. 
  - The default file is /dev/termination-log but it can be changed by terminationMessagePath





