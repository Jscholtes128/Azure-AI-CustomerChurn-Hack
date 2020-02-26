# Azure Customer Churn Hackathon

![hackathon design](/images/hackathon.jpg)

Let’s get hands-on with Azure Machine Learning by developing a Customer Churn solution. In this hack we will source a flat file for our model, leverage Azure Machine Learning Service and Azure Databricks for data prep, experimentation tracking, model development, model deployment and MLOps.

__Objectives:__

- Implement a repeatible ML solution using Azure Machine Learning and Azure Databricks.
- Set-up MLOps for CI/CD model staging with Azure DevOps.

## 1. Hackathon Prerequisites 

The following resources are implemented during the hackathon, please ensure you can create in your subscription or resource group:

- [Azure Storage Account - Blob](https://docs.microsoft.com/en-us/azure/storage/common/storage-account-overview)
- [Azure Machine Learning Workspace](https://docs.microsoft.com/en-us/azure/machine-learning/overview-what-is-azure-ml)
- [Azure Databricks](https://docs.microsoft.com/en-us/azure/azure-databricks/what-is-azure-databricks)
- [Azure DevOps](https://docs.microsoft.com/en-us/azure/devops/user-guide/what-is-azure-devops?view=azure-devops)

### 1.1 Azure Portal

Azure subscription. If you don't have one, create a [free account](https://azure.microsoft.com/en-us/free/) before you begin.


### 1.2 Using Cloud Shell

The following bach commands will be ran using the [Azure Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview). 

Launch from Azure portal using the Cloud Shell icon

![cloud shell](/images/portal-launch-icon.png)

Select __Bash__

![cloud shell](/images/overview-choices.png)


### 1.3 Create a Resource Group for the Hack

A resource group is a logical collection of Azure resources. All resources are deployed and managed in a resource group. To create a resource group:

##### 1.3.1 Resource Group - Use Azure CLI

```bash
resourceGroupName=churnhackathon-$RANDOM
location=SouthCentralUS

az group create \
   --name $resourceGroupName \
   --location $location 
```

#### 1.3.2 Resource Group - Use Azure Portal
[Create Resource Group](https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-create#create-a-resource-group)

### 1.4 Create and Azure Storage Account

An Azure storage account contains all of your Azure Storage data objects: blobs, files, queues, tables, and disks. Data in your Azure storage account is durable and highly available, secure, and massively scalable. We will use a storage account for our cold path storage and to store alert records.

#### 1.4.1 Storage - Use Azure CLI

```bash
accountName=churnhackathon-$RANDON

az storage account create \
    --name $accountName \
    --resource-group $resourceGroupName \
    --location $location \
    --sku Standard_LRS \
    --kind StorageV2

```

Create one container: __customerdata__.

```bash
export AZURE_STORAGE_ACCOUNT="<storage account>"
export AZURE_STORAGE_KEY="<sas key>"

az storage container create --name coldstore

```

#### 1.4.2 Storage - Use Azure Portal

[Create Azure Storage Account](https://docs.microsoft.com/en-us/azure/storage/common/storage-account-create?tabs=azure-portal)

Create one container using the portal: __customerdata__.