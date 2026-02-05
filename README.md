# How to setup 

```shell
# Authenticate with Google Cloud Terminal
gcloud auth list

# Clone
git clone https://github.com/weimeilin79/agentverse-dataengineer
chmod +x ~/agentverse-dataengineer/init.sh
chmod +x ~/agentverse-dataengineer/set_env.sh
chmod +x ~/agentverse-dataengineer/data_setup.sh
chmod +x ~/agentverse-dungeon/run_cloudbuild.sh
chmod +x ~/agentverse-dungeon/start.sh

# create new project
cd ~/agentverse-dataengineer
./init.sh

# Set project ID and enable services
gcloud config set project $(cat ~/project_id.txt) --quiet

gcloud services enable \
    storage.googleapis.com \
    bigquery.googleapis.com \
    sqladmin.googleapis.com \
    aiplatform.googleapis.com \
    dataflow.googleapis.com \
    pubsub.googleapis.com \
    cloudfunctions.googleapis.com \
    run.googleapis.com \
    cloudbuild.googleapis.com \
    artifactregistry.googleapis.com \
    iam.googleapis.com \
    compute.googleapis.com \
    cloudresourcemanager.googleapis.com \
    cloudaicompanion.googleapis.com \
    bigqueryunified.googleapis.com 

# Create Artifact Registry
. ~/agentverse-dataengineer/set_env.sh
gcloud artifacts repositories create $REPO_NAME \
    --repository-format=docker \
    --location=$REGION \
    --description="Repository for Agentverse agents"

# Setup permissions
. ~/agentverse-dataengineer/set_env.sh

# --- Grant Core Data Permissions ---
gcloud projects add-iam-policy-binding $PROJECT_ID \
 --member="serviceAccount:$SERVICE_ACCOUNT_NAME" \
 --role="roles/storage.admin"

gcloud projects add-iam-policy-binding $PROJECT_ID \
 --member="serviceAccount:$SERVICE_ACCOUNT_NAME" \
 --role="roles/bigquery.admin"

# --- Grant Data Processing & AI Permissions ---
gcloud projects add-iam-policy-binding $PROJECT_ID  \
--member="serviceAccount:$SERVICE_ACCOUNT_NAME"  \
--role="roles/dataflow.admin"

gcloud projects add-iam-policy-binding $PROJECT_ID  \
--member="serviceAccount:$SERVICE_ACCOUNT_NAME"  \
--role="roles/cloudsql.admin"

gcloud projects add-iam-policy-binding $PROJECT_ID  \
--member="serviceAccount:$SERVICE_ACCOUNT_NAME"  \
--role="roles/pubsub.admin"

gcloud projects add-iam-policy-binding $PROJECT_ID  \
--member="serviceAccount:$SERVICE_ACCOUNT_NAME"  \
--role="roles/aiplatform.user"

# --- Grant Deployment & Execution Permissions ---
gcloud projects add-iam-policy-binding $PROJECT_ID  \
--member="serviceAccount:$SERVICE_ACCOUNT_NAME"  \
--role="roles/cloudbuild.builds.editor"

gcloud projects add-iam-policy-binding $PROJECT_ID  \
--member="serviceAccount:$SERVICE_ACCOUNT_NAME"  \
--role="roles/artifactregistry.admin"

gcloud projects add-iam-policy-binding $PROJECT_ID  \
--member="serviceAccount:$SERVICE_ACCOUNT_NAME"  \
--role="roles/run.admin"

gcloud projects add-iam-policy-binding $PROJECT_ID  \
--member="serviceAccount:$SERVICE_ACCOUNT_NAME"  \
--role="roles/iam.serviceAccountUser"

gcloud projects add-iam-policy-binding $PROJECT_ID  \
--member="serviceAccount:$SERVICE_ACCOUNT_NAME"  \
--role="roles/logging.logWriter"


gcloud projects add-iam-policy-binding $PROJECT_ID \
  --member="serviceAccount:$SERVICE_ACCOUNT_NAME" \
  --role="roles/dataflow.admin"

# Buld the project
. ~/agentverse-dataengineer/set_env.sh
cd ~/agentverse-dungeon
./run_cloudbuild.sh
cd ~/agentverse-dataengineer

```
