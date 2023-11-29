# Kubernetes-Notes
Kubernetes-Notes from "Kubernetes in Action"

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
- A pod is a group of one or more tightly related containers that will always run together on same worker node. 
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
    





