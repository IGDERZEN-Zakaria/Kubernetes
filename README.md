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













## TroubleShooting 

in case you get ImagePullBackoff 

check this video link : https://www.youtube.com/watch?v=1q7RLvwdkyo














