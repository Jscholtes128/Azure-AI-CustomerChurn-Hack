# Azure Customer Churn Hackathon

![hackathon design](../images/hackathon.jpg)

## 3 Azure Automated ML and Azure Databricks

### Contents

- [Azure Customer Churn Hackathon](../README.md)
  - [3 Azure Automated ML and Azure Databricks](#3-azure-automated-ml-and-azure-databricks)
    - [3.1 Install Python SDK on Databricks](#31-install-python-sdk-on-databricks)
    - [3.2 Prepare the Customer Churn Data](#32-prepare-the-customer-churn-data)
      - [3.2.1 Load Customer Churn Data into Dataframe](#321-load-customer-churn-data-into-dataframe)
      - [3.2.2 Data Prep](#322-data-prep)
        - [3.2.2.1 Undersampling Example](#3221-undersampling-example)
    - [3.3 Training with Azure Automated ML](#33-training-with-azure-automated-ml)
      - [3.3.1 Connect to your workspace](#331-connect-to-your-workspace)
      - [3.3.2 Load Preped ML Dataset](#332-load-preped-ml-dataset)
      - [3.3.3 AutoML Configuration](#333-automl-configuration)
      - [3.3.4 Running and AutoML Experiment](#334-running-and-automl-experiment)
    - [3.4 Deploying the Best Model](#34-deploying-the-best-model)
      - [3.4.1 Connect to your workspace](#341-connect-to-your-workspace)
      - [3.4.2 Deploy Latest Experiment](#342-deploy-latest-experiment)
        - [3.4.2.1 Azure Web Service Deployment](#3421-azure-web-service-deployment)

### 3.1 Install Python SDK on Databricks

Use these [instructions](https://docs.microsoft.com/en-us/azure/machine-learning/how-to-configure-environment#azure-databricks) to set-up Azure Databricks for Automated Machine Learning.  

We will be installing the Pypi: _azureml-sdk[automl]_

### 3.2 Prepare the Customer Churn Data

Create a new Azure Databricks Notebook for training the Automated ML model

#### 3.2.1 Load Customer Churn Data into Dataframe

```python
churn_df = spark.read.table('customer_churn')
```

![customer churn describe](../images/question_icon.jpg) How Does the data look?

Quick Describe

![customer churn describe](../images/customer_churn_describe.PNG)

Visualize the data with Azure Databricks

![customer churn describe](../images/databricks_customer_dash.PNG)

#### 3.2.2 Data Prep

[Example](https://www.kaggle.com/rafjaa/resampling-strategies-for-imbalanced-datasets) on resampling imbalanced data

##### 3.2.2.1 Undersampling Example

```python

drop_columns = ['RowNumber','CustomerId','Surname']
customer_columns = churn_df.columns
column = [x for x in customer_columns if x not in drop_columns]

final_df = churn_df[column]
final_df_pd = final_df.toPandas()

import pandas as pd
# Exited count
count_exited_0, count_exited_1 = final_df_pd.Exited.value_counts()

# Divide by Exited Customers
df_exited_0 = final_df_pd[final_df_pd['Exited'] == 0]
df_exited_1 = final_df_pd[final_df_pd['Exited'] == 1]

df_exited_0_under = df_exited_0.sample(count_exited_1)
df_test_under = pd.concat([df_exited_0_under, df_exited_1], axis=0)

churn_df = spark.createDataFrame(df_test_under)

```

![undersample hist](../images/undersample_exited_hist.PNG)

```python
#Save 'Silver' Dataset for Auto ML
churn_df.write.format('delta').mode('overwrite').partitionBy('Geography').option('path', "/mnt/churndata/silver").saveAsTable('customer_churn_silver')
```

### 3.3 Training with Azure Automated ML

#### 3.3.1 Connect to your workspace

```python
from azureml.core.workspace import Workspace
from azureml.train.automl import AutoMLConfig
from azureml.core.experiment import Experiment
import logging


subscription_id = "YOUR SUBSCRIPTION"
resource_group = "YOUR RESOURCE GROUP"
workspace_name =  "AZURE ML WORKSPACE NAME"#your workspace name
workspace_region = "centralus" #your region


ws = Workspace(workspace_name = workspace_name,
               subscription_id = subscription_id,
               resource_group = resource_group)

```

#### 3.3.2 Load Preped ML Dataset

```python
import azureml.dataprep as dprep
import uuid

churn_df = spark.read.table('customer_churn_silver')

x_prep = dprep.read_pandas_dataframe(churn_df.toPandas().drop('Exited',axis=1),temp_folder='/dbfs/tmp'+str(uuid.uuid4()))
y_prep  = dprep.read_pandas_dataframe(churn_df.toPandas()[['Exited']],temp_folder='/dbfs/tmp'+str(uuid.uuid4()))
```

#### 3.3.3 AutoML Configuration

We will crate the AutoMLCofig to specify the setting for our ML experimentation run. Please view the latest parameter details for [AutoMlConfig](https://docs.microsoft.com/en-us/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) as new features continue to be added.

```python
automl_classifier=AutoMLConfig(
    task='classification',
    primary_metric='AUC_weighted',
    experiment_timeout_minutes=30,
    X = x_prep,
    y = y_prep,
    preprocess = True,
    spark_context=sc,
    iterations = 15,
    max_concurrent_iterations = 6,
    iteration_timeout_minutes = 2,
    n_cross_validations=2,
    enable_early_stopping=True,
    experiment_exit_score = .98)

```

Next we will create our Azure Machine Learning Experiment:

```python
experiment_name = 'automl-churn'
project_folder = './projects/automl-churn'

experiment = Experiment(ws, experiment_name)
```

#### 3.3.4 Running and AutoML Experiment

We will submit our automl configuration using the experiment we created above to run the training:

```python
run = experiment.submit(automl_classifier, show_output=False)
```

For a large number of iterations it is recommended to set *show_output* to __False__.

Go to your Azure Machine Learning Workspace and launch the new studio.

![aml new studio](../images/new_amls_studio.PNG)

Then go to  __Experiments__ to view your running experiment

![aml new studio](../images/experiments_link.PNG)

![aml new studio](../images/experiment_runs.PNG)

Find your 'Best' model.

![aml new studio](../images/run_models.PNG)

### 3.4 Deploying the Best Model

Next we will take the 'best' model from out AutoML experiment, register it, have a docker image created for deployment as a web service.

![model_deployment.PNG](../images/model_deployment.PNG)

Create a new Azure Databricks Notebook for training the Automated ML model.

#### 3.4.1 Connect to your workspace

```python
from azureml.core.workspace import Workspace
from azureml.train.automl import AutoMLConfig
from azureml.core.experiment import Experiment
import logging


subscription_id = "YOUR SUBSCRIPTION"
resource_group = "YOUR RESOURCE GROUP"
workspace_name =  "AZURE ML WORKSPACE NAME"#your workspace name
workspace_region = "centralus" #your region


ws = Workspace(workspace_name = workspace_name,
               subscription_id = subscription_id,
               resource_group = resource_group)
```

#### 3.4.2 Deploy Latest Experiment

After connecting to your workspace we will create a deployment for the last AutoML run

```python

# Get automl-churn experiments
ex = ws.experiments['automl-churn']

#get last run
run = [x for x in ex.get_runs()][0]
```

In this documentation we have been working with Azure Automated ML. Because our last experiment was an automl run, we can use the attributes of _AutoMLRun_ to simplify deploying the _best_ model.

```python
from azureml.train.automl.run import AutoMLRun

#get AutoMLRun from last run
autorun = AutoMLRun(ex, run.id)

#get 'best' model
best_run, fitted_model = autorun.get_output()
```

Now that we have the _best_ model we will want to register it for deployment. We will use the _AutoMlRun_ __register_model__ function. The model registry tracks the model version and we can add a friendly description along with searchable tags. 

```python
description = 'Customer Churn Auto ML'
model = autorun.register_model(description = description)
print(autorun.model_id)
```

##### 3.4.2.1 Azure Web Service Deployment

Deploying as an Azure Web Service offers the following benefits:

- A deployed web service can run on [Azure Container Instances (ACI)](https://docs.microsoft.com/en-us/azure/container-instances/container-instances-overview), [Azure Kubernetes Service (AKS)](https://docs.microsoft.com/en-us/azure/aks/intro-kubernetes), or field-programmable gate arrays (FPGA). 
- Can receive scoring requests via an exposed a load-balanced, HTTP endpoint.
- Can be monitored by collecting Application Insight telemetry and/or model telemetry. 
- Azure can automatically scale deployments

For deployment we will include __AciWebservice__ and __Webservice__. Normally we would have to create our own _scoring.py_ file for inferencing and the conda dependency (YML) file, but with Azure AutoML these files are created along with the _best_ model. We will just use them from the model output folder.

Download _best_ model, scoring file and yml file.

```python
deployment_paths = '/dbfs/mnt/churndata/runs/%s/deployment/' % run.id

best_run.download_files(prefix='outputs',output_directory=deployment_paths)

```

Deploy model as ACI web service (__auth_enabled=True__ must be set for key authentication)

```python
from azureml.core.webservice import AciWebservice, Webservice
from azureml.core.model import Model
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment

webservice_name = "churnservice"

#remove webservice is exists
try:
    service = Webservice(ws, webservice_name)        
    service.delete()
    
except Exception as e:
    print("No Existing Service")


myenv = Environment.from_conda_specification(name = 'myenv',
                                             file_path = (deployment_paths + "/outputs/conda_env_v_1_0_0.yml"))

inference_config = InferenceConfig(entry_script=(deployment_paths + "/outputs/scoring_file_v_1_0_0.py"),
                                   environment=myenv)

deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1,auth_enabled=True)
service = Model.deploy(ws, webservice_name, [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)

```

![question](../images/question_icon.jpg)__Can you test the web service?__