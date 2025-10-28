📁 Folder: myapp/

Your project folder should look like this:

myapp/
│
├── Dockerfile          # Used to build your app’s Docker image
├── deployment.yaml     # Kubernetes Deployment definition
├── service.yaml        # Kubernetes Service definition
├── app.py              # Your actual application code
└── deploy.sh           # Automation script to build, push & deploy

🧩 Step-by-Step Process to Use This Setup
🪜 Step 1 — Verify your files
🔸 1. Dockerfile (example)

Make sure your Dockerfile looks something like this (for a Python app example):

# Step 1: Use base image
FROM python:3.9-slim

# Step 2: Set working directory
WORKDIR /app

# Step 3: Copy app files
COPY . .

# Step 4: Install dependencies (if requirements.txt exists)
RUN pip install -r requirements.txt || true

# Step 5: Run the app
CMD ["python", "app.py"]


You can adjust this for Node.js, Java, etc.

🔸 2. deployment.yaml

Example:

apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: myapp
        image: yourdockerhubuser/myapp:v1
        ports:
        - containerPort: 80

🔸 3. service.yaml

Example:

apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  selector:
    app: myapp
  ports:
  - name: http
    port: 80
    targetPort: 80
  type: NodePort   # Use ClusterIP for internal access or LoadBalancer in cloud

🔸 4. app.py

Example simple Python web app:

from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello():
    return "Hello, Kubernetes!"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=80)

🔸 5. deploy.sh

This script automates everything:

#!/bin/bash
# deploy.sh — Automate Docker build, push, and Kubernetes deployment

set -e

IMAGE=yourdockerhubuser/myapp:v1

echo "🚀 Step 1: Building Docker image..."
docker build -t $IMAGE .

echo "📦 Step 2: Pushing image to Docker Hub..."
docker push $IMAGE

echo "⚙️ Step 3: Deploying to Kubernetes..."
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml

echo "🔍 Step 4: Checking deployment status..."
kubectl rollout status deployment/myapp

echo "✅ Deployment complete!"
kubectl get pods
kubectl get svc myapp-service

🪜 Step 2 — Give permission to the script

In your terminal (inside myapp/ folder):

chmod +x deploy.sh

🪜 Step 3 — Run the script

Execute:

./deploy.sh


or (if on Windows PowerShell)

bash deploy.sh


This will automatically:

Build your Docker image

Push it to Docker Hub

Create (or update) your Kubernetes Deployment and Service

Show status and running pods

🪜 Step 4 — Verify

After deployment:

kubectl get pods
kubectl get svc


If using Minikube:

minikube service myapp-service --url


This gives you a URL → open in browser to see:
👉 Hello, Kubernetes!
