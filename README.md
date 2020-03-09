# Azure Customer Churn Workshop / Hack Event

![hackathon design](/images/hackathon.jpg)

Let’s get hands-on with Azure Machine Learning by working through a customer churn prediction solution. During this event we will quickly gain experience with Azure Machine Learning and Azure Databricks using the provided data in preparation for a hackathon.

## Objectives

- Learn how to securely ingest data located in an Azure Storage Account from Azure Databrick
- Track experimentation with Azure Machine Learning & Azure Databricks
- Leverage Azure Databricks as a collaborative workspace to accelerate data exploration and model development.
- Explore MlOps pipelines with Azure Machine Learning and Azure DevOps

## [Step 1 - Hackathon Prerequisites](01-PreReq/)

<table>
<tr>
<td><img align="left" src="/images/config_img.png"> </td><td>Set-Up your Azure resources to complete this End-2-End workshop or perform a similar hack event with your own data.

The following resources are implemented through-out this material, please ensure you can create in your subscription or resource group:

- [Azure Storage Account - Blob](https://docs.microsoft.com/en-us/azure/storage/common/storage-account-overview)
- [Azure Machine Learning Workspace](https://docs.microsoft.com/en-us/azure/machine-learning/overview-what-is-azure-ml)
- [Azure Databricks](https://docs.microsoft.com/en-us/azure/azure-databricks/what-is-azure-databricks)
- [Azure DevOps](https://docs.microsoft.com/en-us/azure/devops/user-guide/what-is-azure-devops?view=azure-devops)
</td>
</tr>
</table>

## [Step 2 - Data Load with Azure Databricks](02-DataLoad/)

<table>
<tr>
<td><img align="left" src="/images/data_load.png" > </td><td>Leverage Powerful cloud compute with Spark as a Service to load, transform and explore the customer churn data.
</td>
</tr>
</table>

## [Step 3 Azure Automated ML and Azure Databricks](03-AutoML/)

<table>
<tr>
<td><img align="left" src="/images/ml_img.png" > </td><td>Combine Azure Databricks with Azure Machine Learning Service to accelerate the development of the customer churn model with AutoML.
</td>
</tr>
</table>


## Contents

- __[4 Azure MLOPs - CI/CD](04-MLOps-CICD/)__
  * [4.1 Service Principal Authentication](04-MLOps-CICD/#41-service-principal-authentication)
  * [4.2 Azure DevOps MLOPs Pipeline](04-MLOps-CICD/#42-azure-devops-mlops-pipeline)
    + [4.2.1 Connecting Azure Databricks to Azure DevOps](04-MLOps-CICD/#421-connecting-azure-databricks-to-azure-devops)
    + [4.2.2 Create a new Pipeline MLOps Pipeline](04-MLOps-CICD/#422-create-a-new-pipeline-mlops-pipeline)
    + [4.2.3 Adding Databrick Pipeline Tasks from Marketplace](04-MLOps-CICD/#423-adding-databrick-pipeline-tasks-from-marketplace)
    + [4.2.3.1 Generate a Personal Access Token in Databricks](04-MLOps-CICD/#4231-generate-a-personal-access-token-in-databricks)
