# gcp-dataflow-tweets

## Project Documentation: Data Pipeline with Apache Beam
This project involves creating a data pipeline using Apache Beam to process fake social network tweets. The pipeline utilizes Apache Beam for data processing, Google Cloud Pub/Sub for data ingestion, Google Cloud Dataflow for scalable execution, and Google BigQuery for storing the processed data.

The pipeline consists of the following steps:

- Generating fake tweets using the tweeter.py script and publishing them to a Pub/Sub topic.
- Configuring the project ID and subscription in the pipeline.py script for Pub/Sub data ingestion.
- Implementing data transformations, including parsing the Pub/Sub data, fixing the timestamp format, and flagging tweets containing dangerous words.
- Running the data pipeline locally using the Apache Beam runner to process data from Pub/Sub and write it to BigQuery.
- Executing the data pipeline on Google Cloud Dataflow for scalable and distributed processing of the Pub/Sub data, storing the results in BigQuery.
- By following these steps, you can set up a robust data pipeline that ingests, processes, and stores social network tweets using Apache Beam, Google Cloud Pub/Sub, Google Cloud Dataflow, and Google BigQuery.

Feel free to customize the project according to your requirements and refer to the detailed instructions in the previous sections for more information.

## Pipeline in Data Flow

![alt text](https://github.com/habibbueteee/gcp-dataflow-tweets/blob/main/images/Dataflow.png)

## Prerequisites
1. Python 3.x installed on your machine.
2. Google Cloud Platform (GCP) account with necessary permissions to access Pub/Sub and BigQuery services.
3. Virtual Python environment set up (optional but recommended).


## Steps
### Step 1: Installation
Clone the project repository from GitHub:

```sh
git clone <repository_url>
```
Navigate to the project directory
```sh
cd <project_directory>
```
Activate the virtual Python environment (if applicable):

```sh
source <path_to_virtual_env>/bin/activate
```

Install the required Python packages:

```sh
pip install -r requirements.txt
```

### Step 2: Configure Project ID and Topics
Open the tweeter.py file.
Replace <YOUR_PROJECT_ID> in the PROJECT_ID variable with your GCP project ID.
Set the TOPIC variable to specify the Pub/Sub topic name. You can use the default value 'tweets' or choose a custom name.


### Step 3: Generate Fake Tweets
Open the tweeter.py file.
Run the script to start generating fake tweets:
```sh
python tweeter.py
```

This script will generate 200 fake user profiles and start publishing tweets to the Pub/Sub topic.
Note: Use CTRL-C to stop generating tweets.

### Step 4: Configure Project ID and Subscription
Open the pipeline.py file.
Replace <YOUR_PROJECT_ID> in the PROJECT_ID variable with your GCP project ID.
Set the SUBSCRIPTION variable to specify the Pub/Sub subscription name. The default value assumes the subscription name is tweet-reader. Modify it if necessary.

### Step 5: Data Transformation and Flagging
Open the pipeline.py file.
Implement the following transformations in the pipeline:

```sh
Open the pipeline.py file.
def parse_pubsub(data):
    import json
    return json.loads(data)

def fix_timestamp(data):
    import datetime
    d = datetime.datetime.strptime(data['created_at'], "%d/%b/%Y:%H:%M:%S")
    data['created_at'] = d.strftime("%Y-%m-%d %H:%M:%S")
    return data

def check_tweet(data):
    BAD_WORDS = ['attack', 'drug', 'gun']
    data['flagged'] = False
    for word in BAD_WORDS:
        if word in data['text']:
            data['flagged'] = True
    return data
```

### Step 6: Run the Data Pipeline Locally
Open the terminal and navigate to the project directory.
Execute the following command to run the pipeline locally:

```sh
python pipeline.py --streaming
```
This command will read data from the Pub/Sub subscription, apply the defined transformations, and write the processed data to the BigQuery table.

### Step 7: Run the Data Pipeline with Cloud Dataflow
Open the terminal and navigate to the project directory.
Execute the following command to run the pipeline using Cloud Dataflow:

```sh
python pipeline.py --streaming --runner DataflowRunner \
--project <YOUR_PROJECT_NAME> \
--temp_location gs://<YOUR_BUCKET_NAME>/temp \
--staging_location gs://<YOUR_BUCKET_NAME>/staging \
--region us-central1 \
--job_name tweets
```

Replace <YOUR_PROJECT_NAME> with your GCP project name and <YOUR_BUCKET_NAME> with your Cloud Storage bucket name.
This command will execute the data pipeline on Cloud Dataflow, processing data from the Pub/Sub subscription and storing it in the specified BigQuery table.
