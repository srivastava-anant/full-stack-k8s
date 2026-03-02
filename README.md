# 🚀 Full-Stack Chat App Deployment Guide: Kubernetes (Kind) & Docker Compose

This project demonstrates a production-ready deployment of a full-stack chat application using Kubernetes. It showcases the orchestration of a React frontend, a Node.js backend, and a MongoDB database with persistent storage.


1. Set up a local Kubernetes environment using **Minikube**.
2. Deploy a **Full-Stack Chat Application** (Frontend, Backend, MongoDB) on **Kubernetes**.
3. Explore an alternative deployment using **Docker Compose**.

   



### 2. Create Namespace and Base Resources

A **namespace** is a way to organize your resources. It keeps the app's resources isolated and easy to manage. To create the namespace for our chat app, run:


```bash
# Create namespace
kubectl apply -f k8s/namespace.yaml

# Verify namespace creation
kubectl get namespaces
```

### 3. Create Storage and Configurations

MongoDB is used for storing chat messages and user data. To deploy MongoDB, apply the following commands:

```bash
# Create MongoDB PVC
kubectl apply -f k8s/mongo-pvc.yaml -n chat-app

# Create backend secrets
kubectl apply -f k8s/backend-secrets.yaml -n chat-app

# Create frontend nginx config
kubectl apply -f k8s/frontend-configmap.yaml -n chat-app
```

### 4. Deploy MongoDB
```bash
# Deploy MongoDB
kubectl apply -f k8s/mongodb-deployment.yaml -n chat-app
kubectl apply -f k8s/mongodb-service.yaml -n chat-app

# Wait for MongoDB pod to be ready
kubectl wait --for=condition=Ready pods -l app=mongodb -n chat-app --timeout=120s
```


### 5. 🖥️ Deploy Backend Service

The **backend** service processes messages and user authentication. To deploy the backend, run the following commands:

```bash
# Deploy Backend
kubectl apply -f k8s/backend-deployment.yaml -n chat-app
kubectl apply -f k8s/backend-service.yaml -n chat-app

# Wait for Backend pod to be ready
kubectl wait --for=condition=Ready pods -l app=backend -n chat-app --timeout=120s
```

These files will deploy the backend service, which will handle all API requests from the frontend.

### 6. 🌐 Deploy Frontend Service

The **frontend** is the user interface where people interact with the chat app. To deploy the frontend, use these commands:

```bash
# Deploy Frontend
kubectl apply -f k8s/frontend-deployment.yaml -n chat-app
kubectl apply -f k8s/frontend-service.yaml -n chat-app

# Wait for Frontend pod to be ready
kubectl wait --for=condition=Ready pods -l app=frontend -n chat-app --timeout=120s
```

This will launch the frontend UI and expose it to the web.

---

## 🧐 Verification and Management

Once the app is deployed, it's crucial to verify that everything is running smoothly.

```bash
# Check all resources
kubectl get all -n chat-app

# Check pod logs if needed
kubectl logs -f -l app=frontend -n chat-app
kubectl logs -f -l app=backend -n chat-app
kubectl logs -f -l app=mongodb -n chat-app
```
## Accessing the Application

The application is exposed through NodePort services:
http://localhost:8080

You can verify the service URLs using:
```bash
# Get service details
kubectl get svc -n chat-app
```

### 🔍 Describe a Pod

If a pod isn’t working as expected, you can describe it to get more information:

```bash
kubectl describe pod <pod-name> -n chat-app
```

This will give you detailed information about a specific pod, including any potential issues.

## Cleanup

When you're done, you can clean up using:
```bash
# Delete all resources in namespace
kubectl delete namespace chat-app

# Delete the kind cluster
kind delete cluster --name chat-app-cluster
```

---

## 🐳 Docker Compose (Alternative Local Deployment)

If you prefer a simpler local setup using **Docker Compose**, you can deploy the chat app without Kubernetes. Here’s how to do it:

```bash
docker-compose up -d --build
```

This command:
- Starts all services defined in the `docker-compose.yml` file.
- Runs the services in detached mode (`-d`).
- Rebuilds the Docker images (`--build`) in case of any changes.

Once the services are running, you can access the app at [http://localhost:8080](http://localhost:8080).



