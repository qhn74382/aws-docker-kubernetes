Objectives
 - Introduction to Docker and Kubernetes
 - Understand the major components of Kubernetes
 - Setup Kubernetes cluster using EC2
 - Deploy an application on Docker containers
 - Learn Kubernetes concepts via hands-on labs
 - Get Certified Kubernetes Application Developer (CKAD)
   and Certified Kubernetes Admininstrator (CKA) certification

What is Kubernetes?

Kubernetes is an open-source system for automating deployment, scaling, and management of containerized applications

Container orchestration - groups containers that make up an application into logical units for easy management and discovery

Advantages:
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

Disadvantages:
 - Complexity to setup
 - Network latency
 - Manual configuration
 - Limits on Resources like CPU / Memory / Storage

Main Components:
  - Cluster
    - consists of at least one cluster master and multiple worker machines called nodes. When the programs or applications are deployed onto a cluster, it handles distributing work to the individual nodes via the master

Master-Node architecture

  - Master Components (Decision Makers) - also known as "Control Plane" of the cluster / Can be setup on any machine / After setup, it starts all the master components on that machine
    - kube-apiserver = exposes k8s APIs / processes the configuration file (Yaml or Json file - contains information such as name, label, and specifications about the entity - such as Pod, Service) for cluster provisioning

    - kube-scheduler = head of all scheduling / ensures pod rescheduling in case of node failure
    - kube-controller-manager = runs the controllers such as Node Controllers, Replication Controllers, Endpoints Controller (A controller watches the state of the cluster through kube-apiserver and make changes to get to the desired state)
    - etcd

  - Node Components
    - kubelet
    - container runtime
    - kube-proxy


