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

## Apply the Kubernetes Manifest

```
kubectl apply -f ./
```

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

