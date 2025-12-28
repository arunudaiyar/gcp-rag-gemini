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


## Accessing the Chatbot UI

External Loabbalncer created, can be accesed using the IP

```
kubectl get svc -n elastic
```

## Access chatbot

Open [http://localhost:8080](http://localhost:8080) to access Open WebUI.

<img width="1366" height="645" alt="openweb-llama" src="https://github.com/user-attachments/assets/354127e4-4ffb-4503-986d-c6103da81f47" />

