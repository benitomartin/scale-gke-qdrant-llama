# Q&A Pipeline Deployment on GKE for Scalability with LlamaIndex and Qdrant". 🚀

<p align="center">
  <img width="976" alt="aws" src="https://github.com/benitomartin/mlops-aws-insurance/assets/116911431/4bfeb7ce-b151-4042-8cf6-c83299a2765a">
</p>

This repository contains a full Q&A pipeline using the LlamaIndex framework, Qdrant as a vector database, and deployment on Google Kubernetes Engine (GKE) using a FastAPI app and Dockerfile. Python files from my repositories are loaded into the vector database, and the FastAPI app processes requests. The main goal is to provide fast access to your own code, enabling reuse of functions.

For detailed project descriptions, refer to this [Medium article](XXX).

Main Steps

- **Data Ingestion**: Load data from GitHub repositories.
- **Indexing**: Use SentenceSplitter for indexing in nodes.
- **Embedding**: Implement FastEmbedEmbedding.
- **Vector Store**: Use Qdrant for inserting metadata.
- **Query Retrieval**: Implement RetrieverQueryEngine.
- **FastAPI and GKE**: Handle requests via the FastAPI app deployed on GKE.
- **Streamlit**: UI component.
  
Feel free to ⭐ and clone this repo 😉

## Tech Stack

![Visual Studio Code](https://img.shields.io/badge/Visual%20Studio%20Code-0078d7.svg?style=for-the-badge&logo=visual-studio-code&logoColor=white)
![Jupyter Notebook](https://img.shields.io/badge/jupyter-%23FA0F00.svg?style=for-the-badge&logo=jupyter&logoColor=white)
![Python](https://img.shields.io/badge/python-3670A0?style=for-the-badge&logo=python&logoColor=ffdd54)
![OpenAI](https://img.shields.io/badge/OpenAI-74aa9c?style=for-the-badge&logo=openai&logoColor=white)
![Anaconda](https://img.shields.io/badge/Anaconda-%2344A833.svg?style=for-the-badge&logo=anaconda&logoColor=white)
![Linux](https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=white)
![Ubuntu](https://img.shields.io/badge/Ubuntu-E95420?style=for-the-badge&logo=ubuntu&logoColor=white)
![Google Cloud](https://img.shields.io/badge/GoogleCloud-%234285F4.svg?style=for-the-badge&logo=google-cloud&logoColor=white)
![Kubernetes](https://img.shields.io/badge/kubernetes-%23326ce5.svg?style=for-the-badge&logo=kubernetes&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-005571?style=for-the-badge&logo=fastapi)
![Git](https://img.shields.io/badge/git-%23F05033.svg?style=for-the-badge&logo=git&logoColor=white)
![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/github%20actions-%232671E5.svg?style=for-the-badge&logo=githubactions&logoColor=white)
![Streamlit](https://img.shields.io/badge/Streamlit-FF4B4B?style=for-the-badge&logo=Streamlit&logoColor=white)


## Project Structure

The project has been structured with the following files:

- `.github/workflows:` CI/CD pipelines
- `tests`: unittest
- `Dockerfile:`Dockerfile
- `Makefile`: install requirements, formating, linting, testing and clean up
- `app.py:` FastAPI
- `pyproject.toml`: linting and formatting using ruff
- `create_qdrant_collection.py:` script to create the collection in Qdrant
- `deploy-gke.yaml:` deployment function
- `kustomization.yaml:` kustomize deployment function
- `requirements.txt:` project requirements


## Project Set Up

The Python version used for this project is Python 3.10. You can follow along the medium article.

1. Clone the repo (or download it as a zip file):

   ```bash
   git clone https://github.com/benitomartin/rag-aws-qdrant.git
   ```

2. Create the virtual environment named `main-env` using Conda with Python version 3.10:

   ```bash
   conda create -n main-env python=3.10
   conda activate main-env
   ```
   
3. Execute the `Makefile` script and install the project dependencies included in the requirements.txt:

    ```bash
    pip install -r requirements.txt

    or
 
    make install
    ```

4. You can test the app locally running:

   ```bash
   uvicorn app:app --host 0.0.0.0 --port 8000
   ```

   then go to one of these addresses
   
   http://localhost:8000/docs
   http://127.0.0.1:8000/docs

5. Create **GCP Account**, project, service account key, and activate GKE API

6. Make sure the `.env` file is complete:

   ```bash
   OPENAI_API_KEY=
   QDRANT_API_KEY=
   QDRANT_URL=
   COLLECTION_NAME=
   ACCESS_TOKEN=
   GITHUB_USERNAME=
   ```

7. Add the following secrets into github:
   ```bash
   OPENAI_API_KEY
   QDRANT_API_KEY
   QDRANT_URL
    COLLECTION_NAME
   GKE_SA_KEY
   GKE_PROJECT # PROJECT_ID
   ```

8. Be sure to authenticate in GCP:
    ```bash
    gcloud auth login
    ```

    ```bash
    gcloud config set project PROJECT_ID
    ```

9. Create Kubernetes Cluster

    ```bash
    gcloud container clusters create llama-gke-cluster \
        --zone=europe-west6-a \
        --num-nodes=5 \
        --enable-autoscaling \
        --min-nodes=1 \
        --max-nodes=10 \
        --machine-type=n1-standard-4 \
        --enable-vertical-pod-autoscaling
    ```

    after creation check the nodes
    
    ```bash
    kubectl get nodes
    ```

10. Push the GitHub Actions workflows to start the deployment

11. Verify Kubernetes is running after deployment

    ```bash
    kubectl get po 
    kubectl get svc 
    ```

    <p align="center">
    <img width="940" alt="Pods Running" src="https://github.com/benitomartin/mlops-car-prices/assets/116911431/d4dee27d-383f-4375-9a21-29996a5b5089">
    </p>

    under svc the external ip is the endpoint (34.65.3.225), that can be added in the streamlit app

    <p align="center">
    <img width="767" alt="lambda-gke" src="https://github.com/benitomartin/mlops-car-prices/assets/116911431/b4a7e10c-52f9-4ca2-ade3-f2136ff6bbdf">
    </p>

    http://34.65.191.211:8000

12. Check some pods and logs

    ```bash
    kubectl logs llama-app-gke-deploy-79bf48d7d8-4b77z
    kubectl describe pod llama-app-gke-deploy-79bf48d7d8-4b77z
    ```

13. Clean up to avoid costs deleting the cluster and the docker image

    ```bash
    gcloud container clusters delete app-llama-gke-cluster --zone=europe-west6-a
    kubectl delete deployment llama-app-gke-deploy
    ```

## Streamlit UI

Run the streamlit app adding the endpoint url that you get after deployment:

   ```bash
   streamlit run streamlit_app.py
   ```

    <p align="center">
    <img width="767" alt="lambda-gke" src="https://github.com/benitomartin/mlops-car-prices/assets/116911431/b4a7e10c-52f9-4ca2-ade3-f2136ff6bbdf">
    </p>
