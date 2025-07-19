# AKS Store Demo CI/CD Pipeline

This repository contains a GitHub Actions workflow for automated deployment of the AKS store demo application using a self-hosted runner on Azure.

## Architecture

- **Self-hosted GitHub Runner**: Running on Azure VM for secure access to Azure resources
- **Managed Identity Authentication**: Using user-assigned managed identity for secure Azure authentication
- **Automated Deployment**: Triggers on push to main branch or manual dispatch

## Prerequisites

### 1. Azure VM with Self-hosted Runner

- Azure VM with GitHub self-hosted runner installed
- Azure CLI and kubectl installed on the VM
- User-assigned managed identity attached to the VM

### 2. Managed Identity Setup

- Managed Identity ID: `893a4871-96d2-4a32-b6c6-954045688408`
- Required roles assigned:
  - Contributor (subscription level)
  - Network Contributor (subscription level)

### 3. Azure Resources

- Resource Group: `rg-aks-final`
- AKS Cluster: `aks-final-cluster`
- Location: `eastus`

## Workflow Steps

1. **Checkout Code**: Retrieves the latest code from the repository
2. **Azure CLI Login**: Authenticates using managed identity
3. **AKS Cluster Management**: Creates AKS cluster if it doesn't exist
4. **Get Credentials**: Retrieves kubectl credentials for the cluster
5. **Deploy Application**: Applies the AKS store demo to the cluster
6. **Verify Deployment**: Checks deployment status and service URLs

## Security Features

- ✅ **Managed Identity**: No secrets stored in workflow
- ✅ **Self-hosted Runner**: Secure execution environment
- ✅ **Namespace Isolation**: Deploys to dedicated namespace
- ✅ **Error Handling**: Robust error handling and retry logic

## Deployment Results

### Services Deployed

- **store-front**: LoadBalancer service (external access)
- **product-service**: ClusterIP service
- **order-service**: ClusterIP service
- **rabbitmq**: StatefulSet with ClusterIP service

### Pod Status

- All pods running successfully
- Services properly configured
- LoadBalancer pending external IP assignment

## Files

- `.github/workflows/deploy-aks-store-runner.yaml` - Main CI/CD workflow
- `README.md` - This documentation file

## Usage

1. Push to main branch to trigger automatic deployment
2. Or use manual dispatch from GitHub Actions tab
3. Monitor deployment progress in GitHub Actions
4. Check service URLs in the workflow output
