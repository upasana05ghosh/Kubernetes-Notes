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
     <img width="590" alt="image" src="https://github.com/upasana05ghosh/Kubernetes-Notes/assets/17885669/3b1237eb-967f-4c2a-95c2-db0e9e2d526b">

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
