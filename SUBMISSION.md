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
| Name | Mohammad Mohib Haider |
| Roll Number | 27100164 |
| GitHub Repository URL | https://github.com/mohibhaider1/CS487-PA4 |
| Resource Group | `rg-sp26-27100164` |
| Assigned Region | `ukwest` |

## Evidence Rules

- Use relative image paths, for example: `![AKS nodes](docs/aks-nodes.png)`.
- Every image must have a 1-3 sentence description below it.
- Azure Portal screenshots must show the resource name and enough page context to identify the service.
- CLI screenshots must show the command and output.
- Mask secrets such as function keys, ACR passwords, and storage connection strings.


## Task 1: App Service Web App (15 points)

### Evidence 1.1: Forked Repository

![Forked Repository](docs/1.1.png)

Description: This is my working fork of the CS487-PA4 starter repository under my GitHub account. It contains the full PA4 project structure including webapp, function-app, validate-api, and report-job folders.

### Evidence 1.2: App Service Overview

![App Service Overview](docs/1.3.png)

Description: The Web App pa4-27100164 is deployed in resource group rg-sp26-27100164 in the UK West region. It is running on Node 22 LTS and is accessible at https://pa4-27100164.azurewebsites.net.

### Evidence 1.3: Deployment Center / GitHub Actions

![Deployment Center](docs/1.2.png)

Description: The Web App is connected to my GitHub fork via the Azure Deployment Center. Any push to the main branch automatically triggers a GitHub Actions workflow that deploys the latest code to the App Service.

### Evidence 1.4: Live Web UI

![Live Web UI](docs/1.4.png)

Description: The TaskFlow dashboard is loading successfully over HTTPS from the App Service. The Submit Order form is visible confirming the frontend is being served correctly.

---

## Task 2: Azure Container Registry (15 points)

### Evidence 2.1: ACR Overview

![ACR Overview](docs/2.1.png)

Description: The container registry pa427100164 is provisioned in resource group rg-sp26-27100164 in UK West with the Basic SKU and admin user enabled.

### Evidence 2.2: Docker Builds

![Docker Builds](docs/2.2.png)
![Docker Builds](docs/2.2b.png)

Description: All three images were built locally using docker build with the linux/amd64 platform flag. validate-api was built from the validate-api folder, report-job from report-job, and func-app from function-app.

### Evidence 2.3: ACR Repositories

![ACR Repositories](docs/2.4.png)
![ACR Repositories](docs/2.5.png)

Description: All three images were successfully pushed to the registry. validate-api:v1, report-job:v1, and func-app:v1 are all visible in the ACR repository list.

---

## Task 3: Durable Function Implementation (12 points)

### Evidence 3.1: Completed Function Code

[function_app.py](function-app/function_app.py)

Description: The orchestrator calls validate_activity first which makes a POST request to the AKS validator. If the order is valid it then calls report_activity which creates an ACI to generate the PDF report and returns the blob URL.

### Evidence 3.2: Local Function Handler Listing

![Function Handlers](docs/3.1.png)

Description: Running func start locally shows all four Durable Function handlers were discovered by the runtime: http_starter, my_orchestrator, validate_activity, and report_activity.

---

## Task 4: Function App Container Deployment (8 points)

### Evidence 4.1: Function App Container Configuration

![Function App Container](docs/4.1.png)

Description: The Function App pa4-27100164-fn is configured to pull the func-app:v1 image from pa427100164.azurecr.io in the Deployment Center.

### Evidence 4.2: Orchestration Smoke Test

![Smoke Test](docs/4.3.png)

Description: The curl command successfully triggered the orchestrator and returned an instance id along with a statusQueryGetUri proving the Function App is running and accepting requests.

### Evidence 4.3: Expected Failed Status Before Downstream Wiring

![Expected Failure](docs/4.4.png)

Description: The status query shows runtimeStatus as Failed because VALIDATE_URL was not yet configured at this stage. This is expected and confirms the orchestrator ran and reached the validate_activity step before failing.

---

## Task 5: AKS Validator (15 points)

### Evidence 5.1: AKS Cluster

![AKS Cluster](docs/5.1.png)

Description: The AKS cluster pa4-27100164 is running in resource group rg-sp26-27100164 in UK West with 1 node of size Standard_B2s.

### Evidence 5.2: Kubernetes Nodes and Pods

![Nodes and Pods](docs/5.1.png)
![Nodes and Pods](docs/5.2.png)

Description: kubectl get nodes shows one node in Ready state and kubectl get pods shows the validate-deployment pod is running successfully.

### Evidence 5.3: Kubernetes Service

![Kubernetes Service](docs/5.3.png)

Description: The validate-service of type LoadBalancer has been assigned the external IP 51.141.114.228 on port 8080 which is used by the Function App to reach the validator.

### Evidence 5.4: Validator API Tests

![Validator Tests](docs/5.4.png)

Description: The health endpoint returns ok. A valid order with qty 2 returns valid true and a bad order with qty 999 returns valid false with the reason quantity exceeds limit confirming the validator logic is working.

### Evidence 5.5: Function App `VALIDATE_URL`

![VALIDATE_URL Setting](docs/5.5.png)

Description: The VALIDATE_URL application setting on the Function App is set to http://51.141.114.228:8080/validate which is the external IP of the AKS LoadBalancer service.

### Evidence 5.6: AKS Idle Behavior

![AKS Idle](docs/5.6.png)

Description: Unlike ACI, the AKS node continues running even when there are no incoming orders. The node stays in Ready state and the pod stays Running at all times because AKS is designed for long-lived services not short-lived jobs.

---

## Task 6: ACI Report Job (15 points)

### Evidence 6.1: Blob Container

![Blob Container](docs/6.1.png)

Description: The reports blob container was created in the storage account pa427100164 where the report job writes its generated PDFs.

### Evidence 6.2: Manual ACI Run

![ACI Run](docs/6.2.png)

Description: The manually created container instance ci-report-test reached the Succeeded state confirming the report-job image ran successfully and exited cleanly after completing its work.

### Evidence 6.3: ACI Logs

![ACI Logs](docs/6.3.png)

Description: The container logs show the report job generating the PDF and uploading it to blob storage before exiting.

### Evidence 6.4: Generated PDF

![Generated PDF](docs/6.4.png)

Description: TEST-001.pdf is listed in the reports blob container confirming the ACI successfully wrote its output to Azure Blob Storage.

### Evidence 6.5: Function App Managed Identity and IAM

![Managed Identity](docs/6.5.png)

Description: The user assigned managed identity mi-pa4-27100164 is attached to the Function App. This allows the report_activity to authenticate with Azure and create ACI instances on demand without storing any credentials in the code.

### Evidence 6.6: Report App Settings

![Report Settings](docs/6.6.png)
![Report Settings](docs/6.6b.png)

Description: REPORT_IMAGE points to the report-job image in ACR. ACR_SERVER, ACR_USERNAME, and ACR_PASSWORD are used to pull the image. STORAGE_ACCOUNT_URL tells the report job where to write the PDF. SUBSCRIPTION_ID and REPORT_RG tell the SDK where to create the ACI. Passwords are masked.

---

## Task 7: End-to-End Pipeline (15 points)

### Evidence 7.1: Web App Wiring

![Web App Wiring](docs/1.5.png)

Description: FUNCTION_START_URL is set to the http_starter endpoint of the Function App with the function key. FUNCTION_STATUS_URL is set to the Durable Task webhook endpoint used by the frontend to poll orchestration status.

### Evidence 7.2: Happy Path UI

![Happy Path](docs/7.1.png)
![Happy Path](docs/7.2.png)
![Happy Path](docs/7.3.png)
![Happy Path](docs/7.4.png)

Description: A valid order was submitted with SKU ABC and qty 2. The status panel first showed Running with a live instance ID then updated to Completed with the report URL proving the full pipeline executed successfully.

### Evidence 7.3: Backend Participation

![Backend Participation](docs/7.5.png)
![Backend Participation](docs/7.6.png)
![Backend Participation](docs/7.7.png)
![Backend Participation](docs/7.7b.png)
![Backend Participation](docs/7.8.png)

Description: The Function App log stream shows http_starter, my_orchestrator, validate_activity, and report_activity all executing in sequence for the same order. The AKS validator received the request and the PDF appeared in blob storage matching the submitted order ID.

### Evidence 7.4: Reject Path UI

![Reject Path](docs/7.9.png)
![Reject Path](docs/7.10.png)
![Reject Path](docs/7.11.png)
![Reject Path](docs/7.11b.png)

Description: An order was submitted with qty 999 which exceeds the validator limit of 100. The orchestrator received the rejected response from the AKS validator and returned status rejected without creating any ACI, confirmed by the empty az container list output.

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
