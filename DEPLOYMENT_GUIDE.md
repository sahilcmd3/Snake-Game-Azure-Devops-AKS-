# Azure Deployment Commands for Snake Game

## Resource Information
- **Resource Group**: Snake-Game
- **ACR Name**: snakegamesahil
- **ACR Login Server**: snakegamesahil.azurecr.io
- **Image Repository**: sahilcmddjangoapp

---

## Build and Push Docker Image to ACR

### Step 1: Login to Azure and ACR
```bash
# Login to Azure
az login

# Login to ACR
az acr login --name snakegamesahil
```

### Step 2: Build Docker Image
```bash
# Navigate to project directory
cd d:/django-app

# Build the image
docker build -t snakegamesahil.azurecr.io/sahilcmddjangoapp:v1 .
```

### Step 3: Push to ACR
```bash
# Push the image
docker push snakegamesahil.azurecr.io/sahilcmddjangoapp:v1
```

### Step 4: Verify Image in ACR
```bash
# List repositories
az acr repository list --name snakegamesahil --output table

# Show tags
az acr repository show-tags --name snakegamesahil --repository sahilcmddjangoapp --output table
```

---

## Create AKS Cluster

```bash
# Create AKS cluster and attach ACR
az aks create \
  --resource-group Snake-Game \
  --name snakegame-aks \
  --node-count 2 \
  --node-vm-size Standard_B2s \
  --generate-ssh-keys \
  --attach-acr snakegamesahil
```

---

## Deploy to AKS Manually

```bash
# Get AKS credentials
az aks get-credentials --resource-group Snake-Game --name snakegame-aks

# Apply Kubernetes manifests
kubectl apply -f manifests/deployment.yml
kubectl apply -f manifests/service.yml

# Check deployment
kubectl get all
kubectl get service sahilcmddjangoapp

# Get external IP (wait for LoadBalancer)
kubectl get service sahilcmddjangoapp --watch
```

---

## Quick Test Commands

```bash
# Test Docker image locally
docker run -d -p 8000:8000 \
  -e DEBUG="True" \
  -e SECRET_KEY="test-key" \
  -e ALLOWED_HOSTS="*" \
  --name django-test \
  snakegamesahil.azurecr.io/sahilcmddjangoapp:v1

# Check logs
docker logs django-test

# Test in browser
# http://localhost:8000

# Stop and remove
docker stop django-test
docker rm django-test
```

---

## Azure DevOps Pipeline

Your pipeline is already configured with:
- ACR: snakegamesahil.azurecr.io
- Image: sahilcmddjangoapp
- Pull Secret: snakegamesahil-auth

Just push your code to trigger the pipeline!

```bash
git add .
git commit -m "Updated ACR configuration"
git push
```
