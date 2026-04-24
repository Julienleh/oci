# Understand the NVIDIA RAG Blueprint and AI-Q on Oracle Kubernetes Engine (OKE)

## Introduction

This lab introduces the solution you will deploy in the rest of the workshop. You will review how the NVIDIA RAG Blueprint and NVIDIA AI-Q work together on Oracle Kubernetes Engine (OKE), what each service does, and what resources you need before deployment.

Estimated Time: 15 minutes

### About NVIDIA AI-Q

NVIDIA AI-Q is a research assistant framework that helps AI agents gather information from multiple sources, reason over that information, and produce structured outputs such as summaries and reports. In this workshop, AI-Q complements the RAG Blueprint by extending document question answering into broader research workflows that can also include web search and report generation.

### Objectives

In this lab, you will:

- Understand the purpose of the NVIDIA RAG Blueprint and NVIDIA AI-Q
- Review the main components used in this workshop
- Confirm the GPU and API requirements for the deployment
- Prepare for the hands-on deployment labs that follow

### Prerequisites

This lab assumes you have:

- Access to an OCI environment where you can deploy Oracle Kubernetes Engine (OKE)
- An NVIDIA API key
- A Tavily API key for web search integration
- Access to NVIDIA GPU resources sized for the full workshop deployment

## Task 1: Review the Solution Architecture

The workshop combines two NVIDIA blueprints into one platform running on OKE:

- The **NVIDIA RAG Blueprint** provides document ingestion, retrieval, reranking, and grounded question answering.
- **NVIDIA AI-Q Research Assistant** builds on top of those capabilities to gather information from multiple sources and generate longer-form research outputs.

Together, they create a learner-friendly reference architecture for:

- document question answering
- research report generation
- web-assisted investigation
- human review and refinement of AI-generated results

This architecture is useful when you want a system that can both answer questions about private content and expand the response with outside research when needed.

## Task 2: Understand the Main Components

This workshop uses several services that each play a different role in the solution:

1. **Nemotron Super 49B**

    This large language model handles advanced reasoning, question answering, and report synthesis. In this workshop, it is shared across both the RAG and AI-Q portions of the solution.

2. **NeMo Retriever Embedding 1B**

    This model converts document content into embeddings so the platform can search and retrieve relevant context.

3. **NeMo Retriever Reranking 1B**

    This model improves answer quality by reordering retrieved results before they are passed to the language model.

4. **Page Elements NIM**

    This service extracts content from PDF documents so the system can ingest and understand document structure more effectively.

5. **Milvus Vector Database**

    Milvus stores the vector embeddings used for retrieval. It is the search backbone for the RAG workflow.

6. **Tavily API**

    Tavily adds real-time web research, which allows AI-Q to combine private enterprise knowledge with current public information.

7. **Phoenix Tracing**

    Phoenix provides observability for the AI workflows so you can inspect requests, debug behavior, and understand how the research pipeline performs.

## Task 3: Review Deployment Requirements

Before you begin the hands-on deployment, review the resource profile for the complete solution:

| Component | Blueprint | GPU Count | GPU Memory | Purpose |
| --- | --- | --- | --- | --- |
| Nemotron Super 49B LLM | RAG + AI-Q | 4 | 40 GB each | Advanced reasoning, question answering, and report generation |
| Embedding Model | RAG | 1 | 40 GB | Text embeddings |
| Reranking Model | RAG | 1 | 40 GB | Result reranking |
| Page Elements NIM | RAG | 1 | 40 GB | PDF text extraction |
| Milvus Vector DB | RAG | 0 | CPU | Vector storage |
| **Total Used** | **Full system** | **7** | **40 GB each** | **Combined deployment footprint** |
| **Available** | **Spare capacity** | **1** | **40 GB** | **Optional headroom for other workloads** |

You also need these external credentials:

- **NVIDIA API Key** for accessing NVIDIA-hosted assets and services
- **Tavily API Key** for web search integration

Review or obtain them here:

- [Create an NVIDIA API key](https://nvdam.widen.net/s/kfshg7fpsr/create-build-account-and-api-key-4)
- [Create a Tavily API key](https://tavily.com/)

## Task 4: What You Will Build in This Workshop

In the next labs, you will move from architecture review to deployment and testing. By the end of the workshop, you will have:

- deployed the NVIDIA RAG Blueprint on OKE
- deployed NVIDIA AI-Q Research Assistant on the same platform
- connected shared model services across both blueprints
- enabled web search for broader research workflows
- loaded sample document collections
- generated research outputs and reviewed system behavior

This lab gives you the context for why those steps matter before you begin the actual deployment work.

## Learn More

- [NVIDIA AI-Q Blueprint](https://build.nvidia.com/nvidia/aiq)
- [NVIDIA technical blog on AI-Q and enterprise data](https://developer.nvidia.com/blog/chat-with-your-enterprise-data-through-open-source-ai-q-nvidia-blueprint/)
- [NVIDIA NIM microservices](https://www.nvidia.com/en-us/ai-data-science/products/nim-microservices/)
- [Llama Nemotron Super 49B](https://build.nvidia.com/nvidia/llama-3_3-nemotron-super-49b-v1)

## Acknowledgements

- **Author** - Alejandro Casas, Oracle; Anurag Kuppala, NVIDIA
- **Contributors** - Julien Lehmann
- **Last Updated By/Date** - Codex, April 2026
