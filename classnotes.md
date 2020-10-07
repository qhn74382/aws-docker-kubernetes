## Objectives
 - Introduction to Docker and Kubernetes
 - Understand the major components of Kubernetes
 - Setup Kubernetes cluster using EC2
 - Deploy an application on Docker containers
 - Learn Kubernetes concepts via hands-on labs
 - Get Certified Kubernetes Application Developer (CKAD)
   and Certified Kubernetes Admininstrator (CKA) certification

## What is Kubernetes?

Kubernetes is an open-source system for automating deployment, scaling, and management of containerized applications

Container orchestration - groups containers that make up an application into logical units for easy management and discovery

## Advantages:
 - Automates various manual processes
 - Interacts with several groups of containers
 - Self-healing / Self-monitoring
 - Horizontal scaling
 - Storage orchestration
 - Automates rollout and rollbacks (Recovery from errors)
 - Container Load Balancing
 - Run everywhere / supports On-premise/Cloud/Hybrid Environment
 - Velocity
 - Immutability (container image consists of immutable layers, but can add another layer on top of them)
 - Declarative configuration
 - Decoupling / Supports microservices architecture / separation of concerns
 - Efficiency
 - Cost efficiency
 - Portability / Modularity

## Disadvantages:
 - Complexity to setup
 - Network latency
 - Manual configuration
 - Limits on Resources like CPU / Memory / Storage


## Main Components:

### Cluster
Consists of at least one cluster master and multiple worker machines called nodes. When the programs or applications are deployed onto a cluster, it handles distributing work to the individual nodes via the master

### Master Components (Decision Makers)
Also known as "Control Plane" of the cluster / Can be setup on any machine / After setup, it starts all the master components on that machine
  - kube-apiserver
    - Exposes k8s APIs
    - Processes the configuration file (Yaml or Json file - contains information such as name, label, and specifications about the entity - such as Pod, Service) for cluster provisioning

  - kube-scheduler
      - Head of all scheduling
      - Ensures pod rescheduling in case of node failure
  - kube-controller-manager
      - Runs the controllers such as Node Controllers, Replication Controllers, Endpoints Controller (A controller watches the state of the cluster through kube-apiserver and make changes to get to the desired state)
  - etcd
    - Storage for the k8s cluster (Key Value pair format)

### Node Components
Individual servers, machines, or virtual machines/ Run on each node / Nodes have the services necessary to run application containers and be managed from the master systems / maintain the running pods / provide k8s runtime environment
  - kubelet
    - Agent running on each node and ensures that the containers are running as intended
    - Communicates with the Master via API
    - Ensure decoupling - executed isolated application containers
  - container runtime
      - Resposible for downloading container images and running the containers
  - kube-proxy
    - Groups pods under a common access policy
    - Creates a virtual IP to allow proxied access to the pods in a service
    - Maintains network rules on the host
    - Run on each node responsible for watching API server for any changes on services to maintain the entire network configuration up to date
    - Ensure port can talk to another port / node talk to another node
    - Each node runs a kube-proxy process which programs iptables rules to trap access to service IPs and redirect the to the correct backends

<b>Important Note</b>: If there is no ReplicationController and a node dies or become unavailable, the pods on taht node gets terminated

## Object Management Models - Imperative vs Declarative
### Imperative Model
Explicitly define each state that lead to the final result
  - ### Kubernetes Command Approach
    - Is simple and often has single kubectl commands to make cluster updates
    - kubectl command
    - ### For example
      - To create a deployment object
        ```bash
        kubectl create deployment nginx --image nginx
        ```
      - To run nginx container
        ```bash
        kubectl run nginx --image nginx
        ```
    - ### Advantages
      - Simple and easy to remember
      - Single step to make changes to the cluster
    - ### Disadvantages
      - No integration with change review processes
      - No audit trail associated with changes
      - No source of recodes except for what is live
  - ### Imperative Object Configuration Approach
    - kubectl command specifies the operation (create, replace, etc) optional flags, and at least one file name that must contain full object definition
    - File (Yaml or Json) must contain a full definition of the object
    - ### For example
      - To create the objects defined in a configuration file myApp.yml
        ```bash
        kubectl create -f nginx.yaml
        ```
      - To delete the objects defined in the configuration file
        ```bash
        kubectl delete -f nginx.yaml
    - ### Advantages
      - Supports strong the configuration in source control system like Git
      - Changes can be reviewed before pushing and audit trail
      - Provides template for creating new objects
    - ### Disadvantages
      - Requires understanding of the object schema
      - Works with files, not directories
      - Updates to the live objects must be reflected in configuration files

### Declarative Model
  - define the final stage that is desired, and the system responsibility is to follow all the steps to achieve that state
  - Enables working on directories
  - User defines a desired state of the application in a file (or set of files)
  - User does not define the operations to be taken on the files
  - The prefered approach - operates on directories and automatically detects operation types (create, patch, delete) per-object 
  - For example
    - To process a configuration file, the user would use following command
      ```bash
      kubectl apply -f myApp.yml
      ```
    - To process all the configuration files in a directory myDirectory
      ```bash
      kubectl apply -f myDirectory/
      ```
  - Object Configuration File format: YAML or JSON
  - ### Advantages
    - Changes made directly to live objects are retained, even if they are not merged back into the configuration files
    - Support for operating on directories and automatically detecting operation types (create, patch, delete) per-object
  - Disadvantages
    - Harder to debug and undertand results when they are unexpected

## Overview of Docker and Containers
### Docker
  - Open-source project for automating the deployment of applications as portable, self-sufficient containers that can run on the cloud or on-premise
  - A host environment which allows the containerized applications to run

### Docker Host
  - Machine that has Docker components installed
  - On the development computer, the developer runs a Docker host where Docker images are deployed, inlcuding the app and its dependencies

### Container Image
  - A package with all the dependencies and information needed to create a container (e.g. OS Files and objectts, Manifest and the application files)
  - Includes all the dependencies (such as framework) plus deployment and execution configuration to be used by a container runtime
  - An image derives from mutliple base images that are layers stacked on top of each other to form the container's filesystem
  - The information about the layers is stored in a "manifest" file
  - An image is immutable once it has been created, however you can create another layer on top of the image

### Container
  - A running instance of a Docker image
  - A container represents the execution of a single application, process, or service. It consists of the contents of a Docker image, an execution environment, and a standard set of instructions
  - Multiple containers can run from a single image
  - Command to run a container from an image: docker run 'image-name'
  - When scaling a service, you create multiple instances of a container from the same image
  - A batch job can create mutliple containers from the same image, passing different parameters to each instance
  - The images are "build-time" constructs and containers are the "run-time" constructs

## Container States
### Created
  - A new container has been created from the image via command - docker create 'image name'

### Running
  - All processes within the container are running

### Paused
  - All processes with the container are suspended

### Restarting
  - The container is getting restarted via command - docker restart 'container name'

### Removed
  - Container is getting removed via command docker rm 'container name'

### Exited
  - The main process running in the container has exited

### Dead
  - Container is in a defunct state (e.g. issues with accessing their underlying storage)

## Container Registry
  - A service that provides access to repositories
  - All images are stored in registry
  - The default registry for most public images is Docker Hub
  - Companies often have private registries to store and manage imaes they've created

## Dockerfile
  - A text file that contains all commands, in order, needed to build a given image
  - Docker builds image automatically by reading the instructions from a Dockerfile
  - A Dockerfile adheres to a specific format and set of instructions
  - A Docker image consists of read-only layers each of which represents a Dockerfile instructions

## Deploy A Kubernetes cluster with 1 master and 2 workers
### Pre-Requisite Steps
  - Master Security Group
    - SSH 22
    - Custom TCP
      - API 6443
      - etcd 2379-2380
      - scheduler 10251
      - kubelet 10250
      - controller 10252
  - Worker Security Group
    - SSH 22
    - HTTP 80
    - nodeport 30000-32767
    - kubelet 10250

## Setup the Master Node first
```bash
#change hostname for master and workers
sudo hostnamectl set-hostname master
sudo hostnamectl set-hostname worker1
sudo hostnamectl set-hostname worker2

#add ubuntu to a docker group
sudo usermod -aG docker $USER
newgrp docker

sudo apt-get update
sudo apt-get install docker.io -y
#start and enable docker
sudo systemctl start docker
sudo systemctl enable docker

#install the kube environment that kubernetes needs for the master and initialize it to become the master
sudo apt-get update && sudo apt-get install -y apt-transport-https gnupg2
#download kubernetes projects
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
#get the keys to the repository to the installer
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list

sudo apt-get update

#allow the linux kernel to use network bridge correctly
cat << EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
#apply the configs to the system
sudo sysctl --system

#install kube packages
sudo apt-get install -y kubeadm kubectl kubelet
#start and enable kubelet (how pods communicate)
sudo systemctl start kubelet
sudo systemctl enable kubelet
```
### ------
```bash
#On the Master, Pull the kubeadm config image
sudo kubeadm config images pull


#Get Master and Worker Private IP address
Master  172.31.25.83
Worker1 18.234.238.125
Worker2 3.87.240.49

#On the Master, advertise the Master ip address
sudo kubeadm init  # --apiserver-advertise-address=172.31.25.83 --pod-network-cidr=172.16.0.0/16
#add a flag in case of error because we run on a smaller size instance
#sudo kubeadm init --apiserver-advertise-address=172.31.19.58 --pod-network-cidr=172.16.0.0/16 --ignore-preflight-errors=NumCPU
#to reset the kubeadm
#sudo kubeadm reset

#kube config is the key to the kingdom, if you don't have this file, you CAN'T interact with this cluster at all
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

#Create a manifest, the pod manager
mkdir manifests
cd manifests
#nano canal.yaml
  #copy the yaml file from this link
  # https://raw.githubusercontent.com/projectcalico/canal/master/k8s-install/1.7/canal.yaml

#Everything in Kubernetes, you usually apply for the imply
#Apply deployment to cluster
sudo kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml

#check to see if master is ready
kubectl get nodes

#Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 172.31.25.83:6443 --token qci143.4vmn48rm4lo9hor9 \
    --discovery-token-ca-cert-hash sha256:4257ba2e81d8f554d9ffbb491eb211eeb1a5eb6ae9d4c5020fa8179490c25bb3
```
## Set Up 4 Worker Nodes and add them to the cluster
```bash
#ssh into the workers

#update all packages
sudo apt-get update

#install docker
sudo apt-get install docker.io -y

#enable docker service
sudo systemctl start docker
sudo systemctl enable docker

#change hostname for master and workers
sudo hostnamectl set-hostname master
sudo hostnamectl set-hostname worker1
sudo hostnamectl set-hostname worker2

#add ubuntu to a docker group
sudo usermod -aG docker $USER
newgrp docker

#install the kube environment for the workers, we need the environment in order to add the workers to the cluster
sudo apt-get update && sudo apt-get install -y apt-transport-https gnupg2
#download kubernetes projects
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
#get the keys to the repository to the installer
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list

sudo apt-get update

#install kube packages
sudo apt-get install -y kubeadm kubectl kubelet
#start and enable kubelet (how pods communicate)
sudo systemctl start kubelet
sudo systemctl enable kubelet

#On the Master, create a token for the worker to join, if the token expired, generate a new one
sudo kubeadm token create --print-join-command

#copy the whole command and paste to the worker1 and worker2
kubeadm join 172.31.25.83:6443 --token 4k15k0.7ih0iynxu9a0j204     --discovery-token-ca-cert-hash sha256:4257ba2e81d8f554d9ffbb491eb211eeb1a5eb6ae9d4c5020fa8179490c25bb3

#On the master, check the status of the workers
kubectl get nodes


```








## Overview of Pods
### Important Points
  - A Pod is the basic building block of Kubernetes - the smallest and simplest atomic unit in the Kubernetes object model that you create or deploy
  - Pods are atomic
  - A pod can run on a single instance at a time
  - Pods enable scaling of the application
  - Pods are disposable
  - Pods are managed by controller
  - Pods can be deployed individually as well as declaratively
  - 1 Pod - 1 IP Address

### Pods - Networking
  - Each Pod is assigned a unique IP address
  - Every container in a Pod shares the network namespace, including the IP address and network ports
  - Containers inside a Pod can communicate with one another using localhost
  - When containers in a Pod communicate with entities outside the Pod, they must coordinate how they use the shared network resources (such as ports)

### Pods - Storage
  - Pods provides storage for data sharing among containers
  - A Pod can specify a set of shared storage volumes
  - All containers in the Pod can access the shared volumes, allowing those containers to share data
  - Volumes also allow persistent data in a Pod to survive in case one of the containers within needs to be restarted

### Pods - Containers
  - Pods consist of either one or more containers (e.g. Docker containers) that are tightly coupled and that share resources
  - The containers in a Pod are automatically co-located and co-scheduled on the same physical or virtual machine in the cluster
  - The containers can share resources and dependencies, communicate with one another, and coordinate when and how they are terminated
  - Pods in the unit of scaling - not containers!

### Pod - Lifecycle
  - Pending - The pod creation is waiting on one or more containers images to finish getting created
  - Running - Pod is successfully deployed, all containers inside it are provisioned, and running on the underlying node
  - Succeeded - All containers in the Pod have successfully deployed
  - Failed - At least one container has failed
  - Unknown - For some reason the stat of the Pod could not be obtained, typically due to an error in communicating with the host of the Pod

## Container Probes
