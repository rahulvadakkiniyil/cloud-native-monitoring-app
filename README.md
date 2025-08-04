# 🚀Cloud-Native Resource Monitoring Application on Kubernetes

## Overview
This project involves building a cloud-native resource monitoring application using Python, Flask, and psutil. The application is containerized with Docker, deployed on Kubernetes using KIND, and orchestrated with Kubernetes services. The objective is to provide a hands-on learning experience in developing, containerizing, and deploying cloud-native applications in a local Kubernetes environment.

![Cloud Monitoring App Screenshot](https://raw.githubusercontent.com/apurvagargote/Cloud-native-monitoring-app/main/Images/cloud-native-monitoring-app-image.png)


## 📌 Key Learning Outcomes
- Develop a resource monitoring application using Python and Flask.
- Execute and test the application locally.
- Understand Docker fundamentals and containerization.
- Create and implement a Dockerfile.
- Build and run Docker images.
- Utilize essential Docker commands for container management.
- Push Docker images to Docker Hub.
- Deploy applications on Kubernetes using KIND.
- Use `kubectl port-forward` to expose the application.
- Follow a structured, step-by-step deployment approach.

---

## 📌 Prerequisites
Before starting this project, ensure you have the following:
- ✅ Docker installed.
- ✅Kubernetes (KIND) installed and configured.
- ✅Python 3 installed.
- ✅A code editor (VS Code recommended).
- ✅A Docker Hub account.

---

## Project Implementation

### ✅ Step 1: Deploying the Flask Application Locally
#### Clone the Repository
Clone the code from the repository:
```bash
git clone <repository_url>
```
#### Install Dependencies
Install the required dependencies using pip:
```bash
pip3 install -r requirements.txt
```
#### Run the Application
Navigate to the project root directory and execute:
```bash
python3 app.py
```
The Flask server will start at `http://localhost:5000/`. Open the URL in a browser to access the application.

---

### ✅ Step 2: Containerizing the Flask Application
#### Create a Dockerfile
Create a `Dockerfile` in the root directory with the following content:
```dockerfile
# Use the official Python image as the base image
FROM python:3.9-slim-buster

# Set the working directory in the container
WORKDIR /app

# Copy the requirements file to the working directory
COPY requirements.txt .

RUN pip3 install --no-cache-dir -r requirements.txt

# Copy the application code to the working directory
COPY . .

# Set the environment variables for the Flask app
ENV FLASK_RUN_HOST=0.0.0.0

# Expose the port on which the Flask app will run
EXPOSE 5000

# Start the Flask app when the container is run
CMD ["flask", "run"]
```
#### Build the Docker Image
To build the Docker image, execute:
```bash
docker build -t <image_name> .
```
#### Run the Docker Container
To run the Docker container, execute:
```bash
docker run -p 5000:5000 <image_name>
```
The Flask application will be accessible at `http://localhost:5000/` within the containerized environment.

---

### ✅ Step 3: Pushing Docker Image to Docker Hub
#### Authenticate with Docker Hub
Login to your Docker Hub account:
```bash
docker login
```
#### Tag the Docker Image
Tag your image with your Docker Hub username:
```bash
docker tag <image_name> <docker_hub_username>/<image_name>:latest
```
#### Push the Image to Docker Hub
Push the tagged image to Docker Hub:
```bash
docker push <docker_hub_username>/<image_name>:latest
```

---

## ✅ Step 4: Deploying on Kubernetes using KIND
#### Create a KIND Cluster
Ensure KIND is installed, then create a new Kubernetes cluster:
```bash
kind create cluster --name my-cluster
```
#### Load the Docker Image into the KIND Cluster
```bash
kind load docker-image <docker_hub_username>/<image_name>:latest --name my-cluster
```
#### Kubernetes Manifest Files
To deploy the application on Kubernetes, we define the following manifest files:

**📄 1. Deployment (`deployment.yml`)**
This file defines the deployment strategy, including the number of replicas and pod configuration.
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: cloud-native-monitoring-app-deployment
  labels:
    app: cloud-native-monitoring-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: cloud-native-monitoring-app
  template:
    metadata:
      labels:
        app: cloud-native-monitoring-app
    spec:
      containers:
      - name: cloud-native-monitoring-app
        image: apurva1025/my-flask-app:latest
        ports:
        - containerPort: 5000

```

**📄 2. Pod Definition (`pod.yml`)**
This file creates a single standalone pod for running the Flask application.
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: cloud-native-monitoring-app-pod
spec:
  containers:
  - name: cloud-native-monitoring-app
    image: apurva1025/my-flask-app:latest
    ports:
    - containerPort: 5000
```

**📄 3. Service (`service.yml`)**
This file exposes the application within the cluster and enables communication between different components.
```yaml
apiVersion: v1
kind: Service
metadata:
  name: cloud-native-monitoring-app-service
spec:
  selector:
    app: cloud-native-monitoring-app
  ports:
    - protocol: TCP
      port: 5000
      targetPort: 5000
```
#### Deploy the Application on Kubernetes
Apply the Kubernetes manifest files:
```bash
kubectl apply -f deployment.yml
kubectl apply -f pod.yml
kubectl apply -f service.yml
```
#### Verify Deployment
Check if the pods and services are running:
```bash
kubectl get deployments
kubectl get pods
kubectl get services
```
#### 🌍 Expose the Application using Port-Forwarding
Since we're not using a LoadBalancer or NodePort, expose the service using port-forwarding:
```bash
kubectl port-forward service/cloud-native-monitoring-app-service 5000:5000
```
Now, access the application at `http://localhost:5000/`.

---
For contributions, feedback, or improvements, feel free to collaborate. Happy coding 🤝!
