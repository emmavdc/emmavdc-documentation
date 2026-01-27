# CI/CD Basics (Azure DevOps Context)

---

## What does CI/CD mean?
CI/CD stands for **Continuous Integration** and **Continuous Deployment (or Delivery)**.

It is a way to **automate code validation and application deployment**.

---

## Continuous Integration (CI)

**Goal:**  
Make sure the code works correctly.

**What happens in CI:**
- A developer pushes code to Git
- Azure DevOps pulls the code
- The code is built (compiled)
- Automated tests are executed

**Result:**  
✔️ The code is valid and ready to be deployed

**Tools:**
- Azure DevOps Pipelines (Build)
- GitHub Actions

---

## Continuous Deployment (CD)

**Goal:**  
Deploy the application automatically.

**What happens in CD:**
- Uses the validated output from CI
- Deploys the application to one or more environments
- Deployment can be automatic or require manual approval

**Deployment targets (not only Azure):**
- Azure (App Service, AKS, VMs)
- AWS
- Google Cloud
- On-premise servers
- Kubernetes clusters
- Any server accessible via SSH, API, or agent

**Result:**  
🚀 The application is running in the target environment

---

## Key Difference

| CI | CD |
|----|----|
| Validate code | Deploy application |
| Build + test | Release to environment |
| Before deployment | After CI |

---

## Simple Example with Azure DevOps

1. Developer pushes code to Git
2. CI pipeline runs:
   - Build
   - Tests
3. CD pipeline runs:
   - Deploy to the target environment (Azure or elsewhere)

---

## One-line Summary

**CI = Check the code**  
**CD = Deploy the application**