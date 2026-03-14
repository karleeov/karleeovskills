---
name: azure-devops
description: "Use this skill for Azure DevOps operations including work item management, pipeline configuration, repository operations, and team collaboration. Triggers when users mention 'Azure DevOps', 'ADO', 'Azure Boards', 'Azure Pipelines', 'Azure Repos', work items, sprint planning, CI/CD pipelines in Azure context, or need to interact with Azure DevOps REST API. Also triggers for tasks involving boards, backlogs, queries, service connections, or Azure DevOps CLI commands."
license: Proprietary. LICENSE.txt has complete terms
---

# Azure DevOps Skill

Work with Azure DevOps services for project management, CI/CD, and team collaboration.

## Overview

Azure DevOps provides developer services for teams to plan work, collaborate on code, and build/deploy applications. This skill covers:

- **Azure Boards**: Work items, backlogs, sprints, queries
- **Azure Repos**: Git repositories, pull requests, branch policies
- **Azure Pipelines**: Build and release pipelines, YAML configurations
- **Azure Test Plans**: Test cases, test suites, test runs
- **Azure Artifacts**: Package management (NuGet, npm, Maven)

## Prerequisites

```bash
# Install Azure CLI
# Windows
winget install Microsoft.AzureCLI

# macOS
brew install azure-cli

# Linux
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash

# Install Azure DevOps extension
az extension add --name azure-devops

# Authenticate
az login
```

## Azure DevOps CLI Commands

### Authentication & Configuration

```bash
# Set default organization and project
az devops configure --defaults organization=https://dev.azure.com/yourorg project=yourproject

# List organizations
az devops organization list

# Show current configuration
az devops configure --list
```

### Work Items (Azure Boards)

```bash
# Create work item
az boards work-item create --title "Implement user authentication" --type "User Story" --description "Add OAuth2 login"

# Show work item details
az boards work-item show --id 123

# Update work item
az boards work-item update --id 123 --state "Active" --assigned-to "developer@email.com"

# List work items
az boards work-item list --wiql "SELECT [System.Id], [System.Title], [System.State] FROM WorkItems WHERE [System.WorkItemType] = 'User Story'"

# Add comment to work item
az boards work-item comment add --id 123 --text "Updated implementation approach"
```

### Sprints and Iterations

```bash
# List sprints
az boards sprint list

# Show sprint details
az boards sprint show --id @current

# List work items in sprint
az boards sprint work-item list --id @current
```

### Repositories (Azure Repos)

```bash
# List repositories
az repos list

# Create repository
az repos create --name "new-service"

# Show repository
az repos show --repository "my-repo"

# List pull requests
az repos pr list --repository "my-repo" --status active

# Create pull request
az repos pr create --repository "my-repo" --source-branch feature/auth --target-branch main --title "Add authentication"

# Show pull request
az repos pr show --id 456

# Update pull request (approve, etc.)
az repos pr update --id 456 --vote approve
```

### Pipelines (Azure Pipelines)

```bash
# List pipelines
az pipelines list

# Show pipeline details
az pipelines show --id 789

# Run pipeline
az pipelines run --id 789 --branch main

# Run pipeline with parameters
az pipelines run --id 789 --parameters environment=staging

# List pipeline runs
az pipelines runs list --pipeline-ids 789

# Show pipeline run details
az pipelines runs show --id 12345
```

## REST API Usage

For operations not covered by CLI, use the Azure DevOps REST API:

### Authentication

```bash
# Create Personal Access Token (PAT)
# Go to: https://dev.azure.com/yourorg/_usersSettings/tokens

# Use with curl
export AZDO_PAT="your-personal-access-token"
export AZDO_ORG="https://dev.azure.com/yourorg"
export AZDO_PROJECT="yourproject"

# Base64 encode PAT for auth
AUTH=$(echo -n ":$AZDO_PAT" | base64)
```

### Work Items API

```bash
# Get work item
curl -H "Authorization: Basic $AUTH" \
  "$AZDO_ORG/_apis/wit/workitems/123?api-version=7.0"

# Create work item
curl -X POST \
  -H "Authorization: Basic $AUTH" \
  -H "Content-Type: application/json-patch+json" \
  -d '[{"op": "add", "path": "/fields/System.Title", "value": "New Feature"}]' \
  "$AZDO_ORG/$AZDO_PROJECT/_apis/wit/workitems/\$User%20Story?api-version=7.0"

# Query work items (WIQL)
curl -X POST \
  -H "Authorization: Basic $AUTH" \
  -H "Content-Type: application/json" \
  -d '{"query": "SELECT [System.Id], [System.Title] FROM WorkItems WHERE [System.State] = '\''Active'\''"}' \
  "$AZDO_ORG/$AZDO_PROJECT/_apis/wit/wiql?api-version=7.0"
```

### Pipelines API

```bash
# List pipelines
curl -H "Authorization: Basic $AUTH" \
  "$AZDO_ORG/$AZDO_PROJECT/_apis/pipelines?api-version=7.0"

# Run pipeline
curl -X POST \
  -H "Authorization: Basic $AUTH" \
  -H "Content-Type: application/json" \
  -d '{"resources": {"repositories": {"self": {"refName": "refs/heads/main"}}}}' \
  "$AZDO_ORG/$AZDO_PROJECT/_apis/pipelines/789/runs?api-version=7.0"
```

### Pull Requests API

```bash
# List pull requests
curl -H "Authorization: Basic $AUTH" \
  "$AZDO_ORG/$AZDO_PROJECT/_apis/git/repositories/my-repo/pullrequests?api-version=7.0"

# Create pull request
curl -X POST \
  -H "Authorization: Basic $AUTH" \
  -H "Content-Type: application/json" \
  -d '{"sourceRefName": "refs/heads/feature/auth", "targetRefName": "refs/heads/main", "title": "Add authentication"}' \
  "$AZDO_ORG/$AZDO_PROJECT/_apis/git/repositories/my-repo/pullrequests?api-version=7.0"
```

## Pipeline YAML Examples

### Basic Build Pipeline

```yaml
# azure-pipelines.yml
trigger:
  - main

pool:
  vmImage: 'ubuntu-latest'

steps:
  - task: UsePythonVersion@0
    inputs:
      versionSpec: '3.11'

  - script: |
      python -m pip install --upgrade pip
      pip install -r requirements.txt
    displayName: 'Install dependencies'

  - script: |
      pip install pytest
      pytest tests/
    displayName: 'Run tests'

  - task: PublishTestResults@2
    inputs:
      testResultsFiles: '**/test-*.xml'
```

### Multi-stage Deployment Pipeline

```yaml
trigger:
  - main

stages:
  - stage: Build
    jobs:
      - job: BuildJob
        pool:
          vmImage: 'ubuntu-latest'
        steps:
          - script: echo "Building..."
          - task: PublishBuildArtifacts@1
            inputs:
              PathtoPublish: 'dist'
              ArtifactName: 'app'

  - stage: DeployStaging
    dependsOn: Build
    jobs:
      - deployment: DeployStaging
        environment: 'staging'
        pool:
          vmImage: 'ubuntu-latest'
        strategy:
          runOnce:
            deploy:
              steps:
                - script: echo "Deploying to staging..."

  - stage: DeployProduction
    dependsOn: DeployStaging
    condition: succeeded()
    jobs:
      - deployment: DeployProduction
        environment: 'production'
        pool:
          vmImage: 'ubuntu-latest'
        strategy:
          runOnce:
            deploy:
              steps:
                - script: echo "Deploying to production..."
```

## Common Workflows

### Create and Track a Feature

```bash
# 1. Create user story
STORY_ID=$(az boards work-item create --title "Add password reset" --type "User Story" --query id -o tsv)

# 2. Create tasks
az boards work-item create --title "Implement reset API" --type "Task" --parent $STORY_ID
az boards work-item create --title "Add email templates" --type "Task" --parent $STORY_ID
az boards work-item create --title "Write unit tests" --type "Task" --parent $STORY_ID

# 3. Create branch
az repos create --name "feature/password-reset"

# 4. Create PR when ready
az repos pr create --repository "feature/password-reset" --source-branch main --title "Password Reset Feature" --work-items $STORY_ID
```

### Sprint Planning

```bash
# 1. List available sprints
az boards sprint list

# 2. List backlog items
az boards work-item list --wiql "SELECT [System.Id], [System.Title] FROM WorkItems WHERE [System.WorkItemType] = 'User Story' AND [System.State] = 'New'"

# 3. Assign to sprint
az boards work-item update --id 123 --iteration "Sprint 42"
```

## Service Connections

```bash
# List service connections
az devops service-endpoint list

# Create service connection (example: GitHub)
az devops service-endpoint github create --name "GitHub" --url "https://github.com" --pat $GITHUB_PAT
```

## Best Practices

### Work Item Management
- Use area paths for team categorization
- Use iteration paths for sprint planning
- Link related work items (parent/child, related, duplicate)
- Tag work items for filtering

### Pipeline Design
- Use YAML pipelines over classic UI pipelines
- Implement proper secret management (use Azure Key Vault)
- Use templates for reusable pipeline components
- Implement proper branching strategies

### Security
- Use managed identities where possible
- Rotate PATs regularly
- Use least-privilege permissions
- Enable branch policies on main branches
