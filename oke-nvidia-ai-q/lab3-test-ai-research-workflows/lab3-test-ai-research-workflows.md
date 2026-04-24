# Monitor the AI-Q Deployment and Validate Service Readiness

## Introduction

In this lab, you verify that the AI-Q deployment is starting correctly and that all required services become ready. You will use Kubernetes commands to watch pod startup, confirm final pod status, and understand how Kubernetes visibility helps you troubleshoot and operate AI workflows on Oracle Kubernetes Engine (OKE).

Estimated Time: 10 minutes

### Objectives

In this lab, you will:

- Watch the AI-Q pods start in Kubernetes
- Confirm that the AI-Q services reach a healthy running state
- Understand how Kubernetes helps monitor and troubleshoot AI deployments
- Verify that AI-Q is reusing the shared Nemotron model instead of deploying another LLM stack

### Why Kubernetes Monitoring Matters

One of the major benefits of running AI services on Kubernetes is operational visibility. Instead of treating the deployment as a black box, you can inspect pod status, startup progress, restart behavior, and service readiness directly from the cluster. This is especially useful for AI workflows, where different services such as frontends, backends, model endpoints, and tracing tools must all become healthy before the application behaves as expected.

In this workshop, Kubernetes monitoring helps you quickly answer questions such as:

- Which pods are still starting?
- Which service is not yet ready?
- Did a container restart unexpectedly?
- Is AI-Q reusing shared services correctly instead of duplicating expensive model workloads?

## Task 1: Watch AI-Q Pod Startup

After deployment, start by watching the AI-Q pods come online.

1. In Cloud Shell, run the following command:

    ```bash
    <copy>kubectl get pods -n aira --watch</copy>
    ```

2. Observe the early startup states.

    Example output during the first part of startup:

    ```text
    NAME                                         READY   STATUS              RESTARTS   AGE
    aira-aira-backend-xxxxx                      0/1     ContainerCreating   0          15s
    aira-frontend-xxxxx                          0/1     ContainerCreating   0          15s
    aira-nginx-xxxxx                             0/1     ContainerCreating   0          15s
    aira-phoenix-xxxxx                           0/1     ContainerCreating   0          15s
    ```

3. Continue watching until you see the pods move toward a healthy state, then press `Ctrl+C` to stop the live watch.

This live view is helpful because it lets you catch issues early, such as pods stuck in `ContainerCreating`, `Pending`, or `CrashLoopBackOff`.

## Task 2: Confirm Final Pod Status

Once the initial startup activity settles, confirm that the deployment completed successfully.

1. Wait 2 to 3 minutes for the services to finish starting.

2. Run the following command:

    ```bash
    <copy>kubectl get pods -n aira</copy>
    ```

3. Confirm that all AI-Q pods show `READY 1/1` and `STATUS Running`.

    Example output:

    ```text
    NAME                                         READY   STATUS    RESTARTS   AGE
    aira-aira-backend-7b8c9d5f4-xk2pm            1/1     Running   0          3m
    aira-frontend-6d7f8c9b5-jp4tn                1/1     Running   0          3m
    aira-nginx-5c8d7b6f9-mn3rl                   1/1     Running   0          3m
    aira-phoenix-847b9c5d6-kp2wq                 1/1     Running   0          3m
    ```

4. If one or more pods are not ready, pause here and investigate the affected service before continuing to the next lab.

## Task 3: Verify the Shared Model Architecture

As part of the deployment validation, confirm that AI-Q is using the shared model architecture described in the workshop.

1. Review the AI-Q pod list you just captured.

2. Notice that no additional NIM LLM pods are created inside the `aira` namespace.

3. Confirm the intended design:

    - AI-Q reuses the Nemotron Super 49B model already deployed in the RAG environment
    - AI-Q does not deploy a second large language model stack for the same purpose

This shared-service pattern is one of the practical advantages of Kubernetes-based deployments. It allows multiple application components to reuse common services, which can reduce GPU consumption, simplify operations, and make the overall platform easier to monitor.

## Learn More

- [Oracle Kubernetes Engine documentation](https://docs.oracle.com/en-us/iaas/Content/ContEng/home.htm)
- [NVIDIA AI-Q Blueprint](https://build.nvidia.com/nvidia/aiq)
- [NVIDIA NIM microservices](https://www.nvidia.com/en-us/ai-data-science/products/nim-microservices/)

## Acknowledgements

- **Author** - Alejandro Casas, Oracle; Anurag Kuppala, NVIDIA
- **Contributors** - Julien Lehmann
- **Last Updated By/Date** - Codex, April 2026
