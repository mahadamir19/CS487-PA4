<div align="center">

# PA4 Submission: TaskFlow Pipeline

<img alt="GitHub only" src="https://img.shields.io/badge/Submit-GitHub%20URL%20Only-10b981?style=for-the-badge">
<img alt="Total points" src="https://img.shields.io/badge/Total-100%20points-7c3aed?style=for-the-badge">

</div>

<div style="background:#f5f3ff;color:#111827;border-left:6px solid #6330bc;padding:14px 18px;border-radius:10px;margin:18px 0;">
Copy this file to <code style="color:#111827;background:#ddd6fe;padding:2px 4px;border-radius:4px;">SUBMISSION.md</code>. Put every screenshot in <code style="color:#111827;background:#ddd6fe;padding:2px 4px;border-radius:4px;">docs/</code>, embed it under the correct task, and write a short description below each image explaining what it proves. The grader should not need any file outside this repository.
</div>

## Student Information

| Field | Value |
|---|---|
| Name | Mahad Amir |
| Roll Number | 27100289 |
| GitHub Repository URL | https://github.com/mahadamir19/CS487-PA4 |
| Resource Group | `rg-sp26-27100289` |
| Assigned Region | `ukwest` |

## Evidence Rules

- Use relative image paths, for example: `![AKS nodes](docs/aks-nodes.png)`.
- Every image must have a 1-3 sentence description below it.
- Azure Portal screenshots must show the resource name and enough page context to identify the service.
- CLI screenshots must show the command and output.
- Mask secrets such as function keys, ACR passwords, and storage connection strings.


## Task 1: App Service Web App (15 points)

### Evidence 1.1: Forked Repository

![Forked Repo](docs/forked-github-repo.png)

Description: This screenshot proves the CS487-PA4 starter repository was successfully forked to my personal GitHub account, which now acts as the central source code repository and deployment trigger for the Azure Web App

### Evidence 1.2: App Service Overview

![Web App Overview](docs/web_app_overview.png)

Resource group: rg-sp26-27100289

Region: UK West

Runtime: Node 22 LTS (Node 20 LTS was not available on the portal, I searched it but it did not appear in the search results).

Public URL: pa4-27100289.azurewebsites.net

### Evidence 1.3: Deployment Center / GitHub Actions

![Deployment Centre](docs/deployment_centre.png)

Description: This illustrates the Continuous Deployment (CI/CD) configuration linking the Azure Web App to the main branch of my GitHub fork. It demonstrates the use of a GitHub Actions workflow, authenticated via a publish profile, to automatically build the webapp/ directory and deploy the code to Azure

### Evidence 1.4: Live Web UI

![Live Web UI](docs/web_app_page.png)

Description: This screenshot captures the live TaskFlow frontend accessible over HTTPS in a browser, confirming that the Node.js application was successfully built and is being served by the Azure App Service

### Evidence 1.5: App Settings

![App Settings](docs/app_settings.png)

Description: This verifies the configuration of the FUNCTION_START_URL and FUNCTION_STATUS_URL environment variables within the Azure Portal

---

## Task 2: Azure Container Registry (15 points)

### Evidence 2.1: ACR Overview

![ACR Overview](docs/acr_overview.png)

Description: SKU: Basic and Resource group: rg-sp26-27100289

### Evidence 2.2: Docker Builds

![Validate Image](docs/validate_api_image.png)
![Report Image](docs/report_job_image.png)
![Func Image](docs/function_app_image.png)

Description: The validate-api:latest image was built from the ./validate-api directory, the report-job:latest image from the ./report-job directory, and the func-app:latest image from the ./function-app directory

### Evidence 2.3: Testing validate api

![Test](docs/validator_test.png)

Description: The screenshot shows a curl POST request sent to localhost:8080/validate and the container successfully returning the expected JSON response ({"valid":true}), proving the application logic functions correctly before cloud deployment

### Evidence 2.4: Pushing images

![Push](docs/validate_report_push.png)
![Func Push](docs/function_app_push.png)

Description: These screenshots show the local images being successfully tagged with the pa427100289.azurecr.io login server prefix and pushed to the cloud using the docker push command

### Evidence 2.5: ACR Repositories

![Verification](docs/verification.png)

Description: This screenshot confirms that all three required container repositories—validate-api:v1, report-job:v1, and func-app:v1—were successfully published to Azure

---

## Task 3: Durable Function Implementation (12 points)

### Evidence 3.1: Completed Function Code

[function_app.py](function-app/function_app.py)

Description: This file contains the complete Durable Function logic, demonstrating the sequential chaining of activities. The orchestrator uses the yield keyword to pause and checkpoint execution while calling validate_activity, disrupts the workflow if the order is rejected, and only proceeds to trigger report_activity if the validation is successful.

### Evidence 3.2: Local Function Handler Listing

TODO: ![Func Start](docs/func_start.png)

Description: This screenshot verifies that the local Azure Functions Core Tools successfully compiled the Python code and registered all four required Durable Function handlers (the HTTP starter, the orchestrator, and the two activity triggers) without any binding errors.

---

## Task 4: Function App Container Deployment (8 points)

![error](docs/error.png)
There was a naming conflict for the function app so I named to to pa4-func-27100289

### Evidence 4.1: Function App Container Configuration

![error](container/container_image.png)

Description: TODO: Function app name: pa4-func-27100289 and image URI: pa427100289.azurecr.io/func-app:v1

### Evidence 4.2: Functions List

![functions](docs/functions_list.png)

Description: This screenshot confirms that the container deployed successfully and the Azure Functions runtime recognized the four required Durable Function components: http_starter, my_orchestrator, validate_activity, and report_activity

### Evidence 4.3: Orchestration Smoke Test

![curl](docs/curl_output.png)

Description: The returned id and statusQueryGetUri prove that the http_starter function successfully received the curl POST request, authenticated using the function key, and correctly initialized a new instance of the Durable Orchestrator (my_orchestrator). It also confirms that the Function App is properly connected to its underlying Azure Storage account to track orchestration state.

### Evidence 4.4: Expected Failed Status Before Downstream Wiring

![failure](docs/uri_json.png)

Description: This failure is expected at this stage because the orchestration successfully started and moved to the first activity (validate_activity), which attempts to make an HTTP call to the VALIDATE_URL. Since the Azure Kubernetes Service (AKS) validator microservice is not deployed and wired up until Task 5, the network call fails, causing the orchestration to correctly checkpoint as Failed.

---

## Task 5: AKS Validator (15 points)

### Evidence 5.1: Kubernetes Nodes

![nodes](docs/get_nodes.png)

Description: This screenshot shows the kubectl get nodes output, confirming that the AKS cluster is successfully provisioned and the single Standard_B2s worker node is authenticated and in a Ready state.

### Evidence 5.2: Kubernetes Pods

![pods](docs/get_pods.png)

Description: The kubectl get pods output confirms that the validate-api deployment successfully pulled the container image from the Azure Container Registry. The pod is now scheduled and actively Running on the worker node.

### Evidence 5.3: Kubernetes Service

![service](get_service.png)

Description: The kubectl get service output demonstrates that the Azure LoadBalancer successfully provisioned a public IP address (51.140.204.239) for the validate-service. This exposes the internal validator API on port 8080 to the public internet so it can receive HTTP traffic.

### Evidence 5.4: Validator API Tests

![health](docs/health.png)
![checks](docs/validity_checks.png)

Description: These curl tests verify the API's logic over the internet. The accepted path (O-1001) correctly returns {"valid": true} because the requested quantity (2) is valid. The rejected path (O-1002) correctly returns {"valid": false} and specifies the reason, because the item quantity (999) triggers the validation rule that rejects orders where qty > 100

### Evidence 5.5: Function App `VALIDATE_URL`

![func](docs/func_app_settings.png)

Description: This shows the Function App's configuration updated with the VALIDATE_URL. The Durable Function's validate_activity uses this exact endpoint (http://51.140.204.239:8080/validate) to securely route the order payload from the orchestrator to the AKS cluster for validation during every pipeline run

---

## Task 6: ACI Report Job (15 points)

### Evidence 6.1: Blob Container

![container](docs/container.png)

Description: The generated PDF reports are stored in a dedicated blob container named reports within the Azure Storage Account (pa427100289). This container acts as the final destination for the "ephemeral" ACI job, providing persistent storage for reports that would otherwise be lost when the container instance terminates.

### Evidence 6.2: Manual ACI Run

![success](docs/pdf_success.png)

Description: The final state of the container is terminated (succeeded). Because the ACI was configured with a RestartPolicy of Never, it does not attempt to reboot after the Python script finishes its execution. This is intentional for "task-based" workloads that perform a single job and then shut down to save costs.

### Evidence 6.3: ACI Logs

![success](docs/pdf_success.png)

Description: The logs show the Python runtime successfully initializing, generating the PDF, and then outputting the confirmation message: Successfully uploaded [ORDER_ID.pdf to reports container.

### Evidence 6.4: Generated PDF

![success](docs/pdf_success.png)

Description: The presence of the file in the Azure Portal's Storage Explorer proves that the ACI successfully bridged the network and security gap. It demonstrates that the container had the necessary write permissions to perform the operation against the storage service.

### Evidence 6.5: Function App Managed Identity and IAM

![identity](docs/managed_identity.png)

Description: The Function App requires the Contributor (or a custom ACI-specific) role at the Resource Group scope. This is because the Function App acts as an orchestrator; it must have permission to programmatically "create" and "delete" other Azure resources (the ACI instances) via the Azure Resource Manager (ARM) API.

### Evidence 6.6: Report App Settings

![envvar1](docs/envvar_1.png)
![envvar2](docs/envvar_2.png)

Description: The environment variables are split into three functional groups:

Registry Auth: (ACR_USER, ACR_PASS) used by the ACI host to pull the private image from the Azure Container Registry.

Infrastructure Target: (STORAGE_ACCOUNT_URL, STORAGE_CONN) defines where the container should upload the final report.

Application Context: (ORDER_ID, ORDER_JSON) provides the specific business data the container needs to render the content of the PDF.

---

## Task 7: End-to-End Pipeline (15 points)

### Evidence 7.1: Web App Wiring

![web](docs/web_config.png)

Description: The frontend utilizes the asynchronous HTTP API pattern required by Azure Durable Functions. When a user submits an order, the Web App sends an HTTP POST request to the http_starter function. The Function App immediately returns an HTTP 202 (Accepted) response containing management endpoints, including a statusQueryGetUri. The frontend's JavaScript then actively polls this status URI at regular intervals, updating the UI state based on the runtimeStatus until it reads Completed or Failed.

### Evidence 7.2: Happy Path UI

![form](docs/form.png)
![completed](docs/completed.png)
![running](docs/running.png)
![pdf](docs/pdf.png)

Description: This sequence demonstrates a successful end-to-end execution. A valid order payload (with a quantity well below the threshold of 100) is submitted via the form. The UI correctly transitions to a "Running" state, displaying the unique orchestration instance ID while polling the backend. Once the orchestration finishes, the UI updates to "Completed" and dynamically renders the direct blob storage URL. The final downloaded PDF confirms the data payload was accurately passed from the frontend all the way to the reporting container.

### Evidence 7.3: Backend Participation

![aci](docs/spawned_aci.png)
![container](docs/container_pdf.png)
![aks_logs](docs/aks_logs.png)

Description: By tracing a specific Order ID through the system logs, we verify the distributed participation of all microservices. First, the AKS pod logs confirm that the validate_activity successfully routed the traffic to the cluster, which parsed and validated the payload. Following validation, the az container list output proves the orchestrator dynamically provisioned a temporary ACI (ci-report-[order_id]) to execute the report_activity. Finally, the storage container view confirms the ACI successfully completed its job by writing the PDF to Blob Storage before being deleted.

Note: Due to the strict Azure Policy restrictions enforced within the university's lab environment, I was unable to provision an Azure Application Insights resource (or a Log Analytics Workspace). Application Insights is a strict prerequisite for the Function App's "Monitor -> Invocations" dashboard to capture and display historical execution data. Because the resource creation was blocked by policy, the Invocations UI remains unpopulated, and I cannot provide a screenshot of this specific view. However, the successful orchestration of both activities is proven by the final "Completed" state in the Web App UI and the successful generation of the PDF in the Blob container.

### Evidence 7.4: Reject Path UI

![failure](docs/failure_msg.png)

Description: This sequence demonstrates the orchestrator's short-circuit logic. An invalid order (e.g., quantity > 100) is submitted, which causes the AKS validator to return a valid: false response. The Durable Orchestrator evaluates this result and immediately halts execution, returning a "rejected" status to the polling Web App. Because the workflow exits early, the report_activity is never invoked. This prevents the unnecessary provisioning of an ACI and saves compute costs, proving the orchestration handles business logic failures gracefully.

---

## Task 8: Write-up and Architecture Diagram (5 points)

### Evidence 8.1: Architecture Diagram

TODO: Embed your architecture diagram from `docs/`.

Description: TODO: Confirm that it shows GitHub, App Service, Durable Function, AKS, ACI, Blob Storage, ACR, and IAM.

### Question 8.2: Service Selection

TODO: In 3-4 sentences each, explain why TaskFlow uses App Service, Durable Functions, AKS, and ACI for their specific roles.

### Question 8.3: ACI vs AKS

TODO: Compare idle behavior, cost behavior, and operational model for AKS and ACI using your screenshots.

### Question 8.4: Durable Functions vs Plain HTTP

TODO: Explain at least two problems that Durable Functions solves for this sequential workflow.

### Question 8.5: Cost Review

TODO: Embed Cost Management screenshot scoped to your resource group.

Description: TODO: Identify the most expensive resource and explain why.

### Question 8.6: Challenges Faced

TODO: Describe at least two real issues you hit and how you debugged them.

---
