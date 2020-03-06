# Azure Customer Churn Hackathon

![hackathon design](../images/hackathon.jpg)

## 5 Azure MLOPs - CI/CD

### 5.1 Service Principal Authentication

When setting up a machine learning workflow as an automated process, we recommend using Service Principal Authentication. This approach decouples the authentication from any specific user login, and allows managed access control.
Note that you must have administrator privileges over the Azure subscription to complete these steps.
The first step is to create a service principal. First, go to [Azure Portal](https://portal.azure.com/), select __Azure Active Directory__ and __App Registrations__. Then select __+New application__, give your service principal a name, for example my-svc-principal. You can leave other parameters as is.

Then click __Register__.

![scv pr1](../images/svc-pr-1.png)

From the page for your newly created service principal, copy the _Application ID_ and _Tenant ID_ as they are needed later.

![scv pr1](../images/svc-pr-2.png)

Then select __Certificates & secrets__, and __+New client secret__ write a description for your key, and select duration. Then click __Add__, and copy the value of client secret to a secure location.

![scv pr1](../images/svc-pr-3.png)

Finally, you need to give the service principal permissions to access your workspace. Navigate to __Resource Groups__, to the resource group for your Machine Learning Workspace.

Then select __Access Control (IAM)__ and Add a __role assignment__. For Role, specify which level of access you need to grant, for example Contributor. Start entering your service principal name and once it is found, select it, and click __Save__.

![scv pr1](../images/svc-pr-4.png)

Now you are ready to use the service principal authentication. For example, to connect to your Workspace, see code below and enter your own values for tenant ID, application ID, subscription ID, resource group and workspace.

It is strongly recommended that you do not insert the secret password to code. Please create a Key Vault back secret scope as you did in [2.2 Create a Secret and Secret scope for Azure Storage Account](02-DataLoad#22-create-a-secret-and-secret-scope-for-azure-storage-account).

Update you Azure Databricks Notebooks to use **Service Principal Authentication**.

```python
from azureml.core.authentication import ServicePrincipalAuthentication

svc_pr_password = dbutils.secrets.get(scope = "Secret Scope", key = "Secret")

svc_pr = ServicePrincipalAuthentication(
    tenant_id="Tenent ID",
    service_principal_id="Application ID",
    service_principal_password=svc_pr_password)


ws = Workspace(workspace_name = workspace_name,
               subscription_id = subscription_id,
               resource_group = resource_group,
               auth=svc_pr)
```

### 5.2 Azure DevOps MLOPs Pipeline

Go to your Azure DevOps organization '<https://[organization].visualstudio.com/>' and create a new project.

![new project](../images/new_dev_ops_project.PNG)

![new project](../images/new_devops_project_details.PNG)


#### 5.2.1 Connecting Azure Databricks to Azure DevOps

 ![new rep](../images/repo_icon.PNG) Afer createing your new project, your will need to initialize the Repository.

![initialize](../images/initialize_repo.PNG)

Then follow the steps for [Azure DevOps Services Version Control](https://docs.microsoft.com/en-us/azure/databricks/notebooks/azure-devops-services-version-control)

#### 5.2.2 Create a new Pipeline MLOps Pipeline

![initialize](../images/new_pipeline.png)

![initialize](../images/classic_editor.PNG)

![initialize](../images/create_pipeline_details.PNG)

Start with an **Empty Job**

#### 5.2.3 Adding Databrick Pipeline Tasks from Marketplace

Go to **Add Task** then search for **Databricks**. You will need to add the Databricks tasks from the _Marketplace_.

![initialize](../images/market_place_devops.PNG)

**This does require elevated permissions on the subscription**

Example of _Build pipeline_

![initialize](../images/build_pipeline.PNG)

#### 5.2.3.1 Generate a Personal Access Token in Databricks

Generate a Databricks [Personal Access Token](https://docs.microsoft.com/en-us/azure/databricks/dev-tools/api/latest/authentication#--generate-a-token)
