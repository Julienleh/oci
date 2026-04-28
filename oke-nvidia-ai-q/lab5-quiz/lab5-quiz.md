# Optional Quiz: NVIDIA AI-Q on Oracle Kubernetes Engine (OKE)

## Introduction

This optional lab lets you review the main ideas from the workshop and confirm your understanding of how the NVIDIA RAG Blueprint and NVIDIA AI-Q work together on Oracle Kubernetes Engine (OKE).

Estimated Time: 10 minutes

### Objectives

In this lab, you will:

- Review the key concepts from the workshop
- Test your understanding of the deployment flow
- Confirm how the RAG and AI-Q services interact

## Task 1: Complete the Quiz

1. Answer the questions below based on what you learned in the Introduction and Labs 1 through 4.

2. This quiz is optional. Use it as a quick self-check before you finish the workshop.

    ```quiz
    Q: What is the main purpose of the NVIDIA RAG Blueprint in this workshop?
    - It replaces Kubernetes with a simpler deployment model.
    * It provides document ingestion, retrieval, reranking, and grounded question answering.
    - It is used only for web search through Tavily.
    - It exists only to host the AI-Q frontend.
    > The RAG Blueprint is the document-focused foundation of the solution. It handles ingestion and retrieval so answers can be grounded in source content.

    Q: Why do you set a Tavily API key before deploying AI-Q?
    - To authenticate access to the Milvus vector database.
    - To build the Helm chart dependencies.
    * To enable web search during AI-Q research workflows.
    - To create the Kubernetes namespace for AI-Q.
    > Tavily is the web search component in this workshop. AI-Q uses it when you want research results to include current web information.

    Q: Why is Kubernetes monitoring useful during the AI-Q deployment?
    * It lets you inspect pod status, readiness, and restart behavior while services are starting.
    - It removes the need for checking deployment output from Helm.
    - It automatically generates research reports from your collections.
    - It replaces Phoenix tracing for all observability tasks.
    > Kubernetes visibility helps you confirm that the right services are healthy and makes it easier to spot problems such as stuck or restarting pods.

    Q: What is one advantage of AI-Q reusing the shared Nemotron model from the RAG environment?
    - It prevents the need to load any document collections.
    * It reduces duplicate large model deployment and can save GPU resources.
    - It allows AI-Q to run without any Kubernetes services.
    - It eliminates the need for a frontend interface.
    > Reusing a shared model is efficient. It avoids deploying another full LLM stack for the same job and helps simplify operations.

    Q: What does the `load-files.yaml` job do in Lab 4?
    - It deploys the Phoenix tracing service.
    - It creates the Tavily API key in Kubernetes.
    * It loads the default biomedical and financial datasets into the vector database.
    - It replaces the AI-Q backend container with a newer version.
    > The load job prepares sample collections so AI-Q can retrieve from them during report generation and testing.

    Q: After AI-Q generates a report, what can the learner do next in the interface?
    - Only delete the report and restart from the beginning.
    * Edit sections, request refinements, and save updated content.
    - Export the report only through Kubernetes commands.
    - Disable the frontend and continue from Cloud Shell only.
    > The workshop highlights the human-in-the-loop flow. You can review and refine individual sections instead of treating the output as fixed.
    ```

3. When you finish the questions, congratulations on completing the quiz. Revisit any related lab if you want a quick refresher.

## Conclusion

Congratulations on finishing the workshop and the optional quiz. You now have a practical understanding of how to deploy, monitor, and test NVIDIA AI-Q with the NVIDIA RAG Blueprint on Oracle Kubernetes Engine.

## Learn More

- [NVIDIA AI-Q Blueprint](https://build.nvidia.com/nvidia/aiq)
- [Oracle Kubernetes Engine product page](https://www.oracle.com/cloud/cloud-native/kubernetes-engine/)
- [Oracle Kubernetes Engine documentation](https://docs.oracle.com/en-us/iaas/Content/ContEng/home.htm)

## Acknowledgements

- **Author** - Alejandro Casas, Oracle; Anurag Kuppala, NVIDIA
- **Contributors** - Julien Lehmann
- **Last Updated By/Date** - Codex, April 2026
