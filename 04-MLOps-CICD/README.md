# Azure Customer Churn Hackathon

![hackathon design](../images/hackathon.jpg)

## 4 Model Deployment Automation with Azure DevOps (CI/CD)

### Contents

- [Azure Customer Churn Hackathon](../)
  - [4 Model Deployment Automation with Azure DevOps (CI/CD)](#4-model-deployment-automation-with-azure-devops-cicd)
    - [4.1 Service Principal Authentication](#41-service-principal-authentication)
    - [4.1.1 Update you Azure Databricks Notebooks to use **Service Principal Authentication**](#411-update-you-azure-databricks-notebooks-to-use-service-principal-authentication)
    - [4.2 Azure DevOps MLOPs Pipeline](#42-azure-devops-mlops-pipeline)
      - [4.2.1 Connecting Azure Databricks to Azure DevOps](#421-connecting-azure-databricks-to-azure-devops)
      - [4.2.2 Create a new Build Pipeline MLOps Pipeline](#422-create-a-new-build-pipeline-mlops-pipeline)
      - [4.2.3 Adding Databrick Pipeline Tasks from Marketplace](#423-adding-databrick-pipeline-tasks-from-marketplace)
      - [4.2.3.1 Generate a Personal Access Token in Databricks](#4231-generate-a-personal-access-token-in-databricks)
      - [4.2.3.2 Passing Build Parameters using Azure Databricks Widgets](#4232-passing-build-parameters-using-azure-databricks-widgets)
      - [4.2.4 Create a new Release Pipeline MLOps Pipeline](#424-create-a-new-release-pipeline-mlops-pipeline)
      - [4.2.4.1 Passing Deployment Parameters using Azure Databricks Widgets](#4241-passing-deployment-parameters-using-azure-databricks-widgets)


![ml ops](../images/mlop_amls.PNG)

### 4.1 Service Principal Authentication

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

### 4.1.1 Update you Azure Databricks Notebooks to use **Service Principal Authentication**

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

### 4.2 Azure DevOps MLOPs Pipeline

Go to your Azure DevOps organization '<https://[organization].visualstudio.com/>' and create a new project.

![new project](../images/new_dev_ops_project.PNG)

![new project](../images/new_devops_project_details.PNG)


#### 4.2.1 Connecting Azure Databricks to Azure DevOps

 ![new rep](../images/repo_icon.PNG) After creating your new project, your will need to initialize the Repository.

![initialize](../images/initialize_repo.PNG)

Then follow the steps for [Azure DevOps Services Version Control](https://docs.microsoft.com/en-us/azure/databricks/notebooks/azure-devops-services-version-control)

#### 4.2.2 Create a new Build Pipeline MLOps Pipeline

![initialize](../images/new_pipeline.png)

![initialize](../images/classic_editor.PNG)

![initialize](../images/create_pipeline_details.PNG)

Start with an **Empty Job**

#### 4.2.3 Adding Databrick Pipeline Tasks from Marketplace

Go to **Add Task** then search for **Databricks**. You will need to add the Databricks tasks from the _Marketplace_.

![initialize](../images/market_place_devops.PNG)

**This does require elevated permissions on the subscription**

#### 4.2.3.1 Generate a Personal Access Token in Databricks

You will need to add a Databricks' Personal Access Token to the pipeline tasks.

Generate a Databricks [Personal Access Token](https://docs.microsoft.com/en-us/azure/databricks/dev-tools/api/latest/authentication#--generate-a-token)

Example of _Build Tasks_

![initialize](../images/build_pipeline.PNG)

#### 4.2.3.2 Passing Build Parameters using Azure Databricks Widgets

We can record the build number from Azure DevOps with our experiment to simplify tracking runs with data prep and model changes. To pass the build number as a parameter to Azure Databricks we will use a Notebook parameter through the use of [Azure Databricks Widgets](https://docs.microsoft.com/en-us/azure/databricks/notebooks/widgets)

In command cell 1 of our Training Notebook add the following to create our notebook parameters.

```python
dbutils.widgets.text("buildNumber","","buildNumber")
buildNumber = str(dbutils.widgets.get("buildNumber"))

dbutils.widgets.text("buildSource","","buildSource")
buildSource = str(dbutils.widgets.get("buildSource"))
```

![train widget](../images/training_adb_widgets.PNG)

Then we pass the _build number_ and _build source_ to our notebook from our __Execute Databricks Notebook Task__

![execute_training_task](../images/execute_training_task.PNG)

Having the build number gives us a lot of options. We can tag out experiment with it. We can also save (or tag) our training and test data for an audit trail.

First we need to set values when the notebook is ran manually. You could do something like:

```python
if len(buildNumber) <=0:
  buildNumber = str(uuid.uuid4())
  buildSource = "manual"
```

Then we can track our training data with the build:

```python
dbutils.fs.mkdirs("/mnt/churndata/runs/%s" % buildNumber)

train.to_csv("/dbfs/mnt/churndata/runs/%s/train.csv" % buildNumber, index=False)
test.to_csv("/dbfs/mnt/churndata/runs/%s/test.csv" % buildNumber, index=False)
```
Finally, we can _tag_ our __Experiment__:

```python
experiment.set_tags({'buildnumber':buildNumber,'buildsource': buildSource})
```

#### 4.2.4 Create a new Release Pipeline MLOps Pipeline

Don't forget to update your deployment notebook to use the [service principle for authentication](#411-update-you-azure-databricks-notebooks-to-use-service-principal-authentication).

![pipeline_release](../images/pipeline_release.png)

- **New Release** Pipeline and start with **Empty Job**
- Add a new _Stage_ calling it _"Stage"_ or _"QA"_.

Then click __Jobs Tasks__ to start building the release workflow.

![job_tasks](../images/job_tasks.PNG)

We will add tasks to to _Configure Databricks CLI_, _Start Cluster_, _Execute Deployment Notebook_ and then _Wait for Execution Completion_.

![release_task](../images/release_tasks.PNG)


#### 4.2.4.1 Passing Deployment Parameters using Azure Databricks Widgets

Again we will pass the build number to our deployment notebook, but we will also pass the deployment environment for notebook reuse. See [Azure Databricks Widgets](https://docs.microsoft.com/en-us/azure/databricks/notebooks/widgets) for more details on Azure Databricks Widget and Notebook parameters.

On cmd 1 of your deployment notebook add the following:

```python
dbutils.widgets.text("buildNumber","","buildNumber")
buildNumber = str(dbutils.widgets.get("buildNumber"))

dbutils.widgets.dropdown("deployType","Stage",["Stage","Prod"],"Deploy:")
deploy_type = dbutils.widgets.get("deployType")
```

![deploy_adb_widgets](../images/deploy_adb_widgets.PNG)

Again we will send the notebook parameters in our _Databricks Execute Notebook Task_

![deploy_notebook_task](../images/deploy_notebook_task.PNG)

We will updated the logic to deploy the model using the passed-in build number:

```python
ex = ws.experiments['automl-churn']


if(len(buildNumber)<= 0):
  run = [x for x in ex.get_runs()][0]
  buildNumber = str(run.tags['buildnumber'])
else:
  run = [x for x in ex.get_runs(tags={"buildnumber":buildNumber})][0]
    
print("Running for Build Number: {}".format(buildNumber))
```
When we deploy our web service we can indicate if it is _dev_, _stage_ or event _prod_ using the passed-in parameter. This can later be utilized for model validations and A/B Testing with our 'taged' test dataset.

```python
from azureml.core.webservice import AciWebservice, Webservice
from azureml.core.model import Model
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment

webservice_name = "churnservice-" + deploy_type 

```
Once we are done adding our task to our _'QA'_ or _'Stage'_  Deployment process. We can copy the process for _'Prod'_ and just modify the parameters we are passing to the Azure Databricks deployment notebook.

![prd_deployment_params](../images/prd_deployment_params.PNG)

__Example Release Pipeline__

![release_pipeline](../images/release_pipeline.PNG)


![question](../images/question_icon.jpg)__Thoughts on a validation step?__