# Azure Customer Churn Workshop / Hack Event

![hackathon design](/images/hackathon.jpg)

Let’s get hands-on with Azure Machine Learning by working through a customer churn prediction solution. During this event we will quickly gain experience with Azure Machine Learning and Azure Databricks using the provided data in preparation for a hackathon.

## Objectives

- Learn how to securly ingest data located in an Azure Storage Account from Azure Databrick
- Track experimentation with Azure Machine Learning & Azure Databricks
- Leverage Azure Databricks as a collaboritive workspace to accelerate data exploration and model development.
- Explore MlOps pipelines with Azure Machine Learning and Azure DevOps

## Contents

- __[1 Hackathon Prerequisites](01-PreReq/)__
  * [1.1 Azure Portal](01-PreReq/#11-azure-portal)
    + [1.1.1 Do you have Enough Cores?](01-PreReq/#111-do-you-have-enough-cores-)
  * [1.2 Using Cloud Shell](01-PreReq/#12-using-cloud-shell)
  * [1.3 Create a Resource Group for the Hack](01-PreReq/#13-create-a-resource-group-for-the-hack)
    + [1.3.1 Resource Group - Use Azure CLI](01-PreReq/#131-resource-group---use-azure-cli)
    + [1.3.2 Resource Group - Use Azure Portal](01-PreReq/#132-resource-group---use-azure-portal)
  * [1.4 Create an Azure Storage Account](01-PreReq/#14-create-an-azure-storage-account)
    + [1.4.1 Storage - Use Azure CLI](01-PreReq/#141-storage---use-azure-cli)
    + [1.4.2 Storage - Use Azure Portal](01-PreReq/#142-storage---use-azure-portal)
    + [1.4.3 Download Churn Data](01-PreReq/#143-download-churn-data)
  * [1.5 Create Azure Machine Learning Workspace](01-PreReq/#15-create-azure-machine-learning-workspace)
    + [1.5.1 Azure Machine Learning Workspace - Use Azure CLI](01-PreReq/#151-azure-machine-learning-workspace---use-azure-cli)
    + [1.5.2 Azure Machine Learning Workspace - Use Azure Portal](01-PreReq/#152-azure-machine-learning-workspace---use-azure-portal)
  * [1.6 Azure Databricks (Premium Tier)](01-PreReq/#16-azure-databricks-premium-tier)
    + [1.6.1 Azure Databricks Workspace - Use Azure Portal](01-PreReq/#161-azure-databricks-workspace---use-azure-portal)
- __[2 Data Load with Azure Databricks](02-DataLoad/)__
  * [2.1 Set-up Secret Store with Azure Key Vault](02-DataLoad/#21-set-up-secret-store-with-azure-key-vault)
    + [2.1.1 Create an Azure Key Vault - Use Azure CLI](02-DataLoad/#211-create-an-azure-key-vault---use-azure-cli)
    + [2.1.2 Create an Azure Key Vault - Use Azure Portal](02-DataLoad/#212-create-an-azure-key-vault---use-azure-portal)
  * [2.2 Create a Secret and Secret scope for Azure Storage Account](02-DataLoad/#22-create-a-secret-and-secret-scope-for-azure-storage-account)
    + [2.2.1 Get Storage Account Key](02-DataLoad/#221-get-storage-account-key)
    + [2.2.2 Create Azure Key Vault Secret](02-DataLoad/#222-create-azure-key-vault-secret)
    + [2.2.3 Create Azure Databricks Secret Scope](02-DataLoad/#223-create-azure-databricks-secret-scope)
  * [2.3 Mounting Azure Storage Account with Azure Databricks](02-DataLoad/#23-mounting-azure-storage-account-with-azure-databricks)
    + [2.3.1 Create Databricks Cluster](02-DataLoad/#231-create-databricks-cluster)
    + [2.3.2 Create Databricks Notebook](02-DataLoad/#232-create-databricks-notebook)
  * [2.4 Loading Customer Chun Data](02-DataLoad/#24-loading-customer-chun-data)
    + [2.4.1 Mount Storage Account](02-DataLoad/#241-mount-storage-account)
      - [2.4.1.1 Example Mount](02-DataLoad/#2411-example-mount)
      - [2.4.1.2 Customer Churn Mount](02-DataLoad/#2412-customer-churn-mount)
    + [2.4.1 Load Customer Churn Data](02-DataLoad/#241-load-customer-churn-data)
    - [Azure Customer Churn Hackathon](#azure-customer-churn-hackathon)
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
