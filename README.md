# Kubernetes-Notes
Kubernetes-Notes from "Kubernetes in Action"

## What is Kubernetes
 - open source
 - orchestrator
 - for deploying containerized app

## Architecture of Kubernetes
<img width="870" alt="image" src="https://github.com/upasana05ghosh/Kubernetes-Notes/assets/17885669/a051be3d-48ba-4cf1-bb61-37c4a7d3f742">

* API Server - We and other control plane component communicates with it.
* Scheduler - Which schedule your app (assign a worker node to each deployable component of your app)
* Controller Manager - performs cluster level functions like replicating components, handling node failure
* etcd - store cluster config 
    





