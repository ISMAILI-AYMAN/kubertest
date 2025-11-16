# Kubernetes FastAPI Test Application

A simple FastAPI application containerized with Docker and deployed to Kubernetes. This project also includes Terraform configurations for Docker infrastructure management.

## Project Structure

```
kubertest/
├── app.py                 # FastAPI application
├── dockerfile             # Docker image definition
├── req.txt                # Python dependencies
├── readme.md              # This file
├── k8/
│   ├── deployment.yaml    # Kubernetes deployment configuration
│   └── service.yaml       # Kubernetes service configuration
└── terraform-docker-lab/
    ├── main.tf            # Terraform Docker configuration
    ├── terraform.tfstate  # Terraform state file
    └── terraform.tfstate.backup  # Terraform state backup
```

## Prerequisites

- Python 3.9+
- Docker
- Kubernetes cluster (or minikube/kind for local testing)
- kubectl configured
- Terraform (for Docker infrastructure management)

## Local Development

### Install Dependencies

```bash
pip install -r req.txt
```

### Run Locally

```bash
uvicorn app:app --reload
```

The application will be available at `http://localhost:8000`

## Docker Build and Run

### Build Docker Image

```bash
docker build -t fastapi-kuber .
```

### Run Docker Container

```bash
docker run -p 8000:8000 fastapi-kuber
```

## Kubernetes Deployment

### Build Docker Image (for local Kubernetes)

If using minikube or kind, make sure the image is available in your cluster:

```bash
# For minikube
minikube image build -t fastapi-kuber .

# For kind
kind load docker-image fastapi-kuber
```

### Deploy to Kubernetes

```bash
kubectl apply -f k8/deployment.yaml
kubectl apply -f k8/service.yaml
```

### Check Deployment Status

```bash
kubectl get deployments
kubectl get pods
kubectl get services
```

### Access the Application

The service is exposed as NodePort on port 30080. Access it via:

- **Minikube**: `minikube service fastapi-service` or `http://$(minikube ip):30080`
- **Other clusters**: `http://<node-ip>:30080`

### Test the Endpoint

```bash
curl http://localhost:30080/
```

Expected response:
```json
{"message": "Kubernetes test running"}
```

## Cleanup

To remove the deployment:

```bash
kubectl delete -f k8/service.yaml
kubectl delete -f k8/deployment.yaml
```

## Terraform Docker Lab

The `terraform-docker-lab` directory contains Terraform configurations for managing Docker containers.

### Prerequisites for Terraform

- Terraform installed
- Docker running locally

### Deploy with Terraform

```bash
cd terraform-docker-lab
terraform init
terraform plan
terraform apply
```

### Destroy Terraform Resources

```bash
cd terraform-docker-lab
terraform destroy
```

### Current Terraform Configuration

The current configuration deploys an nginx container:
- **Image**: nginx:latest
- **Container Name**: nginx-test
- **Ports**: Internal 80 → External 8080

Access nginx at `http://localhost:8080`

## API Endpoints

- `GET /` - Returns a test message confirming the application is running
