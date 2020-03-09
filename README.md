# Azure Customer Churn Workshop / Hack Event

![hackathon design](/images/hackathon.jpg)

Let’s get hands-on with Azure Machine Learning by working through a customer churn prediction solution. During this event we will quickly gain experience with Azure Machine Learning and Azure Databricks using the provided data in preparation for a hackathon.

## Objectives

- Learn how to securely ingest data located in an Azure Storage Account from Azure Databrick
- Track experimentation with Azure Machine Learning & Azure Databricks
- Leverage Azure Databricks as a collaborative workspace to accelerate data exploration and model development.
- Explore MlOps pipelines with Azure Machine Learning and Azure DevOps

## [Step 1 Hackathon Prerequisites](01-PreReq/)

<table border=0>
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

## [Step 2 Data Load with Azure Databricks](02-DataLoad/)

<table border=0>
<tr>
<td><img align="left" src="/images/data_load.png" > </td><td>Leverage Powerful cloud compute with Spark as a Service to load, transform and explore the customer churn data.
</td>
</tr>
</table>

## Contents

- __[3 Azure Automated ML and Azure Databricks](03-AutoML/)__
  * [3.1 Install Python SDK on Databricks](03-AutoML/#31-install-python-sdk-on-databricks)
  * [3.2 Prepare the Customer Churn Data](03-AutoML/#32-prepare-the-customer-churn-data)
    + [3.2.1 Load Customer Churn Data into Dataframe](03-AutoML/#321-load-customer-churn-data-into-dataframe)
    + [3.2.2 Data Prep](03-AutoML/#322-data-prep)
      - [3.2.2.1 Undersampling Example](03-AutoML/#3221-undersampling-example)
  * [3.3 Training with Azure Automated ML](03-AutoML/#33-training-with-azure-automated-ml)
    + [3.3.1 Connect to your workspace](03-AutoML/#331-connect-to-your-workspace)
    + [3.3.2 Load Preped ML Dataset](03-AutoML/#332-load-preped-ml-dataset)
    + [3.3.3 AutoML Configuration](03-AutoML/#333-automl-configuration)
    + [3.3.4 Running and AutoML Experiment](03-AutoML/#334-running-and-automl-experiment)
- __[4 Azure MLOPs - CI/CD](04-MLOps-CICD/)__
  * [4.1 Service Principal Authentication](04-MLOps-CICD/#41-service-principal-authentication)
  * [4.2 Azure DevOps MLOPs Pipeline](04-MLOps-CICD/#42-azure-devops-mlops-pipeline)
    + [4.2.1 Connecting Azure Databricks to Azure DevOps](04-MLOps-CICD/#421-connecting-azure-databricks-to-azure-devops)
    + [4.2.2 Create a new Pipeline MLOps Pipeline](04-MLOps-CICD/#422-create-a-new-pipeline-mlops-pipeline)
    + [4.2.3 Adding Databrick Pipeline Tasks from Marketplace](04-MLOps-CICD/#423-adding-databrick-pipeline-tasks-from-marketplace)
    + [4.2.3.1 Generate a Personal Access Token in Databricks](04-MLOps-CICD/#4231-generate-a-personal-access-token-in-databricks)
