---
name: azure-deploy
description: "Use this skill for deploying applications and infrastructure to Microsoft Azure. Triggers when users mention 'deploy to Azure', 'Azure deployment', 'azd', 'Azure Developer CLI', provisioning Azure resources, or need to create/manage Azure infrastructure. Also triggers for tasks involving Azure CLI commands, ARM templates, Bicep, Azure resource groups, App Service, Azure Functions, AKS, or container deployments to Azure."
license: Proprietary. LICENSE.txt has complete terms
---

# Azure Deploy Skill

Deploy applications and manage Azure infrastructure using Azure Developer CLI (`azd`) and Azure CLI (`az`).

## Overview

This skill covers:

- **Azure Developer CLI (`azd`)**: Streamlined application deployment workflow
- **Azure CLI (`az`)**: Comprehensive Azure resource management
- **Infrastructure as Code**: Bicep and ARM templates
- **Common Azure Services**: App Service, Functions, AKS, Storage, SQL, Key Vault

## Prerequisites

```bash
# Install Azure Developer CLI
# Windows
winget install Microsoft.AzureDeveloperCLI

# macOS
brew tap azure/azd && brew install azd

# Linux
curl -fsSL https://aka.ms/install-azd.sh | bash

# Install Azure CLI (if not already installed)
# Windows
winget install Microsoft.AzureCLI

# macOS
brew install azure-cli

# Authenticate
az login
azd auth login
```

## Azure Developer CLI (`azd`)

The Azure Developer CLI provides a streamlined workflow for deploying applications.

### Core Commands

```bash
# Initialize a new project (detects project type)
azd init

# Initialize from template
azd init --template azure-python-webapp

# Provision infrastructure only
azd provision

# Deploy application code only
azd deploy

# Provision and deploy in one command
azd up

# Destroy all resources
azd down

# Show current environment
azd env get-values

# Set environment values
azd env set KEY VALUE
```

### Environment Management

```bash
# List environments
azd env list

# Select environment
azd env select production

# Create new environment
azd env new staging

# Delete environment
azd env delete old-env
```

### Pipeline Integration

```bash
# Configure CI/CD pipeline
azd pipeline config

# Configure with specific provider
azd pipeline config --provider github
azd pipeline config --provider azdo
```

### Project Structure

```
myapp/
├── src/                    # Application code
├── infra/                  # Infrastructure as Code
│   ├── main.bicep         # Main infrastructure template
│   └── main.parameters.json
├── azure.yaml             # azd configuration
└── .azure/
    └── environments/      # Environment configurations
```

### azure.yaml Configuration

```yaml
name: myapp
services:
  web:
    project: ./src/web
    language: python
    host: appservice
  api:
    project: ./src/api
    language: python
    host: function
resources:
  web:
    type: azure.bicep
    path: infra/main.bicep
```

## Azure CLI (`az`)

### Resource Groups

```bash
# Create resource group
az group create --name myResourceGroup --location eastus

# List resource groups
az group list --output table

# Delete resource group (and all resources)
az group delete --name myResourceGroup --yes --no-wait
```

### App Service (Web Apps)

```bash
# Create app service plan
az appservice plan create --name myPlan --resource-group myRG --sku P1V3

# Create web app
az webapp create --name myapp --resource-group myRG --plan myPlan --runtime "PYTHON|3.11"

# Deploy from local folder
az webapp up --name myapp --resource-group myRG --location eastus

# Deploy from GitHub
az webapp deployment source config --name myapp --resource-group myRG --repo-url https://github.com/user/repo --branch main --manual-integration

# Configure environment variables
az webapp config appsettings set --name myapp --resource-group myRG --settings KEY=VALUE

# View logs
az webapp log tail --name myapp --resource-group myRG

# List web apps
az webapp list --resource-group myRG --output table
```

### Azure Functions

```bash
# Create storage account (required for functions)
az storage account create --name mystorageacct --resource-group myRG --location eastus --sku Standard_LRS

# Create function app
az functionapp create --name myfunc --resource-group myRG --storage-account mystorageacct --consumption-plan-location eastus --runtime python --functions-version 4

# Deploy function app
az functionapp deployment source config-zip --name myfunc --resource-group myRG --src deployment.zip

# Configure function app settings
az functionapp config appsettings set --name myfunc --resource-group myRG --settings KEY=VALUE
```

### Azure Kubernetes Service (AKS)

```bash
# Create AKS cluster
az aks create --name myAKSCluster --resource-group myRG --node-count 3 --generate-ssh-keys

# Get credentials
az aks get-credentials --name myAKSCluster --resource-group myRG

# Scale cluster
az aks scale --name myAKSCluster --resource-group myRG --node-count 5

# Upgrade cluster
az aks upgrade --name myAKSCluster --resource-group myRG --kubernetes-version 1.28

# List clusters
az aks list --output table
```

### Container Registry (ACR)

```bash
# Create container registry
az acr create --name myacr --resource-group myRG --sku Basic

# Login to registry
az acr login --name myacr

# Build and push image
az acr build --image myapp:v1 --registry myacr .

# Import image from Docker Hub
az acr import --name myacr --source docker.io/library/nginx:latest --image nginx:latest

# List repositories
az acr repository list --name myacr --output table
```

### Azure SQL Database

```bash
# Create SQL server
az sql server create --name myserver --resource-group myRG --location eastus --admin-user adminuser --admin-password Password123!

# Create database
az sql db create --name mydb --resource-group myRG --server myserver --service-objective S1

# Create firewall rule
az sql server firewall-rule create --name AllowAll --resource-group myRG --server myserver --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255

# Get connection string
az sql db show-connection-string --name mydb --server myserver --client ado.net
```

### Key Vault

```bash
# Create key vault
az keyvault create --name myKeyVault --resource-group myRG --location eastus

# Add secret
az keyvault secret set --name MySecret --value "secret-value" --vault-name myKeyVault

# Get secret
az keyvault secret show --name MySecret --vault-name myKeyVault --query value -o tsv

# Grant access to web app
az keyvault set-policy --name myKeyVault --object-id <principal-id> --secret-permissions get list
```

### Storage Accounts

```bash
# Create storage account
az storage account create --name mystorageacct --resource-group myRG --location eastus --sku Standard_LRS --kind StorageV2

# Create container
az storage container create --name mycontainer --account-name mystorageacct

# Upload file
az storage blob upload --account-name mystorageacct --container-name mycontainer --name myfile.txt --file ./local-file.txt

# List blobs
az storage blob list --account-name mystorageacct --container-name mycontainer --output table

# Get connection string
az storage account show-connection-string --name mystorageacct --resource-group myRG --query connectionString -o tsv
```

### Virtual Machines

```bash
# Create VM
az vm create --name myVM --resource-group myRG --image Ubuntu2204 --admin-username azureuser --generate-ssh-keys

# Open port
az vm open-port --name myVM --resource-group myRG --port 80

# Start VM
az vm start --name myVM --resource-group myRG

# Stop VM
az vm deallocate --name myVM --resource-group myRG

# List VMs
az vm list --resource-group myRG --output table
```

## Bicep Templates

### Basic Structure

```bicep
// main.bicep
param location string = resourceGroup().location
param appName string
param environment string = 'dev'

var tags = {
  Environment: environment
  Application: appName
}

resource appServicePlan 'Microsoft.Web/serverfarms@2023-01-01' = {
  name: '${appName}-plan'
  location: location
  sku: {
    name: 'P1v3'
    tier: 'PremiumV3'
  }
  tags: tags
}

resource webApp 'Microsoft.Web/sites@2023-01-01' = {
  name: appName
  location: location
  properties: {
    serverFarmId: appServicePlan.id
    siteConfig: {
      pythonVersion: '3.11'
    }
  }
  tags: tags
}

output webAppUrl string = 'https://${webApp.defaultHostName}'
```

### Deploy Bicep

```bash
# Validate template
az deployment group validate --resource-group myRG --template-file main.bicep --parameters appName=myapp

# Deploy template
az deployment group create --resource-group myRG --template-file main.bicep --parameters appName=myapp environment=prod

# What-if deployment (preview changes)
az deployment group what-if --resource-group myRG --template-file main.bicep --parameters appName=myapp
```

## Common Deployment Workflows

### Deploy Python Web App

```bash
# 1. Create resource group
az group create --name myapp-rg --location eastus

# 2. Create app service plan and web app
az appservice plan create --name myapp-plan --resource-group myapp-rg --sku B1
az webapp create --name myapp-web --resource-group myapp-rg --plan myapp-plan --runtime "PYTHON|3.11"

# 3. Deploy code
az webapp up --name myapp-web --resource-group myapp-rg

# 4. Configure settings
az webapp config appsettings set --name myapp-web --resource-group myapp-rg --settings @settings.json
```

### Deploy with azd (Recommended)

```bash
# 1. Initialize project
azd init

# 2. Set environment
azd env new production

# 3. Deploy everything
azd up

# 4. Monitor
azd monitor
```

### Deploy Container to AKS

```bash
# 1. Create ACR
az acr create --name myacr --resource-group myRG --sku Basic

# 2. Create AKS cluster with ACR integration
az aks create --name myAKS --resource-group myRG --node-count 2 --attach-acr myacr

# 3. Build and push image
az acr build --image myapp:v1 --registry myacr .

# 4. Get credentials
az aks get-credentials --name myAKS --resource-group myRG

# 5. Deploy with kubectl
kubectl apply -f kubernetes.yaml
```

## Cost Management

```bash
# View costs
az consumption usage list --top 10

# Set budget
az consumption budget create --name monthly-budget --amount 500 --time-grain monthly --start-date 2024-01-01 --end-date 2024-12-31
```

## Best Practices

### Security
- Use managed identities for Azure resources
- Store secrets in Key Vault, never in code
- Use private endpoints for sensitive resources
- Enable Azure Security Center

### Cost Optimization
- Use auto-scaling for variable workloads
- Right-size your resources (don't over-provision)
- Use reserved instances for predictable workloads
- Tag resources for cost tracking

### Reliability
- Deploy across availability zones
- Use Azure Front Door for global load balancing
- Enable backup and disaster recovery
- Monitor with Azure Monitor and Application Insights
