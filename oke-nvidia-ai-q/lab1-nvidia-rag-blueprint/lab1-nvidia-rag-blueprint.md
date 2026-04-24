# Deploy the NVIDIA RAG Blueprint on Oracle Kubernetes Engine (OKE)

## Introduction

In this lab, you deploy the NVIDIA RAG Blueprint on Oracle Kubernetes Engine (OKE) and confirm that the core services start correctly. You then open the RAG Playground, upload a sample document, and verify that the system can answer questions with grounded citations.

Estimated Time: 30 minutes

### Objectives

In this lab, you will:

- Verify that your OKE worker nodes are available
- Configure access to the NVIDIA Helm repository
- Deploy the NVIDIA RAG Blueprint with the required services
- Monitor the deployment until the main pods are ready
- Test the RAG Playground with a sample Oracle document

### About the RAG Blueprint

The NVIDIA RAG Blueprint is a reference deployment for retrieval-augmented generation. It combines document ingestion, vector search, reranking, and large language model inference so users can ask questions about enterprise documents and receive responses grounded in source content.

This lab focuses on the RAG portion of the solution. Later labs can build on this foundation with broader research workflows.

### Prerequisites

This lab assumes you have:

- Access to an OKE cluster with the required NVIDIA GPU nodes
- `kubectl` access to that cluster
- A valid NVIDIA NGC API key
- Cloud Shell or another shell environment with Helm installed

## Task 1: Verify Access to the Cluster

Before you deploy the RAG Blueprint, confirm that your cluster nodes are available and ready.

1. Open Cloud Shell.

2. Run the following command:

    ```bash
    <copy>kubectl get nodes</copy>
    ```

3. Confirm that your worker node shows a `Ready` status.

    Example output:

    ```text
    NAME         STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                  KERNEL-VERSION                    CONTAINER-RUNTIME
    10.0.10.116  Ready    node    18h   v1.30.1   10.0.10.116   <none>        Oracle Linux Server 8.10  5.15.0-210.163.7.el8uek.x86_64   cri-o://1.30.5
    ```

If the node is not ready, stop here and resolve cluster access before continuing.

## Task 2: Configure the NVIDIA Helm Repository

The RAG Blueprint is deployed from the NVIDIA Helm repository, so you must first set your API key and refresh the chart metadata.

1. Export your NVIDIA API key in Cloud Shell.

    Replace `<YOUR_NGC_API_KEY>` with your actual key.

    ```bash
    <copy>export NGC_API_KEY="<YOUR_NGC_API_KEY>"</copy>
    ```

2. Add the NVIDIA Helm repository.

    ```bash
    <copy>helm repo add nvidia https://helm.ngc.nvidia.com/nim \
      --username '$oauthtoken' \
      --password "$NGC_API_KEY"</copy>
    ```

3. Update the repository.

    ```bash
    <copy>helm repo update nvidia</copy>
    ```

4. Review the output and confirm the repository update completed successfully.

    Example output:

    ```text
    "nvidia" already exists with the same configuration, skipping
    Hang tight while we grab the latest from your chart repositories...
    ...Successfully got an update from the "nvidia" chart repository
    Update Complete. Happy Helming!
    ```

## Task 3: Deploy the RAG Blueprint

This step creates the `rag` namespace and deploys the RAG Blueprint with the required inference, embedding, reranking, ingestion, and frontend services.

1. Run the Helm install command below:

    ```bash
    <copy>helm install rag https://helm.ngc.nvidia.com/nvidia/blueprint/charts/nvidia-blueprint-rag-v2.2.0.tgz \
      --username '$oauthtoken' \
      --password "${NGC_API_KEY}" \
      --set imagePullSecret.password=$NGC_API_KEY \
      --set ngcApiSecret.password=$NGC_API_KEY \
      --set nim-llm.enabled=true \
      --set nim-llm.image.repository="nvcr.io/nim/nvidia/llama-3.3-nemotron-super-49b-v1" \
      --set nim-llm.image.tag="1.8.5" \
      --set nim-llm.resources.limits."nvidia\.com/gpu"="4" \
      --set nim-llm.resources.requests."nvidia\.com/gpu"="4" \
      --set nvidia-nim-llama-32-nv-embedqa-1b-v2.enabled=true \
      --set nvidia-nim-llama-32-nv-embedqa-1b-v2.image.tag="1.9.0" \
      --set nvidia-nim-llama-32-nv-embedqa-1b-v2.resources.limits."nvidia\.com/gpu"=1 \
      --set nvidia-nim-llama-32-nv-embedqa-1b-v2.resources.requests."nvidia\.com/gpu"=1 \
      --set text-reranking-nim.enabled=true \
      --set text-reranking-nim.image.tag="1.7.0" \
      --set text-reranking-nim.resources.limits."nvidia\.com/gpu"=1 \
      --set text-reranking-nim.resources.requests."nvidia\.com/gpu"=1 \
      --set ingestor-server.enabled=true \
      --set ingestor-server.envVars.APP_VECTORSTORE_ENABLEGPUINDEX="False" \
      --set ingestor-server.envVars.APP_VECTORSTORE_ENABLEGPUSEARCH="False" \
      --set ingestor-server.envVars.APP_NVINGEST_EXTRACTTEXT="True" \
      --set ingestor-server.envVars.APP_NVINGEST_EXTRACTTABLES="False" \
      --set ingestor-server.envVars.APP_NVINGEST_EXTRACTCHARTS="False" \
      --set ingestor-server.envVars.APP_NVINGEST_EXTRACTIMAGES="False" \
      --set ingestor-server.envVars.APP_NVINGEST_EXTRACTINFOGRAPHICS="False" \
      --set ingestor-server.envVars.APP_NVINGEST_ENABLEPDFSPLITTER="True" \
      --set ingestor-server.envVars.APP_NVINGEST_CHUNKSIZE="1024" \
      --set ingestor-server.envVars.APP_NVINGEST_CHUNKOVERLAP="150" \
      --set ingestor-server.envVars.NV_INGEST_FILES_PER_BATCH="32" \
      --set ingestor-server.envVars.NV_INGEST_CONCURRENT_BATCHES="8" \
      --set ingestor-server.envVars.ENABLE_MINIO_BULK_UPLOAD="True" \
      --set ingestor-server.envVars.NV_INGEST_DEFAULT_TIMEOUT_MS="5000" \
      --set ingestor-server.nv-ingest.envVars.INGEST_DISABLE_DYNAMIC_SCALING="True" \
      --set ingestor-server.nv-ingest.envVars.MAX_INGEST_PROCESS_WORKERS="32" \
      --set ingestor-server.nv-ingest.envVars.NV_INGEST_MAX_UTIL="80" \
      --set ingestor-server.nv-ingest.envVars.INGEST_EDGE_BUFFER_SIZE="128" \
      --set ingestor-server.nv-ingest.milvus.image.all.repository="docker.io/milvusdb/milvus" \
      --set ingestor-server.nv-ingest.milvus.image.all.tag="v2.5.3" \
      --set ingestor-server.nv-ingest.milvus.image.tools.repository="docker.io/milvusdb/milvus-config-tool" \
      --set ingestor-server.nv-ingest.milvus.minio.image.repository="docker.io/minio/minio" \
      --set ingestor-server.nv-ingest.milvus.standalone.resources.limits."nvidia\.com/gpu"=0 \
      --set ingestor-server.nv-ingest.nemoretriever-page-elements-v2.deployed=true \
      --set ingestor-server.nv-ingest.nemoretriever-page-elements-v2.image.tag="1.4.0" \
      --set ingestor-server.nv-ingest.nemoretriever-page-elements-v2.resources.limits."nvidia\.com/gpu"=1 \
      --set ingestor-server.nv-ingest.nemoretriever-page-elements-v2.resources.requests."nvidia\.com/gpu"=1 \
      --set ingestor-server.nv-ingest.nemoretriever-graphic-elements-v1.deployed=false \
      --set ingestor-server.nv-ingest.nemoretriever-table-structure-v1.deployed=false \
      --set ingestor-server.nv-ingest.paddleocr-nim.deployed=false \
      --set ingestor-server.nv-ingest.redis.image.repository="redis" \
      --set ingestor-server.nv-ingest.redis.image.tag="8.2.1" \
      --set envVars.ENABLE_RERANKER="True" \
      --set frontend.enabled=true</copy>
    ```

2. Wait for Helm to return a successful deployment message.

    Example output:

    ```text
    NAME: rag
    LAST DEPLOYED: Wed Oct 8 18:56:23 2025
    NAMESPACE: rag
    STATUS: deployed
    REVISION: 1
    ```

## Task 4: Monitor the Deployment

After deployment, confirm that the required services are starting correctly.

1. Check the pods:

    ```bash
    <copy>kubectl get pods -n rag</copy>
    ```

2. Wait until the main pods are running.

    Example output after several minutes:

    ```text
    NAME                                                        READY   STATUS    RESTARTS   AGE
    ingestor-server-79484c759c-2f7w9                            1/1     Running   0          10m
    milvus-standalone-594df6565-42rsj                           1/1     Running   0          10m
    rag-etcd-0                                                  1/1     Running   0          10m
    rag-frontend-547bc85495-lz9lc                               1/1     Running   0          10m
    rag-minio-f88fb7fd4-7n88n                                   1/1     Running   0          10m
    rag-nemoretriever-page-elements-v2-699b99c566-m5zql         1/1     Running   0          10m
    rag-nim-llm-0                                               0/1     Running   0          10m
    rag-nv-ingest-85b544688c-kgq47                              1/1     Running   0          10m
    rag-nvidia-nim-llama-32-nv-embedqa-1b-v2-86797758f4-dnf8s   1/1     Running   0          10m
    rag-redis-master-0                                          1/1     Running   0          10m
    rag-redis-replicas-0                                        1/1     Running   0          10m
    rag-server-b9d44657b-7c8gb                                  1/1     Running   0          10m
    rag-text-reranking-nim-5868b47978-dvvzf                     1/1     Running   0          10m
    ```

3. Continue only after the pods are stable.

    Note: `rag-nim-llm-0` can take 10 to 15 minutes to build its TensorRT engines. Wait until it shows `READY 1/1` before moving on.

## Task 5: Open the RAG Playground

Now verify that the frontend is reachable.

1. Print the frontend URL:

    ```bash
    <copy>echo "http://$RAG_DOMAIN"</copy>
    ```

2. Open the displayed URL in your browser.

3. Confirm that the RAG Playground loads successfully.

If the page does not open, review the frontend service and ingress configuration before continuing.

## Task 6: Test the RAG System with a Sample Document

Use a sample Oracle document to confirm that ingestion, retrieval, and answer generation are working.

1. Download the [Oracle OCI Supercluster PDF](https://www.oracle.com/a/ocom/docs/cloud/accelerate-ai-with-oci-supercluster.pdf) to your local machine.

    Open the document link in your browser, then save the PDF locally so you can upload it into the RAG Playground.

2. Create a new collection in the RAG Playground.

    Use these values:

    - Collection name: `OCI Documentation`

3. Upload the PDF into that collection.

    Wait about 30 to 60 seconds for document processing to complete.

4. Run a few sample questions.

    Try prompts such as:

    - `What is OCI Supercluster and what makes it unique?`
    - `How many NVIDIA GPUs can OCI Supercluster scale to?`
    - `What are the main AI use cases supported on OCI?`
    - `How does Oracle partner with NVIDIA for AI workloads?`

5. Review the results carefully.

    Confirm that the RAG system returns:

    - grounded answers based on the uploaded document
    - page-level citations or source references
    - evidence tied back to the source material

## Learn More

- [NVIDIA RAG Blueprint documentation](https://build.nvidia.com/)
- [NVIDIA NIM microservices](https://www.nvidia.com/en-us/ai-data-science/products/nim-microservices/)
- [Oracle Kubernetes Engine documentation](https://docs.oracle.com/en-us/iaas/Content/ContEng/home.htm)

## Acknowledgements

- **Author** - Alejandro Casas, Oracle; Anurag Kuppala, NVIDIA
- **Contributors** - Julien Lehmann
- **Last Updated By/Date** - Codex, April 2026
