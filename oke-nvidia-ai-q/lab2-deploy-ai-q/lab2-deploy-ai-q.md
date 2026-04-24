# Deploy the NVIDIA AI-Q Research Assistant on Oracle Kubernetes Engine (OKE)

## Introduction

In this lab, you deploy the NVIDIA AI-Q Research Assistant and connect it to the services you already prepared for the workshop. You will set the Tavily API key for web search, build the Helm chart dependencies, and install AI-Q so it can work with the shared Nemotron model and the RAG services.

Estimated Time: 20 minutes

### Objectives

In this lab, you will:

- Set the Tavily API key used for web search
- Prepare the AI-Q Helm chart dependencies
- Deploy the AI-Q Research Assistant on OKE
- Confirm that the deployment completes successfully

### About NVIDIA AI-Q

NVIDIA AI-Q is a research assistant framework that can gather information from multiple sources, reason over that information, and assemble structured outputs such as summaries and research reports. In this workshop, AI-Q reuses the shared Nemotron model and integrates with the RAG services so the platform can move beyond document question answering into broader research workflows.

### Prerequisites

This lab assumes you have:

- Completed the setup from the previous lab
- A valid Tavily API key
- A valid NVIDIA NGC API key already available in your shell session
- Access to the AI-Q Helm chart directory in your environment

## Task 1: Set the Tavily API Key

Before deploying AI-Q, set the Tavily API key that AI-Q uses for web search requests.

1. Get your Tavily API key if you have not done so already.

    You can create one at [tavily.com](https://tavily.com/).

2. Export the key in your shell session.

    Replace `<YOUR_TAVILY_API_KEY>` with your actual key.

    ```bash
    <copy>export TAVILY_API_KEY="<YOUR_TAVILY_API_KEY>"</copy>
    ```

3. Verify that the variable is set.

    ```bash
    <copy>echo "Tavily: $TAVILY_API_KEY"</copy>
    ```

4. Confirm that your NVIDIA NGC API key is still available in the same shell session.

    The deployment later in this lab assumes the NGC key remains set from the earlier steps.

## Task 2: Prepare the AI-Q Helm Chart

Move into the AI-Q chart directory and build its dependencies before installation.

1. Navigate to the AI-Q chart directory.

    ```bash
    <copy>cd ~/aiq-aira/</copy>
    ```

2. Build the Helm dependencies.

    ```bash
    <copy>helm dependency build</copy>
    ```

3. Wait for the dependency build to complete before you continue to deployment.

## Task 3: Deploy AI-Q

Now deploy the AI-Q Research Assistant and point it to the shared services already used in the workshop environment.

1. Run the Helm install command below:

    ```bash
    <copy>helm install aira ~/aiq-aira/ \
      --set imagePullSecret.create=false \
      --set imagePullSecret.name=ngc-secret \
      --set ngcApiSecret.create=false \
      --set ngcApiSecret.name=ngc-api \
      --set nginx.nginxImage.name=docker.io/nginx \
      --set phoenix.image.repository=docker.io/arizephoenix/phoenix \
      --set config.tavily_api_key=$TAVILY_API_KEY \
      --set config.instruct_model_name="nvidia/llama-3.3-nemotron-super-49b-v1" \
      --set config.instruct_base_url="http://nim-llm:8000/v1" \
      --set config.nemotron_model_name="nvidia/llama-3.3-nemotron-super-49b-v1" \
      --set config.nemotron_base_url="http://nim-llm:8000/v1" \
      --set config.rag_url="http://rag-server:8081" \
      --set config.rag_ingest_url="http://ingestor-server:8082" \
      --set config.milvus_host="milvus" \
      --set config.milvus_port="19530" \
      --set nim-llm.enabled=false \
      --set frontend.enabled=true</copy>
    ```

2. Wait for Helm to report a successful deployment.

    Example output:

    ```text
    NAME: aira
    LAST DEPLOYED: Wed Oct 8 19:30:00 2025
    NAMESPACE: aira
    STATUS: deployed
    REVISION: 1
    ```

3. Review the command configuration at a high level.

    This deployment:

    - reuses existing NGC secrets instead of creating new ones
    - points AI-Q to the shared Nemotron endpoint
    - connects AI-Q to the RAG server and ingestion services
    - enables the frontend for learner testing

## Task 4: Confirm the Deployment Result

After Helm reports success, confirm that the deployment completed as expected.

1. Review the namespace and release name returned by Helm.

    You should see:

    - release name: `aira`
    - namespace: `aira`
    - status: `deployed`

2. If Helm does not return `STATUS: deployed`, stop here and troubleshoot before continuing to later labs.

3. Once the deployment succeeds, you are ready to continue with validation and testing in the next lab.

## Learn More

- [NVIDIA AI-Q Blueprint](https://build.nvidia.com/nvidia/aiq)
- [NVIDIA technical blog on AI-Q and enterprise data](https://developer.nvidia.com/blog/chat-with-your-enterprise-data-through-open-source-ai-q-nvidia-blueprint/)
- [Oracle Kubernetes Engine documentation](https://docs.oracle.com/en-us/iaas/Content/ContEng/home.htm)

## Acknowledgements

- **Author** - Alejandro Casas, Oracle; Anurag Kuppala, NVIDIA
- **Contributors** - Julien Lehmann
- **Last Updated By/Date** - Codex, April 2026
