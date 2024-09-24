### **1: Introduction and Setup**

## Overview of Kubernetes and Minikube

### **1.1 Introduction to Kubernetes: Concepts and Architecture**
Kubernetes is an open-source container orchestration platform designed to automate the deployment, scaling, and management of containerized applications. It groups containers into logical units for easy management and discovery, making application development, deployment, and scaling easier.

Kubernetes architecture consists of:
- **Master Node**: Manages the cluster and coordinates the workers (scheduling, load balancing, scaling).
- **Worker Nodes**: Run the actual application workloads, handling containerized apps.
- **Pods**: The smallest deployable units that can be created, deployed, and managed.
- **Services**: Define a set of Pods and a way to access them.
- **Controllers**: Manage the life cycle of Pods, ensuring the desired state.

### **1.2 What is Minikube?**
Minikube is a lightweight Kubernetes implementation that creates a VM on your local machine and runs a single-node Kubernetes cluster. It is ideal for local development and testing purposes, allowing developers to experiment with Kubernetes without needing a large cloud infrastructure.

### **1.3 Use Cases for Minikube in Local Development**
- Testing Kubernetes configurations and deployments locally.
- Developing and experimenting with Kubernetes features.
- Creating demos, POCs, and learning environments.

---

## Setting up Minikube for Development

### **2.1 System Requirements**
To install and run Minikube, your system should meet the following requirements:
- **OS**: Linux, macOS, or Windows.
- **RAM**: 2GB or more (recommended).
- **Disk Space**: At least 20GB of free space.
- **Virtualization**: Ensure your system supports VT-x or AMD-v virtualization.

### **2.2 Installing Minikube**
1. Download Minikube binary from the official [Minikube website](https://minikube.sigs.k8s.io/docs/start/).
2. Follow the installation guide specific to your operating system.

```bash
# On Linux/macOS
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

### **2.3 Configuring Hypervisors (VirtualBox, Docker, etc.)**
Minikube can run on different virtualization platforms such as:
- **VirtualBox**: Lightweight and easy to set up.
- **Docker**: Use if Docker is already installed for containerization.

To start Minikube with a specific driver (e.g., Docker):
```bash
minikube start --driver=docker
```

### **2.4 Verifying Installation**
After installation, verify that Minikube is working by starting it and checking the status:
```bash
minikube start
minikube status
```

---

## Kubernetes Command Line Interface (kubectl)

### **3.1 Installing kubectl**
`kubectl` is the command-line tool for Kubernetes. It allows interaction with Kubernetes clusters.

To install `kubectl`:
```bash
# macOS
brew install kubectl

# Linux
curl -LO "https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl"
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
```

### **3.2 Basic kubectl Commands for Interacting with Minikube**
- **Check Cluster Info**:  
  ```bash
  kubectl cluster-info
  ```
- **Get Pods, Services, Nodes**:  
  ```bash
  kubectl get pods
  kubectl get services
  kubectl get nodes
  ```

---

## Exploring Minikube Commands

### **4.1 Starting, Stopping, and Deleting a Cluster**
- **Start Minikube**:  
  ```bash
  minikube start
  ```
- **Stop Minikube**:  
  ```bash
  minikube stop
  ```
- **Delete Minikube Cluster**:  
  ```bash
  minikube delete
  ```

### **4.2 Using Minikube Add-ons**
Minikube has several add-ons to extend functionality. To enable and list add-ons:
```bash
minikube addons list
minikube addons enable ingress
minikube addons enable metrics-server
```

### **4.3 Accessing Kubernetes Dashboard**
Minikube comes with a built-in Kubernetes dashboard:
```bash
minikube dashboard
```