# Azure Customer Churn Hackathon

![hackathon design](../images/hackathon.jpg)

## 3 Azure Automated ML and Azure Databricks

### 3.1 Install Python SDK on Databricks

Use these [instructions](https://docs.microsoft.com/en-us/azure/machine-learning/how-to-configure-environment#azure-databricks) to set-up Azure Databricks for Automated Machine Learning.  

We will be installing the Pypi: _azureml-sdk[automl]_

### 3.2 Prepare the Customer Churn Data

Create a new Azure Databricks Notebook for training the Automated ML model

#### 3.2.1 Load Customer Churn Data into Dataframe


```python
churn_df = spark.read.table('customer_churn')
```

![customer churn describe](../images/question_icon.jpg){: .image-left } How Does the data look?

Quick Describe

![customer churn describe](../images/customer_churn_describe.PNG)

Visualize the data with Azure Databricks

![customer churn describe](../images/databricks_customer_dash.PNG)

#### 3.2.2 Data Prep

[Example](https://www.kaggle.com/rafjaa/resampling-strategies-for-imbalanced-datasets) on resampling imbalanced data

```python
import azureml.dataprep as dprep
import uuid

x_prep = dprep.read_pandas_dataframe(<Create Preped Pandas Dataframe>.drop('Exited',axis=1),temp_folder='/dbfs/tmp'+str(uuid.uuid4()))
y_prep  = dprep.read_pandas_dataframe(<<Create Preped Pandas Dataframe>[['Exited']],temp_folder='/dbfs/tmp'+str(uuid.uuid4()))
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

#### 3.3.2 AutoML COnfiguration

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

#### 3.3.3 Running and AutoML Experiment

We will submit our automl configuration using the experiment we created above to run the training:

```python
run = experiment.submit(automl_classifier, show_output=False)
```

For a large number of iterations it is recommended to set *show_output* to __False__.
