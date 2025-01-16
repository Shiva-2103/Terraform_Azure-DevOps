# Terraform Azure DevOps Project  

This repository contains an end-to-end implementation of **Terraform** with **Azure DevOps**, demonstrating how to automate infrastructure provisioning using Infrastructure as Code (IaC).  

## 🚀 Project Overview  

The project highlights the integration of Terraform and Azure DevOps to:  
- Provision and manage Azure resources.  
- Use Azure DevOps pipelines for automated deployments.  
- Demonstrate best practices in Terraform code structure and state management.  

## 🛠️ Features  

- Modularized Terraform code for reusability.  
- Azure DevOps YAML-based CI/CD pipelines.  
- Automated state management using Azure Storage.  
- Resource provisioning examples (e.g., Virtual Machines, Storage Accounts, etc.).  

## 📋 Prerequisites  

Before you begin, ensure you have the following installed:  
- Terraform CLI
- Azure CLI 
- An active Azure subscription.  
- An Azure DevOps account.  

## 🚀 Getting Started
1. Clone the Repository
   ```
   git clone https://github.com/Shiva-2103/Terraform_Azure-DevOps.git  
   cd Terraform_Azure-DevOps
   ```
2. Configure Backend
   ```
     terraform {  
      backend "azurerm" {  
        resource_group_name  = "<RESOURCE_GROUP>"  
        storage_account_name = "<STORAGE_ACCOUNT>"  
        container_name       = "tfstate"  
        key                  = "terraform.tfstate"  
          }  
        }  
   ```
3. Initialize Terraform
   ```
   terraform init  
   ```
4. Validate and Plan
   ```
   terraform validate  
   terraform plan  
   ```
5. Apply the Configuration
   ```
   terraform apply  
   ```
6. Set Up Azure DevOps Pipeline
   - Import the YAML pipeline files (pipelines/) into Azure DevOps.
   - Configure service connections and variables as needed.

## Azure DevOps CI/CD Pipelie
![Azure DevOps CI/CD Pipeline](https://raw.githubusercontent.com/Shiva-2103/Terraform_Azure-DevOps/main/CI_Pipeline)

## YAML Code for Build pipeline
```
trigger: 
- main

stages:
- stage: Build
  jobs:
  - job: Build
    pool:
      vmImage: 'ubuntu-latest'
    steps:
    - task: TerraformTaskV4@4
      displayName: Tf init
      inputs:
        provider: 'azurerm'
        command: 'init'
        backendServiceArm: '${SERVICECONNECTION}'
        backendAzureRmResourceGroupName: 'demo-resources'
        backendAzureRmStorageAccountName: 'techtutorialswithpiyush'
        backendAzureRmContainerName: 'prod-tfstate'
        backendAzureRmKey: 'prod.terraform.tfstate'
    - task: TerraformTaskV4@4
      displayName: Tf validate
      inputs:
        provider: 'azurerm'
        command: 'validate'
    - task: TerraformTaskV4@4
      displayName: Tf fmt
      inputs:
        provider: 'azurerm'
        command: 'custom'
        customCommand: 'fmt'
        outputTo: 'console'
        environmentServiceNameAzureRM: '${SERVICECONNECTION}'
      
    - task: TerraformTaskV4@4
      displayName: Tf plan
      inputs:
        provider: 'azurerm'
        command: 'plan'
        commandOptions: '-out $(Build.SourcesDirectory)/tfplanfile'
        environmentServiceNameAzureRM: '${SERVICECONNECTION}'
      
    - task: ArchiveFiles@2
      displayName: Archive files
      inputs:
        rootFolderOrFile: '$(Build.SourcesDirectory)/'
        includeRootFolder: false
        archiveType: 'zip'
        archiveFile: '$(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip'
        replaceExistingArchive: true
    - task: PublishBuildArtifacts@1
      inputs:
        PathtoPublish: '$(Build.ArtifactStagingDirectory)'
        ArtifactName: '$(Build.BuildId)-build'
        publishLocation: 'Container'

```
7. Important Pipeline Variables
   - System.Default.Directory
   - BuildDefinitionName
   - Build.Source.Directory
   - Build.ArtifactStagingDirectory
   - Build.BuildId

## 🌟 Key Highlights
   State Management:
   -  State files are stored securely in Azure Blob Storage.
   Environment Isolation:
   - Separate directories for dev, staging, and production.
   CI/CD Pipelines:
   -  Automated deployment pipelines built with Azure DevOps.

## Conclusion
  This project demonstrates the implementation of a CI/CD Pipeline using Azure DevOps. It provides a step-by-step guide to set up and automate code integration, testing, and deployment using Terraform and Azure DevOps services. By leveraging these tools, developers can enhance productivity, maintain high code quality, and ensure efficient deployment processes.




