### **2: Working with Kubernetes Objects**

## Pods and Deployments in Minikube

### **1.1 Creating and Managing Pods**
In Kubernetes, a **Pod** is the smallest deployable unit. It consists of one or more containers that share the same network and storage resources. Pods are typically created and managed through YAML manifest files or by using `kubectl`.

Here’s a simple example of a YAML file to create a Pod:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: example-pod
spec:
  containers:
  - name: example-container
    image: nginx
    ports:
    - containerPort: 80
```

To create the Pod in Minikube, run:
```bash
kubectl apply -f pod.yaml
```

To view Pods:
```bash
kubectl get pods
```

### **1.2 Deployments and ReplicaSets**
A **Deployment** automates the creation, update, and management of Pods and ensures that the desired number of Pod replicas are running. Deployments manage **ReplicaSets**, which maintain a specified number of replica Pods.

Here’s an example of a Deployment manifest:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

Apply the deployment:
```bash
kubectl apply -f deployment/deployment.yaml
```

### **1.3 Scaling Deployments**
You can easily scale Deployments by adjusting the number of replicas in the deployment manifest or by using the `kubectl scale` command.

```bash
kubectl scale deployment nginx-deployment --replicas=5
```

---

## Services in Kubernetes

### **2.1 Types of Services: ClusterIP, NodePort, LoadBalancer**
In Kubernetes, a **Service** is an abstraction that defines how to expose an application running in Pods. It allows stable networking endpoints for communication between Pods and external users or other services.

- **ClusterIP**: Exposes the service only within the cluster.
- **NodePort**: Exposes the service on each Node's IP address at a static port.
- **LoadBalancer**: Exposes the service externally using a cloud provider’s load balancer.

Here’s an example of a NodePort service:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: example-service
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30007
```

Apply the service:
```bash
kubectl apply -f services/service.yaml
```

### **2.2 Exposing Services in Minikube**
Minikube allows easy service exposure via `minikube service`. This command helps access the service externally in a Minikube environment:

```bash
minikube service example-service
```

This will open the service in your default web browser if it’s a web-based service.

### **2.3 Accessing Services from Outside the Cluster**
To access services from outside the Minikube cluster, you can use **NodePort** or Minikube's IP. To get the Minikube IP, use:

```bash
minikube ip
```

Once you have the IP, access the service using `http://<Minikube-IP>:<NodePort>`.

---

## ConfigMaps and Secrets

### **3.1 Managing Configuration with ConfigMaps**
**ConfigMaps** allow you to decouple configuration artifacts from image content, making it easy to manage configuration updates without altering the container images.

Here’s an example of a ConfigMap:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: example-configmap
data:
  config-key1: value1
  config-key2: value2
```

Create the ConfigMap in Minikube:
```bash
kubectl apply -f configs/configmap.yaml
```

#### **Using ConfigMaps in a Pod (Environment Variable)**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-configmap-env
spec:
  containers:
  - name: app-container
    image: nginx
    env:
    - name: CONFIG_KEY1
      valueFrom:
        configMapKeyRef:
          name: example-configmap
          key: config-key1
```

```bash
kubectl apply -f configs/configmap-env.yaml
```

#### **Using ConfigMaps in a Pod (Mounted as a File)**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-configmap-file
spec:
  containers:
  - name: app-container
    image: nginx
    volumeMounts:
    - name: config-volume
      mountPath: /etc/config
  volumes:
  - name: config-volume
    configMap:
      name: example-configmap
```

```bash
kubectl apply -f configs/configmap-file.yaml
```

To access the ConfigMap inside the pod, you can enter the container:

```bash
kubectl exec -it pod-with-configmap-env -- /bin/sh
# Check environment variable
echo $CONFIG_KEY1

kubectl exec -it pod-with-configmap-file -- /bin/sh
# Check mounted file
cat /etc/config/config-key1
```

### **3.2 Securing Sensitive Data with Secrets**
Kubernetes **Secrets** are used to store and manage sensitive information such as passwords, tokens, or keys. They allow you to keep such information separate from the application code.

Here’s an example of a Secret manifest:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: example-secret
type: Opaque
data:
  username: dXNlcg==
  password: cGFzc3dvcmQ=
```

The values are Base64 encoded. To encode a string:
```bash
echo -n "user" | base64
```

Apply the Secret:
```bash
kubectl apply -f secret.yaml
```

#### **Using Secrets in a Pod (Environment Variable)**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-secret-env
spec:
  containers:
  - name: app-container
    image: nginx
    env:
    - name: SECRET_USERNAME
      valueFrom:
        secretKeyRef:
          name: example-secret
          key: username
```

#### **Using Secrets in a Pod (Mounted as a File)**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-secret-file
spec:
  containers:
  - name: app-container
    image: nginx
    volumeMounts:
    - name: secret-volume
      mountPath: /etc/secret
  volumes:
  - name: secret-volume
    secret:
      secretName: example-secret
```

To check the secrets inside the pod:

```bash
kubectl exec -it pod-with-secret-env -- /bin/sh
# Check environment variable
echo $SECRET_USERNAME

kubectl exec -it pod-with-secret-file -- /bin/sh
# Check mounted file
cat /etc/secret/username
```

---

## Kustomization in Kubernetes

**Kustomize** is a Kubernetes-native configuration management tool that allows you to customize Kubernetes YAML configurations without the need for templating.

### **4.1 Introduction to Kustomize**
Kustomize allows you to define overlays that modify base configurations for different environments (e.g., dev, staging, production) without duplicating the YAML files.

Some key concepts in Kustomize:
- **Bases**: Basic Kubernetes resources you want to reuse.
- **Overlays**: Customizations to the base resources for specific environments.

Kustomize is integrated into `kubectl`, so you don’t need additional tools to use it.

### **4.2 Setting Up a Kustomization Directory Structure**
The typical structure for Kustomize includes a base directory and environment-specific overlays. Here’s an example structure:

```
.
├── base
│   ├── deployment.yaml
│   └── kustomization.yaml
└── overlays
    ├── dev
    │   └── kustomization.yaml
    └── prod
        └── kustomization.yaml
```

### **4.3 Creating a Base Kustomization**
A **kustomization.yaml** file specifies the resources to include in the Kustomization process.

Example **base/kustomization.yaml**:
```yaml
resources:
  - deployment.yaml
```

### **4.4 Overlays for Environments**
You can create environment-specific overlays that adjust the base configurations. For example, the dev environment can scale down the number of replicas.

Example **overlays/dev/kustomization.yaml**:
```yaml
resources:
  - ../../base

patchesStrategicMerge:
  - replicas-patch.yaml
```

And the **replicas-patch.yaml** to override the number of replicas:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 1
```

### **4.5 Applying Kustomizations**
To apply a specific overlay (e.g., for the dev environment), use:
```bash
kubectl apply -k overlays/dev/
```

This applies the configuration, including any custom patches from the overlay.

---

With Kustomize, you can efficiently manage Kubernetes configuration for multiple environments, ensuring consistency and ease of deployment
