# Amazon ECR Integration Guide

This guide explains how to integrate our Docker application with ECR. ECR is a fully managed container registry service from AWS that makes it easy to store, manage, and deploy container images.

## Prerequisites

Before you begin, ensure you have:

1. AWS CLI installed and configured with appropriate credentials
2. Docker installed and running on your machine
3. Necessary AWS IAM permissions to work with ECR

## Step-by-Step Guide

### 1. Create an ECR Repository

First, create a new repository in ECR to store your Docker images:

```bash
aws ecr create-repository \
    --repository-name docker-sample-python \
    --region <your-region>
```

This command creates a new repository named `docker-sample-python` in the eu-central-1 region.

### 2. Authenticate Docker to ECR

To push images to ECR, you need to authenticate your Docker client to the ECR registry:

```bash
aws ecr get-login-password \
    --region <your-region> | \
    docker login \
    --username AWS \
    --password-stdin <account-id>.dkr.ecr.<your-region>.amazonaws.com
```

This command retrieves an authentication token and authenticates your Docker client with your ECR registry.

### 3. Build the Docker Image

Build your Docker image using the Dockerfile in the current directory:

```bash
docker build -t docker-sample-python .
```

### 4. Tag the Image for ECR

Tag your local image to match your ECR repository:

```bash
docker tag docker-sample-python:latest \
    <account-id>.dkr.ecr.<your-region>.amazonaws.com/docker-sample-python:latest
```

### 5. Push the Image to ECR

Finally, push your image to ECR:

```bash
docker push <account-id>.dkr.ecr.<your-region>.amazonaws.com/docker-sample-python:latest
```

## Additional Commands

### List Images in Repository

```bash
aws ecr list-images \
    --repository-name docker-sample-python \
    --region <your-region>
```

### Pull Image from ECR

```bash
docker pull <account-id>.dkr.ecr.<your-region>.amazonaws.com/docker-sample-python:latest
```

### Delete an Image

```bash
aws ecr batch-delete-image \
    --repository-name docker-sample-python \
    --image-ids imageTag=latest \
    --region <your-region>
```

## Best Practices

1. Always tag your images with meaningful versions
2. Regularly clean up unused images to manage storage costs
3. Consider implementing image scanning for security
4. Use IAM roles and policies to manage access to your repositories

## Troubleshooting

If you encounter authentication issues:

- Ensure your AWS credentials are properly configured
- Check if your authentication token has expired (valid for 12 hours)
- Verify you have the necessary IAM permissions

For push/pull errors:

- Verify your Docker daemon is running
- Check your network connectivity
- Ensure your image tag matches the repository name exactly
