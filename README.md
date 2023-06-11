# Kubernetes Tutorial

##  Prerequisites
In order to work with kubernetes

- Docker 23.0.2
- Docker Desktop 4.20.0

## Getting Started

### Kubernetes (k8s)

- Kubernetes means pilot in greek language
- written in **golang**
- Google product started with **Borg -> Omega -> Kubernetes**
- Deploys and manages applications (containers) **[Application Orchestrator]**
- scale up and down according demand
- Zero downtime deployments
- Rollbacks

### Cluster

- a cluster is a set of nodes **( Master node / Worker node)**
- Node **-->** VM or physical machine
- Master node is the brain of the Cluster
- Master node and Worker node communicate via the **Kubelet** 

<img src="images/Kubernetes Cluster.png" width="740" height="400"></img>


### Master node

- Master node contains the control plane
- Master node runs all cluster's control plane services
- the brain where control and decisions are made


<img src="images/Master node.png" width="740" height="400"></img>

#### API Server (**api**)

- frontend to kubernetes Control plane
- All communications go through API Server **External** and **Internal**
- Exposes Restfull  API on **port 443**
- Authentication and Authorization checks 
- uses Kubectl client to communicate externally **kubectl apply -f**

#### Cluster store | state (**etcd**)

- stores configuration and the state of the entire cluster
- Distributed key value store
- Single source of truth (**Data base**)
- for documentation check this link : **https://etcd.io/**

#### Scheduler (**sched**)   

- monitoring new workloads/pods  and assigning them to a node based on several scheduling factors
- health checking
- resource checking 
- port availability checking
- Affinity and Non affinity rules

#### Controller manager (**c-m**)

- Daemon that manages the control loop (**Controller of controllers**)
  - Node controller
  - ReplicaSet
  - Endpoint
  - Namespace
  - Service Accounts
 

<img src="images/Controller manager.png" width="540" height="240"></img>


#### Cloud Controller manager (**c-c-m**)

- Responsible to interact with underlying cloud provider(**AWS-AZURE-GCP**) 
- Load balancers
- Storage
- Instances

### Example of creating a loadbalancer


<img src="images/Loadbalancer Ingress.png" width="940" height="350"></img>


### Worker Node

<img src="images/Worker node.png" width="230" height="200"></img>


- VM or physical machine running linux
- provides running environment for our applications 


<img src="images/WorkerNode.png" width="740" height="360"></img>


#### Kubelet

- Main agent that runs on every node
- receives pod definitions from API Server
- Interacts with container Runtime to run containers associated with the Pod
- Reports Node and Pod state to Master node

#### Container Runtime

- Responsible for pulling images from container registries (**DockerHuB - ECR - GCR - ACR**)
- Running containers and abstracting container management for kubernetes
- Container Runtime Interface  **(CRI)**  
  - Interface for 3rd party container runtime
  - **Containerd** (instead of docker that did become deprecated in kubernetes)

- for documentation check this link : **https://containerd.io/**

#### Kube Proxy

- Agent that runs on every node through a **DaemonSet**
- Responsible for 
  - Local Cluster networking
  - each node gets own unique IP Address
  - Routing network traffic to load balanced services

### Running Kubernetes 

- Running kubernetes ourselves ( **really hard** )
- using managed k8s
  - EKS - Elastic k8s Service
  - GKS - Google k8s Engine
  - AKS - Azure k8s
  - others

<ins>Managed kubernetes means that the Master node including its services are fully managed

for us and we just have to focus on the worker nodes and this where our applications do run</ins>


### Running Kube Cluster locally (**Creating a local cluster**)

- minikube
- kind
- docker


  - only used for leaning purposes
  - Local development CI
  - [**Important Note**](): Do not use it for any environment including production

### Docker

Check this link : https://docs.docker.com/get-started

```
docker version
```
### Minikube

minikube is a local kubernetes 

Check this link : https://minikube.sigs.k8s.io/docs/start/

```
minikube version
```
#### Starting the cluster 

```
minikube start
```
#### Checking status

```
minikube status
```
if minikube fails to start check this link : https://minikube.sigs.k8s.io/docs/drivers/

#### Checking ip address for Master node 

```
minikube ip
```
![master node ip address.png](images%2Fmaster%20node%20ip%20address.png)

### Kubectl

kubectl will allow us to interact with our cluster from our machine 

- K8s Command line tool 
- Run commands gains our cluster (Deploy / Inspect / Edit Ressources / Debug / View Logs)

check this link : https://kubernetes.io/docs/tasks/tools/

![kubectl status.png](images%2Fkubectl%20status.png)

#### checking Kubectl version

![kubectl version.png](images%2Fkubectl%20version.png)

### Kubernetes hello world

```
docker run --rm -p 80:80 amigoscode/kubernetes:hello-world
```

##### Creating a pod with a container inside of it 

```
kubectl run hello-world --image=amigoscode/kubernetes:hello-world --port=80
```
##### display pods

![display pods.png](images%2Fdisplay%20pods.png)

##### Access the application within the pod

```
kubectl port-forward pod/hello-world 8080:80
```
![port-forward.png](images%2Fport-forward.png)

##### deleting the pod
```
kubectl delete pod hello-world
```

### exploring the cluster

##### displaying nodes
```
kubectl get nodes
```

##### displaying all pods in all namespace

```
kubectl get pods -A
```

![display all pods.png](images%2Fdisplay%20all%20pods.png)

##### After creating hello-world pod

![display all pods 2.png](images%2Fdisplay%20all%20pods%202.png)

<ins>Note that we don't have Cloud-Controller-Manager because we are not running Kubernetes within AWS or Google cloud, we are running it within our machine</ins>

### SSH into Nodes

```
minikube ssh --node=Node_Name
minikube ssh --node=minikube
minikube ssh
```

##### Stopping the cluster

```
minikube stop
```

##### Fully deleting the cluster

```
minikube delete
```

### Cluster with 2 Nodes (1 Master Node + 1 Worker Node)

We intend to deploy everything on Worker Node

#### Creating a cluster with 2 Nodes 
```
minikube start --help
minikube start --nodes=2
```

#### Displaying Cluster pods / nodes

- Nodes => VM or Physical machines
- Pods => Containers

```
minikube status
kubectl get nodes 
kubectl get pods -A
```

![2 Nodes.png](images%2F2%20Nodes.png)

#### Displaying ip addresses for Master node + Worker node
```
# Master Node (returns 192.168.49.2)
minikube ip

# Worker Node (returns 192.168.49.3)
minikube ip --node=minikube-m02
```

<img src="images/Clsuter.png" width="740" height="400"></img>

### Minikube logs

```
minikube logs

# '-f' flag is used for debbuging purposes to tail our log
minikube logs --node='minikube-m02' -f
```
### Pods

- Is the smallest deployable unit
- Group of 1 or more containers
- Shares network and volumes
- Never use pod on its own , always use controllers instead (ex: deployment)
- Ephemeral or disposable 

<img src="images/Pods.png" width="740" height="400"></img>


### Creating Pods

- **Imperative command** 
- **Declarative configuration**

<img src="images/Imperative.png" width="340" height="200"></img>

<img src="images/Declarative.png" width="480" height="240"></img>


#### Declarative vs Imperative

- **Imperative**
  - Learning
  - Troubleshooting
  - Experimenting "kubectl"

- **Declarative**
  - Reproductible
  - Best Practices


### Creating pods with Imperative command

```
kubectl run hello-world --image=amigoscode/kubernetes:hello-world --port=80
kubectl get pods
```
<ins>Note</ins>
In order to access the application tuning on the pod we can use port-forward, 
but it's mainly used for **testing purposes**

```
kubectl port-forward pod/hello-world  8080:80
```

### Creating pods with Declarative configuration

```
cd Desktop/Kube/yamls
kubectl apply -f pod.yml 
kubectl get pods
```

-f, --filename=[]:
The files that contain the configurations to apply (works on json or yaml files).


### Create and delete ressources

Namespaces are a way to organize clusters into virtual sub-clusters

```
kubectl get namespaces
# or
kubectl get ns
```

![namespaces.png](images%2Fnamespaces.png)

```
cd Desktop/Kube/yamls
kubectl apply -f pod.yml 
kubectl get pods
kubectl delete -f pod.yml 
kubectl get pods
```

### List ressources

```
# list all ressources (default namespace)
kubectl get all

# list all ressources in all namespaces
kubectl get all -A
```

![All ressources.png](images%2FAll%20ressources.png)


```
# getting all pods on kube-system namespace
kubectl get pod --namespace=kube-system
# or
kubectl get pod -n kube-system
```

### Describe a ressource

```
kubectl describe pod hello-world

# Short version
kubectl get pod hello-world -o wide

# To get pod definition
kubectl get pod hello-world -o yaml 
# or 
kubectl get pod hello-world -o json
```

### Debugging 

```
kubectl logs hello-world
# tailling the logs 
kubectl logs hello-world -f

# specify the container withing the pod name 

kubectl logs hello-world -c hello-world
kubectl logs Pod_Name -c Container_Name
```

### Shell access to a running pod

```
kubectl exec -it hello-world -- sh 
#or 
kubectl exec -it hello-world -- bash 
#or 
kubectl exec -it hello-world -c hello-world -- sh

kubectl exec hello-world ls
```

### Access Pod via port-forward

```
kubectl port-forward hello-world 8082:80
#or
kubectl port-forward pod/hello-world 8082:80
kubectl port-forward service/Service_Name 8081:80
```

### List all resources types + Cheat Sheet
```
kubectl api-resources
kubectl get no -o wide
kubectl --help
```

check this link : https://kubernetes.io/docs/reference/kubectl/cheatsheet

### Using Pods

- Never deploy pods using **kind:Pod**
- Pods are ephemeral ( expected to die / Short lifespan)
- Pods on their own don't self-heal

### Deployments

- Manages release of new application
- Zero downtime deployments
- Creating ReplicaSet

```
kubectl apply -f deployment.yml
kubectl get deployments
kubectl describe deployment hello-world

kubectl delete deployment hello-world
#or 
kubectl delete -f deployment.yml
```

### ReplicaSets

- Is a background control loop that ensures that the number of Pods are always present on the Cluster
- Like Pods never create ReplicaSets on their own always use Deployments
- If we want to delete a ReplicaSet we have to delete the Deployment instead of deleting the ReplicaSet

we add

**spec:**

  **replicas: 3**

to deployment.yml to **scale our Deployment Replicas**

```
kubectl apply -f deployment.yml 
kubectl port-forward deployment/hello-world 8081:80
```
- We should be using Services to access out applications instead of using Port-forward


![3 pods.png](images%2F3%20pods.png)

```
kubectl get rs
kubectl describe rs hello-world
```

![replicas.png](images%2Freplicas.png)

### Deployment and Rolling Updates

Live display of Pods

```
kubectl get pods -w
```

we change the configuration as below 

![rollback.png](images%2Frollback.png)

```
# Applying the updated config 

kubectl apply -f deployment.yml
kubectl get pods -w

# to check the new version  
kubectl port-forward deployment/hello-world 8081:80
```

<ins>Note that : the old replicaSet will still exist to facilitate rollbacks</ins>

<img src="images/rollback_replicaSet.png" width="740" height="360"></img>

#### Checking ReplicaSets

```
# should display 2 ReplicaSet

kubectl get rs 
```

#### Display history of changes of configuration

```
kubectl rollout history deployment hello-world
```

![history.png](images%2Fhistory.png)

#### Rollback to a specific revision of configuration

```
# if we don't specify the revision it will go back to the previous one 

kubectl rollout undo deployment hello-world --to-revision=1
kubectl rollout history deployment hello-world
kubectl port-forward deployment/hello-world 8081:80
```

![history 2.png](images%2Fhistory%202.png)


#### get more information about a specific revision

```
kubectl rollout history deployment hello-world --revision=4
```

![history 3.png](images%2Fhistory%203.png)


### Managing our Cluster Using Declarative Approach













## TroubleShooting 

in case you get ImagePullBackoff 

check this video link : https://www.youtube.com/watch?v=1q7RLvwdkyo

### Kubernetes Plugin

to add a new template  check this image

![Templates.png](images%2FTemplates.png)









