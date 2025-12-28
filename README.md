# Running Chatbot which uses custom PDF docs as source using vertex AI in a Kubernetes Cluster


Running RAG chatbot in a Kubernetes Cluster

## Prerequisite

- Enable Kubernetes under Settings > Kubernetes
- GCS Bucket
- Enable Vertex AI
- Elasticsearch > As a Vector DB

## Clone the repository

```
git clone https://github.com/arunudaiyar/gcp-rag-gemini.git
cd gcp-rag-gemini
```

## Follow the blow steps

```
#Set env
export PROJECT_ID=project-123
export KUBERNETES_CLUSTER_PREFIX=elasticsearch
export CONTROL_PLANE_LOCATION=europe-north2-a
export REGION=europe-north2
export DB_NAMESPACE=elastic

#update cluster addons
gcloud container clusters update ${CLUSTER-NAME} \
    --project=${PROJECT_ID} \
    --location=${REGION} \
  --update-addons=StatefulHA=ENABLED

#Set Docker Repo
export DOCKER_REPO=europe-north2-docker.pkg.dev/${PROJECT_ID}/elasticsearch-images
export REGION=europe-north2

#Buid the docker image for chatbot and embeding
gcloud builds submit gcp-rag/elasticsearch/docker/chatbot --region=${REGION} \
  --tag ${DOCKER_REPO}/chatbot:1.0 --async
gcloud builds submit gcp-rag/elasticsearch/docker/embed-docs --region=${REGION} \
  --tag ${DOCKER_REPO}/embed-docs:1.0 --async

#update the image on deployment
sed "s|<DOCKER_REPO>|$DOCKER_REPO|" gcp-rag/elasticsearch/manifests/05-rag/chatbot.yaml | kubectl -n elastic apply -f -
sed "s|<DOCKER_REPO>|$DOCKER_REPO|" gcp-rag/elasticsearch/manifests/05-rag/docs-embedder.yaml | kubectl -n elastic apply -f -

export CLUSTER_SERVICE_ACCOUNT=$(gcloud container clusters describe ${CLUSTER-NAME} \
--location=${CONTROL_PLANE_LOCATION} \
--project=${PROJECT_ID} \
--format="value(nodeConfig.serviceAccount)")

#Bind the SA
gcloud projects add-iam-policy-binding ${PROJECT_ID}  \
--member="serviceAccount:${CLUSTER_SERVICE_ACCOUNT}" \
--role="roles/artifactregistry.serviceAgent"

#Create a GCP Bucket
terraform -chdir=gcp-rag/vector-database/terraform/cloud-storage init
terraform -chdir=gcp-rag/vector-database/terraform/cloud-storage apply \
  -var project_id=${PROJECT_ID} \
  -var region=${REGION} \
  -var cluster_prefix=${KUBERNETES_CLUSTER_PREFIX} \
  -var db_namespace=${DB_NAMESPACE}

#Upload the custom PDF file
gsutil cp gcp-rag/vector-database/documents/carbon-free-energy.pdf -p ${PROJECT_ID} gs://${PROJECT_ID}-${KUBERNETES_CLUSTER_PREFIX}-training-docs

```
To get the running pod's

Get the list of running Pod's
<img width="1170" height="138" alt="get-po" src="https://github.com/user-attachments/assets/db533943-e6bc-4db6-b375-9d99d7976fd0" />


## Accessing the Chatbot UI

External Loabbalncer created, can be accesed using the IP

```
kubectl get svc -n elastic
```

## Access chatbot

Open [http://External-IP] to access Chatbot.
<img width="935" height="641" alt="chatbot" src="https://github.com/user-attachments/assets/c56fc866-fa82-45ae-969f-c258c4e8ee16" />

