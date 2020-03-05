# Azure Customer Churn Workshop / Hack Event

![hackathon design](/images/hackathon.jpg)

Let’s get hands-on with Azure Machine Learning by working through a customer churn prediction solution. During this event we will quickly gain experience with Azure Machine Learning and Azure Databricks using the provided data in preparation for a hackathon.

## Objectives

- Learn how to securly ingest data located in an Azure Storage Account from Azure Databrick
- Track experimentation with Azure Machine Learning & Azure Databricks
- Leverage Azure Databricks as a collaboritive workspace to accelerate data exploration and model development.
- Explore MlOps pipelines with Azure Machine Learning and Azure DevOps

## Contents

* 1 [Hackathon Prerequisites](#1-hackathon-prerequisites)
    * 1.1 [Azure Portal](#11-azure-portal)
    * 1.2 [Using Cloud Shell](#12-using-cloud-shell)
* 2 [Data Preparation](02-DataLoad/)

## 1 Hackathon Prerequisites

The following resources are implemented during the hackathon, please ensure you can create in your subscription or resource group:

- [Azure Storage Account - Blob](https://docs.microsoft.com/en-us/azure/storage/common/storage-account-overview)
- [Azure Machine Learning Workspace](https://docs.microsoft.com/en-us/azure/machine-learning/overview-what-is-azure-ml)
- [Azure Databricks](https://docs.microsoft.com/en-us/azure/azure-databricks/what-is-azure-databricks)
- [Azure DevOps](https://docs.microsoft.com/en-us/azure/devops/user-guide/what-is-azure-devops?view=azure-devops)

### 1.1 Azure Portal

Azure subscription. If you don't have one, create a [free account](https://azure.microsoft.com/en-us/free/) before you begin.

#### 1.1.1 Do you have Enough Cores?

We will need to have enough cores availble to use to spin up a multinode Azure Databricks cluster and deploy to ACI (Azure Container Instance) at the very least. At the very minimum we would need 16 cores available ((4x) 4 Core VMs).

[View you usage and quotas.](https://docs.microsoft.com/en-us/azure/machine-learning/how-to-manage-quotas#view-your-usage-and-quotas)

### 1.2 Using Cloud Shell

The following bach commands will be ran using the [Azure Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview). 

Launch from Azure portal using the Cloud Shell icon

![cloud shell](/images/portal-launch-icon.png)

Select __Bash__

![cloud shell](/images/overview-choices.png)

### 1.3 Create a Resource Group for the Hack

A resource group is a logical collection of Azure resources. All resources are deployed and managed in a resource group. To create a resource group:

#### 1.3.1 Resource Group - Use Azure CLI

```bash
resourceGroupName=churnhackathon-$RANDOM
location=SouthCentralUS

az group create \
   --name $resourceGroupName \
   --location $location
```

#### 1.3.2 Resource Group - Use Azure Portal

[Create Resource Group](https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-create#create-a-resource-group)

### 1.4 Create an Azure Storage Account

An Azure storage account contains all of your Azure Storage data objects: blobs, files, queues, tables, and disks. Data in your Azure storage account is durable and highly available, secure, and massively scalable. We will use a storage account for our cold path storage and to store alert records.

#### 1.4.1 Storage - Use Azure CLI

```bash
accountName=churnhackstore$RANDOM


az storage account create \
    --name $accountName \
    --resource-group $resourceGroupName \
    --location $location \
    --sku Standard_LRS \
    --kind StorageV2

az storage container create --account-name $accountName --name data

```

#### 1.4.2 Storage - Use Azure Portal

[Create Azure Storage Account](https://docs.microsoft.com/en-us/azure/storage/common/storage-account-create?tabs=azure-portal)

Create one container for model data using the portal: __data__

#### 1.4.3 Download Churn Data

**Run from Cloud Shell (Bash)

```bash

curl -L "https://workshopfilesblob.blob.core.windows.net/churn/bank-customer-churn-modeling.zip?sp=r&st=2020-03-04T16:18:55Z&se=2022-03-02T00:18:55Z&spr=https&sv=2019-02-02&sr=b&sig=wO%2FeBqbMvwNWjyJ6ySCEg1nXg51k7JoMG8qUTvxXpnM%3D" > churn.zip

unzip churn.zip

az storage blob upload \
    --account-name $accountName \
    --container-name data \
    --name Churn_Modelling.csv  \
    --file Churn_Modelling.csv

rm churn.zip
rm Churn_Modelling.csv
```

### 1.5 Create Azure Machine Learning Workspace

Azure Machine Learning can be used for any kind of machine learning, from classical ml to deep learning, supervised, and unsupervised learning. Whether you prefer to write Python or R code or zero-code/low-code options such as the designer, you can build, train, and track highly accurate machine learning and deep-learning models in an __Azure Machine Learning Workspace__.

__Pricing Tiers:__ Azure Machine Learning Serivce has two pricing tiers, Basice and Enterprice. Basic will be suficient for this material as we will use a 'code first' approach. Please review the [pricing tier documentation](https://azure.microsoft.com/en-us/pricing/details/machine-learning/).

![amls](/images/azure-machine-learning-taxonomy.png)

#### 1.5.1 Azure Machine Learning Workspace - Use Azure CLI

```bash
workspace=churnhackml-$RANDOM
az extension add -n azure-cli-ml
az ml workspace create -w $workspace -g $resourceGroupName
```

#### 1.5.2 Azure Machine Learning Workspace - Use Azure Portal

[Create Azure Machine Learning Workspace](https://docs.microsoft.com/en-us/azure/machine-learning/how-to-manage-workspace)

### 1.6 Azure Databricks

Azure Databricks is an Apache Spark-based analytics platform optimized for the Microsoft Azure cloud services platform. 

![databricks](/images/azure-databricks-overview.png)

#### 1.6.1 Azure Databricks Workspace - Use Azure Portal

[Create Azure Databricks Workspace](https://docs.microsoft.com/en-us/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace)