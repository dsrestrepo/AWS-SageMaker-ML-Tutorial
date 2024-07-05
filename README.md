# Example ML Deployment with AWS SageMaker and S3

This repository provides an end-to-end tutorial for deploying a machine learning model using AWS SageMaker and S3. We use XGBoost for our model, which will be trained on a dataset, deployed as a SageMaker endpoint, and used to make predictions.

## Repository Contents

- `Example_ML_Deploy.ipynb`: Jupyter notebook for training and deploying an XGBoost model using AWS SageMaker.
- `Inference.ipynb`: Jupyter notebook demonstrating how to make predictions using the deployed model.
- `README.md`: This readme file.
- `test.csv`: CSV file containing the test dataset.
- `train.csv`: CSV file containing the training dataset.

## Prerequisites

Before you start, make sure you have the following:

1. An AWS account.
2. AWS CLI installed and configured with appropriate permissions.
3. SageMaker Jupyter notebook instance or a local Jupyter environment.
4. `boto3` and `sagemaker` Python packages installed.

## Steps

### 1. Setting Up AWS S3

1. Create an S3 bucket to store the training data.
   ```sh
   aws s3 mb s3://your-bucket-name
   ```

2. Upload the `train.csv` and `test.csv` files to your S3 bucket.

   ```sh
    aws s3 cp train.csv s3://your-bucket-name/train.csv
    aws s3 cp test.csv s3://your-bucket-name/test.csv
    ```
### 2. Training and Deploying the Model with SageMaker

1. Follow the steps in the Example_ML_Deploy.ipynb notebook to:

Import Libraries and Set Up SageMaker Session and Role
Prepare the Data
Set Up the XGBoost Estimator
Set Hyperparameters
Start Training
Deploy the Trained Model

### 3. Making Predictions
Use the Inference.ipynb notebook to make predictions using the deployed model. It demonstrates how to:

Serialize input data.
Invoke the SageMaker endpoint.
Deserialize and interpret the prediction results.
Here is a snippet of the code used for making predictions:

```
import boto3
import numpy as np

# Create a SageMaker runtime client
client = boto3.client('sagemaker-runtime')

# Example data point
data = np.array([6.1, 2.8, 4.7, 1.2])
payload = ','.join(map(str, data))

# Invoke the endpoint
response = client.invoke_endpoint(
    EndpointName='your-endpoint-name',
    ContentType='text/csv',
    Body=payload
)

# Get and print the result
result = response['Body'].read().decode('utf-8')
print(result)

```

### 4. Cleanup
To avoid incurring unnecessary costs, make sure to delete the endpoint and the S3 bucket after you are done:

Delete the Endpoint
```
predictor.delete_endpoint()
```

Delete the S3 Bucket
```
aws s3 rb s3://your-bucket-name --force
```