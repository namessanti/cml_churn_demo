# TKO Lab
## Telco churn with refactor code
This is the CML port of the Refractor prototype which is part of the [Interpretability
report from Cloudera Fast Forward Labs](https://clients.fastforwardlabs.com/ff06/report).

### Auto Deploy
To build all the project artifacts, run the `0_build_projet.py` file in a python3 session. 

### Manual Setup
If you wish to do this manually, follow the steps below.

### 0 Install Requirements
At the start of the manual build process, you need to open a workbench and run
`!pip3 install -r requirements.txt`


### 1 Ingest Data
Open `1_data_ingest.py` in a workbench: python3, 1 CPU, 2 GB.

Run the file. 


### 2 Explore Data
Open a Jupyter Notebook session: python3, 1 CPU, 2 GB and open the `2_data_exploration.ipynb` file.

At the top of the page click Cells > Run All

### 3 Train Models
A model has been pre-trained and placed in the models/test_model directory.  

If you want to retrain the model run the 3_train_models.py code in a pyton3 1 CPU, 2 GB session to train a new model.  

The model artifact will be saved in the models directory named after the datestamp, dataset and algorithm (ie. 20191120T161757_ibm_linear). The default settings will create a linear regression model against the IBM telco dataset. However, the code is vary modular and can train multiple model types against essentially any tabular dataset (see below for details).  


### 4 Serve Models
Go to the **Models** section and create a new Explainer model with the following:

* **Name**: Explainer
* **Description**: Explain customer churn prediction
* **File**: 4_model_serve_explainer.py
* **Function**: explain
* **Input**: `{"StreamingTV":"No","MonthlyCharges":70.35,"PhoneService":"No","PaperlessBilling":"No","Partner":"No","OnlineBackup":"No","gender":"Female","Contract":"Month-to-month","TotalCharges":1397.475,"StreamingMovies":"No","DeviceProtection":"No","PaymentMethod":"Bank transfer (automatic)","tenure":29,"Dependents":"No","OnlineSecurity":"No","MultipleLines":"No","InternetService":"DSL","SeniorCitizen":"No","TechSupport":"No"}`
* **Kernel**: Python 3

If you created your own model (see above)
* Click on "Set Environment Variables" and add:
  * **Name**: CHURN_MODEL_NAME
  * **Value**: 20191120T161757_telco_linear  **your model name from above**
  Click "Add" and "Deploy Model"

In the deployed Explainer model -> Settings note (copy) the "Access Key" (ie. mukd9sit7tacnfq2phhn3whc4unq1f38)


### 5 Deploy Application

From the Project level click on "Open Workbench" (note you don't actually have to Launch a session) in order to edit a file.
Select the flask/single_view.html file and paste the Access Key in at line 19. 
Save and go back to the Project.  

Go to the **Applications** section and select "New Application" with the following:
* **Name**: Visual Churn Analysis
* **Subdomain**: telco-churn
* **Script**: 5_application.py
* **Kernel**: Python 3
* **Engine Profile**: 1vCPU / 2 GiB Memory  

If you created your own model (see above)
* Add Environment Variables:  
  * **Name**: CHURN_MODEL_NAME  
  * **Value**: 20191120T161757_tekci_linear  **your model name from above**  
  Click "Add" and "Deploy Model"  

After the Application deploys, click on the blue-arrow next to the name.  The initial view is a table of rows selected at  random from the dataset.  This shows a global view of which features are most important for the predictor model.  

Clicking on any single row will show a "local" interpretabilty of a particular instance.  Here you 
can see how adjusting any one of the features will change the instance's churn prediction.  

### 6 Calculate Accuracy

Ensure a `CDSW_USER_API_KEY` environment variable with your user api key (main settings on the home screen) is defined for the project.

Create a Job to show regular calculation of the model's accuracy

Replace the CRN with the deployment CRN number from the monitoring tab

[crn]: https://github.com/adam-p/markdown-here/raw/master/src/common/images/icon48.png

Go to the **Jobs** section and select "New Job" with the following:
* **Name**: Calculate Accuracy
* **Script**: 6_calculate_accuracy.py
* **Schedule**: Manual
* **Kernel**: Python 3
* **Engine Profile**: 1vCPU / 2 GiB Memory

Run the script a few times to accuracy info stored in the metrics store

### 7 Analyze Metrics
Ensure a `CDSW_USER_API_KEY` environment variable with your user api key (main settings on the home screen) is defined for the project.

Run custom python to analyze metrics from our metrics store

Open 7_analyze_metrics.py and replace the CRN with the deployed model from the Monitoring tab.

Run the python script.