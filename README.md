# AKS Store CI/CD with Self-Hosted Runner

This repository demonstrates a CI/CD pipeline for deploying the AKS Store Demo application using a self-hosted GitHub Actions runner on Azure.

## Architecture Overview

```
GitHub Repository → Self-Hosted Runner (Azure VM) → Azure Kubernetes Service (AKS) → AKS Store Demo Application
```

## Components

### 1. Self-Hosted Runner

- **Location**: Azure VM
- **Runner Version**: 2.326.0
- **Authentication**: Managed Identity with Client ID `893a4871-96d2-4a32-b6c6-954045688408`
- **Setup**: Configured to run on the `self-hosted` runner label

### 2. Azure Resources

- **Resource Group**: `rg-aks-final`
- **AKS Cluster**: `aks-final-cluster`
- **Location**: `eastus`
- **Node Count**: 1 (development setup)

### 3. Application

- **Namespace**: `aks-store-demo`
- **Components**:
  - Store Front (Web UI)
  - Product Service (Backend API)
  - Order Service (Backend API)

## Setup Instructions

### Self-Hosted Runner Setup

1. **Create Runner Directory**:

   ```bash
   mkdir actions-runner && cd actions-runner
   ```

2. **Download Runner Package**:

   ```bash
   curl -o actions-runner-linux-x64-2.326.0.tar.gz -L \
     https://github.com/actions/runner/releases/download/v2.326.0/actions-runner-linux-x64-2.326.0.tar.gz
   ```

3. **Validate Hash** (Optional):

   ```bash
   echo "9c74af9b4352bbc99aecc7353b47bcdfcd1b2a0f6d15af54a99f54a0c14a1de8  actions-runner-linux-x64-2.326.0.tar.gz" | shasum -a 256 -c
   ```

4. **Extract Installer**:

   ```bash
   tar xzf ./actions-runner-linux-x64-2.326.0.tar.gz
   ```

5. **Configure Runner**:

   ```bash
   ./config.sh --url https://github.com/Munasco/aks-store-ci-cd --token AJTNQL2PAPUTMANDMP2UE5TIPM2ZU
   ```

6. **Start Runner**:
   ```bash
   ./run.sh
   ```

### Azure VM Requirements

The Azure VM hosting the self-hosted runner should have:

- **OS**: Ubuntu Linux (recommended)
- **Azure CLI**: Installed and configured
- **kubectl**: Installed for Kubernetes operations
- **Managed Identity**: Assigned with appropriate permissions
- **Network Access**: Outbound internet access for GitHub Actions

### Required Azure Permissions

The managed identity needs the following roles:

- **Contributor** on the resource group
- **Azure Kubernetes Service Cluster Admin Role** on the AKS cluster
- **Network Contributor** (if creating new networks)

## Workflow Details

### Trigger

The workflow is triggered by:

- **Push** to `main` branch
- **Manual** workflow dispatch

### Steps

1. **Checkout Code**: Retrieves the repository code
2. **Azure CLI Login**: Authenticates using managed identity
3. **Create AKS Cluster**: Creates the cluster if it doesn't exist
4. **Get AKS Credentials**: Configures kubectl for the cluster
5. **Deploy AKS Store Demo**:
   - Creates namespace
   - Applies the demo application
   - Waits for deployments to be ready
6. **Get Service URLs**: Displays deployment status

## Monitoring and Troubleshooting

### Check Runner Status

```bash
# On the Azure VM
./run.sh --help
```

### Check AKS Cluster Status

```bash
az aks show --resource-group rg-aks-final --name aks-final-cluster
```

### Check Application Status

```bash
kubectl get all -n aks-store-demo
```

### View Application Logs

```bash
kubectl logs -n aks-store-demo deployment/store-front
kubectl logs -n aks-store-demo deployment/product-service
kubectl logs -n aks-store-demo deployment/order-service
```

## Security Considerations

1. **Runner Token**: The runner token should be rotated regularly
2. **Managed Identity**: Use least privilege principle for managed identity permissions
3. **Network Security**: Consider using private endpoints for Azure services
4. **Secrets**: Store sensitive information in GitHub Secrets, not in code

## Cost Optimization

1. **VM Size**: Use appropriate VM size for your workload
2. **AKS Node Count**: Scale down to 0 nodes when not in use
3. **Auto-shutdown**: Configure VM auto-shutdown for development environments
4. **Spot Instances**: Consider using spot instances for cost savings

## Next Steps

1. **Add Monitoring**: Integrate with Azure Monitor and Application Insights
2. **Implement Blue-Green Deployments**: Add deployment strategies
3. **Add Security Scanning**: Integrate container scanning and vulnerability assessment
4. **Implement Rollback**: Add rollback capabilities for failed deployments
5. **Add Notifications**: Configure Slack/Teams notifications for deployment status

## Troubleshooting

### Common Issues

1. **Runner Offline**: Check if the runner service is running on the VM
2. **Authentication Failures**: Verify managed identity permissions
3. **Deployment Failures**: Check pod logs and events
4. **Network Issues**: Verify outbound connectivity from the VM

### Useful Commands

```bash
# Check runner status
sudo systemctl status actions.runner.*

# Restart runner service
sudo systemctl restart actions.runner.*

# Check Azure CLI authentication
az account show

# Check kubectl configuration
kubectl config current-context
```

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test the workflow
5. Submit a pull request

## License

This project is licensed under the MIT License.
