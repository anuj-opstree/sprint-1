---

# **Terraform Module CI/CD Documentation**

<img width="2540" height="1420" alt="image" src="https://github.com/user-attachments/assets/435c26b6-1f33-4b37-ae8c-4496e8f0988b" />

---

## **Author Details**

| **Created By** | **Created On** | **Version** | **Last Updated By** | **Pre Reviewer** | **L0 Reviewer** | **L1 Reviewer** | **L2 Reviewer** |
| -------------- | -------------- | ----------- | ------------------- | ---------------- | --------------- | --------------- | --------------- |
| Anuj Jain   | 19-09-2025     | V1.0        | Anuj Jain        | Prashant              | TBD             | –               | –               |

---

## **Table of Contents**

1. [Introduction](#introduction)
2. [What is Terraform Module CI/CD?](#what-is-terraform-module-cicd)
3. [Why Terraform Module CI/CD?](#why-terraform-module-cicd)
4. [Terraform CI Workflow](#terraform-ci-workflow)
5. [Terraform CD Workflow](#terraform-cd-workflow)
6. [Supported Functions](#supported-functions)
7. [System Requirements & Prerequisites](#system-requirements--prerequisites)
8. [Jenkins Usage Example](#jenkins-usage-example)
9. [Inputs and Outputs](#inputs-and-outputs)
10. [Best Practices](#best-practices)
11. [Deployment Lifecycle](#deployment-lifecycle)
12. [Troubleshooting](#troubleshooting)
13. [Conclusion](#conclusion)
14. [Contact Information](#contact-information)
15. [References](#references)

---

## **Introduction**

This documentation provides a detailed guide for implementing **CI/CD workflows for Terraform modules** using Jenkins and a shared library.

It standardizes the process of **validating, planning, applying, and destroying infrastructure** across environments, ensuring **consistency, reliability, and reusability**.

---

## **What is Terraform Module CI/CD?**

Terraform Module CI/CD is the automated pipeline that:

* Validates module code.
* Generates execution plans.
* Applies infrastructure changes safely.
* Destroys infrastructure when required.

This automation reduces errors, improves delivery speed, and ensures every change is version-controlled and auditable.

---

## **Why Terraform Module CI/CD?**

*  **Faster Delivery** – Automates provisioning and updates.
*  **Consistency** – Enforces standard workflows across projects.
*  **Security** – Integrates with tools like `tfsec` / `checkov`.
*  **Auditability** – All changes tracked in Git/Jenkins logs.
* 🛡 **Safety** – Requires plan review/approval before apply.

---

## **Terraform CI Workflow**

### **Steps**

1. Initialize Terraform (`terraform init`).
2. Check formatting (`terraform fmt -check`).
3. Validate configuration (`terraform validate`).
4. Lint and security scan (`tflint`, `tfsec`, `checkov`).
5. Generate execution plan (`terraform plan`).

---

## **Terraform CD Workflow**

### **Steps**

1. Apply infrastructure changes (`terraform apply`).
2. Destroy infrastructure if required (`terraform destroy`).
3. Monitor logs and verify state consistency.

---

## **Supported Functions**

| **Function**          | **Stage** | **Description**                    |
| --------------------- | --------- | ---------------------------------- |
| `terraformInit()`     | CI        | Initializes working directory      |
| `terraformValidate()` | CI        | Validates syntax and configuration |
| `terraformPlan()`     | CI        | Creates execution plan             |
| `terraformApply()`    | CD        | Applies infrastructure changes     |
| `terraformDestroy()`  | CD        | Destroys infrastructure safely     |

---

## **System Requirements & Prerequisites**

* Jenkins with Pipeline & Shared Library support.
* Terraform ≥ 1.5.0 installed on agents.
* Shared library added in Jenkins global config.
* GitHub/GitLab/Bitbucket repo with Terraform modules.
* Cloud provider credentials configured in Jenkins credentials store.

---

## **Jenkins Usage Example**

```groovy
@Library('terraform-shared-library') _

pipeline {
    agent any
    stages {
        stage('Init') {
            steps { script { terraformInit() } }
        }
        stage('Validate') {
            steps { script { terraformValidate() } }
        }
        stage('Plan') {
            steps { script { terraformPlan() } }
        }
        stage('Apply') {
            when { branch 'main' }
            steps { script { terraformApply() } }
        }
        stage('Destroy') {
            when { branch 'cleanup' }
            steps { script { terraformDestroy() } }
        }
    }
}
```

---

## **Inputs and Outputs**

### **Inputs**

| **Parameter**     | **Description**                          | **Required** | **Example**        |
| ----------------- | ---------------------------------------- | ------------ | ------------------ |
| `TF_VAR_region`   | Deployment region                        | Yes          | `us-east-1`        |
| `TF_BACKEND_CONF` | Backend config file                      | Optional     | `backend.hcl`      |
| `CREDENTIALS_ID`  | Jenkins credentials ID for provider auth | Yes          | `aws-jenkins-cred` |
| `TF_WORKSPACE`    | Target workspace/environment             | Optional     | `dev`, `prod`      |

### **Outputs**

| **Output**          | **Description**                      |
| ------------------- | ------------------------------------ |
| Terraform Plan File | `.tfplan` generated during pipeline  |
| Deployment Status   | Success/Failure logs                 |
| State File          | Updated state (local/remote backend) |

---

## **Best Practices**

* Use **remote state backends** (S3, GCS, Azure Blob) with locking.
* Enforce **plan before apply**.
* Store credentials in Jenkins securely.
* Implement **branch strategy**:

  * `main` → Apply.
  * `cleanup` → Destroy.
* Use **workspace separation** for dev/staging/prod.
* Add **security checks** (`tfsec`, `checkov`) in CI stage.

---

## **Deployment Lifecycle**

| **Stage**          | **Action**                                       |
| ------------------ | ------------------------------------------------ |
| Code Commit        | Developer pushes Terraform code to Git           |
| Validation         | Lint, syntax check, security scan                |
| Plan               | Execution plan generated                         |
| Approval           | (Optional) Manual approval required before apply |
| Apply              | Apply infrastructure changes                     |
| Monitor            | Logs/alerts monitored post-deployment            |
| Destroy (Optional) | Clean-up infra when no longer needed             |

---

## **Troubleshooting**

| **Issue**                      | **Resolution**                                                          |
| ------------------------------ | ----------------------------------------------------------------------- |
| `terraform init` fails         | Check backend config and credentials                                    |
| No changes in plan             | Verify `.tf` files and workspace                                        |
| Apply fails with IAM error     | Update Jenkins credentials and IAM permissions                          |
| State lock error               | Use `terraform force-unlock <ID>`                                       |
| Functions not found in Jenkins | Ensure library is imported using `@Library('terraform-shared-library')` |

---

## **Conclusion**

The **Terraform Module CI/CD framework** ensures a **standardized, reliable, and secure pipeline** for infrastructure automation. Using shared libraries in Jenkins minimizes repetition and enforces **best practices** for managing infrastructure at scale.

---

## **Contact Information**

| **Name**     | **Email**                                                         |
| ------------ | ----------------------------------------------------------------- |
| Anuj Jain | [Anuj.jain@mygurukulam.co](mailto:vivek.sharma@mygurukulam.co) |

---

## **References**

| **Title**               | **Link**                                                                                                                 |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| Terraform Documentation | [https://developer.hashicorp.com/terraform/docs](https://developer.hashicorp.com/terraform/docs)                         |
| Jenkins Pipeline Docs   | [https://www.jenkins.io/doc/book/pipeline/](https://www.jenkins.io/doc/book/pipeline/)                                   |
| Shared Library Guide    | [https://www.jenkins.io/doc/book/pipeline/shared-libraries/](https://www.jenkins.io/doc/book/pipeline/shared-libraries/) |

---

