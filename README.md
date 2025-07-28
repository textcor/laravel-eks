# laravel-eks
Laravel on AWS EKS with CI/CD Pipeline

![Laravel](https://img.shields.io/badge/Laravel-FF2D20?style=for-the-badge&logo=laravel&logoColor=white)
![Kubernetes](https://img.shields.io/badge/kubernetes-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-%23FF9900.svg?style=for-the-badge&logo=amazon-aws&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/github%20actions-%232671E5.svg?style=for-the-badge&logo=githubactions&logoColor=white)

This project demonstrates how to deploy a Laravel application to AWS Elastic Kubernetes Service (EKS) with a complete CI/CD pipeline using GitHub Actions. The infrastructure includes:

- 🐳 Dockerized Laravel application with PHP-FPM
- 🚀 Nginx as web server
- 🔄 Redis for caching and sessions
- ☸️ Kubernetes manifests for all components
- 🔄 Automated CI/CD pipeline with GitHub Actions
- 🔒 Ingress controller with TLS support
- 📊 Health checks and horizontal pod autoscaling

## Prerequisites

Before you begin, ensure you have:

- [AWS Account](https://aws.amazon.com/) with appropriate permissions
- [AWS CLI](https://aws.amazon.com/cli/) installed and configured
- [eksctl](https://eksctl.io/) installed
- [kubectl](https://kubernetes.io/docs/tasks/tools/) installed
- [Docker](https://www.docker.com/) installed
- GitHub account

## Architecture

![Architecture Diagram](https://i.imgur.com/JKQ4X7m.png)

```
Internet → AWS ALB Ingress → Nginx Pods → Laravel Pods → Redis Service
```

## Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/yourusername/laravel-eks.git
cd laravel-eks
```

### 2. Set Up EKS Cluster (One-time)

```bash
eksctl create cluster \
  --name laravel-cluster \
  --region us-east-1 \
  --nodegroup-name workers \
  --node-type t3.medium \
  --nodes 2 \
  --nodes-min 1 \
  --nodes-max 4 \
  --managed
```

### 3. Install Ingress Controller

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.8.1/deploy/static/provider/aws/deploy.yaml
```

### 4. Configure GitHub Secrets

Set these secrets in your GitHub repository (Settings > Secrets > Actions):

| Secret Name               | Description                          |
|---------------------------|--------------------------------------|
| `AWS_ACCESS_KEY_ID`       | AWS IAM access key                   |
| `AWS_SECRET_ACCESS_KEY`   | AWS IAM secret key                   |
| `DOCKER_HUB_USERNAME`     | Docker Hub username                  |
| `DOCKER_HUB_TOKEN`        | Docker Hub access token              |
| `DOCKER_REPO`            | Your Docker repository (e.g., "yourdockerhub") |

### 5. Deploy Application

Push to the `main` branch to trigger the CI/CD pipeline:

```bash
git add .
git commit -m "Initial commit"
git push origin main
```

## CI/CD Pipeline

The GitHub Actions workflow includes:

1. **Build Stage**:
   - Builds Docker image
   - Runs PHPUnit tests
   - Pushes image to Docker Hub

2. **Deploy Stage**:
   - Updates Kubernetes manifests
   - Rolls out new deployment with zero-downtime
   - Verifies health checks

![Pipeline Stages](https://i.imgur.com/8X9qZyG.png)

## Project Structure

```
.
├── .github/workflows/          # GitHub Actions workflows
│   ├── build-test.yaml         # Build and test pipeline
│   └── deploy-eks.yaml         # Deployment pipeline
├── kubernetes/                 # Kubernetes manifests
│   ├── 01-namespace.yaml       # Namespace configuration
│   ├── 02-configmap.yaml       # Environment variables
│   ├── 03-secrets.yaml         # Sensitive configuration
│   ├── 04-laravel-deployment.yaml # Laravel app deployment
│   ├── 05-nginx-deployment.yaml  # Nginx deployment
│   ├── 06-redis-deployment.yaml  # Redis deployment
│   ├── 07-services.yaml        # Kubernetes services
│   └── 08-ingress.yaml         # Ingress configuration
├── docker/                     # Docker configurations
│   ├── nginx/                  # Nginx config
│   │   └── laravel.conf        # Nginx server block
│   └── php/                    # PHP configurations
│       ├── Dockerfile          # Laravel Dockerfile
│       └── supervisord.conf    # Process manager config
├── .dockerignore
├── .gitignore
└── README.md                   # This file
```

## Monitoring and Maintenance

### Check Deployment Status

```bash
kubectl get pods -n laravel-app -w
```

### View Application Logs

```bash
kubectl logs -f deployment/laravel-app -n laravel-app
```

### Get Application URL

```bash
kubectl get ingress -n laravel-app
```

### Scale Application

```bash
kubectl scale deployment laravel-app --replicas=4 -n laravel-app
```

## Clean Up

To delete the EKS cluster and all resources:

```bash
eksctl delete cluster --name laravel-cluster --region us-east-1
```

## Troubleshooting

### Common Issues

1. **Image pull errors**:
   - Verify Docker Hub credentials in GitHub secrets
   - Check image name in Kubernetes manifests

2. **Pending pods**:
   ```bash
   kubectl describe pod <pod-name> -n laravel-app
   ```

3. **Ingress not working**:
   ```bash
   kubectl get ingress -n laravel-app
   kubectl describe ingress laravel-ingress -n laravel-app
   ```

4. **Database connection issues**:
   - Verify RDS security groups
   - Check database credentials in secrets

## Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## License

Distributed under the MIT License. See `LICENSE` for more information.

---

**Happy Kubernetes Deployment!** 🚀

