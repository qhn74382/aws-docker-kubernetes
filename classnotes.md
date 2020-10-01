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
Run on each node / maintain the running pods / provide k8s runtime environment
  - kubelet
    - Agent running on each node
    - Communicates with the master via API
    - Ensures containers are running in the pods
  - container runtime
      - Resposible for downloading and running the containers
  - kube-proxy
    - Maintains network rules on the host
    - Run on each node responsible for watching API server for any changes on services to maintain the entire network configuration up to date
    - Ensure port can talk to another port / node talk to another node


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
