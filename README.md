# Build and Deploy to Google Cloud Run Workflow

This repository contains a reusable GitHub Actions workflow for building a Docker image, pushing it to Google Artifact Registry (GAR), and deploying it to Google Cloud Run.

## Usage

To use this workflow in your repository, you need to set up the following:

### Pre-requisites

- A Google Cloud Platform project.
- A service account in your GCP project with the necessary permissions (Cloud Run Admin, Storage Admin, and Service Account User roles are commonly required).
- Enable Workload Identity Federation between the GCP project and GitHub Actions.
- Configure secrets in your GitHub repository for authenticating with GCP.

### Workflow Configuration

The workflow is triggered by a `workflow_call` from another workflow/ To call this workflow, use the following syntax in your `PresenceSoft/devops-github-actions/.github/workflows/build-deploy-cloudrun.yaml@main`:

```yaml
name: "Your Workflow Name"

on:
  push:
    branches:
      - main # or any branch you prefer

jobs:
  deploy:
    permissions:
      contents: "read"
      id-token: "write"
    uses: PresenceSoft/devops-github-actions/.github/workflows/build-deploy-cloudrun.yaml@main
    with:
      gar_project_id: "your-gcp-project-id"
      gar_location: "europe-west9"
      gar_repository: "your-repository"
      gar_service: "your-service"
      gar_wif_provider: "your-wif-provider"
      gar_wif_service_account: "your-wif-service-account@your-project.iam.gserviceaccount.com"
      cloudrun_region: "europe-west9"
      cloudrun_wif_provider: "your-cloudrun-wif-provider"
      cloudrun_wif_service_account: "your-cloudrun-wif-service-account@your-project.iam.gserviceaccount.com"
      cloudrun_service: "your-cloudrun-service"
      google_chat_webhook: "your-google-chat-webhook"
```

### Secrets

- `pat_token`: GitHub Personal Access Token required if checkout_submodules is true.

### Inputs

The workflow expects the following inputs:

- `checkout_submodules`: Optional boolean to checkout submodules. Default is false.

- `gar_project_id`: The GCP project ID.
- `gar_location`: The location for the Google Artifact Registry.
- `gar_repository`: The name of the Artifact Registry repository.
- `gar_service`: The name of the service in Artifact Registry.
- `gar_wif_provider`: The Workload Identity Federation provider.
- `gar_wif_service_account`: The service account for the Workload Identity Federation.
- `cloudrun_region`: The region for the Cloud Run deployment.
- `cloudrun_wif_provider`: The Workload Identity Federation provider for Cloud Run.
- `cloudrun_wif_service_account`: The service account for the Workload Identity Federation for Cloud Run.
- `cloudrun_service`: The name of the Cloud Run service.
- `google_chat_webhook`: The webhook URL for Google Chat notifications.

### Workflow Steps

The workflow performs the following steps:

1. **Checkout**: Checks out the repository code.
2. **Google Auth**: Authenticates with GCP using the provided service account.
3. **Set up Docker Buildx**: Sets up Docker Buildx for building multi-architecture images.
4. **Docker Auth**: Authenticates with Google Artifact Registry.
5. **Build and Push**: Builds the Docker image and pushes it to Google Artifact Registry.
6. **Deploy to Cloud Run**: Deploys the image to Google Cloud Run.
7. **Google Chat Notification**: Sends a notification to Google Chat about the deployment status.

### Security

Ensure that your service account keys and other sensitive information are stored securely as [GitHub Encrypted Secrets](https://docs.github.com/en/actions/reference/encrypted-secrets) and are referenced appropriately in the workflow file.

---

For more detailed instructions, please refer to the [GitHub Actions documentation](https://docs.github.com/en/actions) and the [Google Cloud documentation](https://cloud.google.com/docs).

Certainly! Below is a clear and detailed documentation of the Docker image tagging and CI/CD strategy, ready to be included in your `README.md`. This documentation outlines the workflow, tagging conventions, and deployment strategy for managing multiple environments, ensuring clarity and consistency across your team.

---

## CI/CD Strategy with Docker Image Tagging

### Overview

This document outlines our Continuous Integration/Continuous Delivery (CI/CD) strategy, focusing on Docker image management and deployment across various environments, including staging and multiple client-specific environments. Our strategy ensures that the production-ready version of our application is consistently deployed across all client environments, leveraging Docker tags for clear versioning and environment management.

### Docker Image Tagging Convention

We utilize two primary Docker tags to manage our images across the development lifecycle:

- **`latest`**: Represents the most current, production-ready version of our application. This image is deployed across all client environments to ensure consistency and reliability.
- **`staging`**: Used for the version currently in development and testing phases. This tag is automatically deployed to our staging environment for testing.

Additionally, immutable tags (e.g., based on semantic versioning like `v1.2.3`) are used to identify specific releases. These tags are crucial for tracking, rollback, and documentation purposes.

### CI/CD Workflow

Our CI/CD pipeline is designed to support continuous integration by automatically building and testing code changes, followed by a controlled deployment process across various environments.

1. **Development and Testing**:

   - **Commit to Development Branch**: Commits to a development branch (e.g., `develop`) trigger an automatic build and tagging of the Docker image as `staging`. This image is deployed to the staging environment for thorough testing.
   - **Environment**: The staging environment is a replica of production, ensuring that any issues can be caught before they impact clients.

2. **Production-Ready Release**:

   - **Commit to Main Branch**: Commits to the main branch trigger a Docker build for the `latest` tag, signifying that the code is ready for production deployment. This process should include stringent tests and possibly manual reviews to ensure the code's stability and performance.
   - **Git Tagging for Releases**: Creating a Git tag on the main branch (e.g., `v1.2.3`) signifies a new production release. This tag is associated with the `latest` Docker image and documented for historical tracking.

3. **Client-Specific Deployment**:
   - **Deployment Trigger**: The addition of a Git tag to the main branch acts as a trigger for deploying the `latest` Docker image to client-specific environments. This ensures all clients are updated to the new version simultaneously, maintaining uniformity across deployments.
   - **Deployment Process**: The deployment process involves pulling the `latest` tagged Docker image from our registry and deploying it across all client environments, following any client-specific configurations or requirements.

### Immutable Tags and Rollbacks

For each build, we also create an immutable tag that reflects the specific version or commit hash. This practice allows for precise version tracking and facilitates easy rollback if necessary.

### Deployment Safeguards

- **Manual Approval**: For production deployments, including updates to client environments, we incorporate a manual approval step. This safeguard ensures that only fully vetted and tested changes are deployed to production.
- **Monitoring and Alerts**: Post-deployment, we monitor application performance and health closely. Any anomalies trigger alerts and may initiate a rollback to a previous stable version.

### Conclusion

This CI/CD strategy with Docker image tagging ensures a streamlined, consistent deployment process across all environments. It balances automation with necessary controls to maintain high-quality, stable releases of our application.

---

Feel free to adjust the documentation as needed to fit your project's specific requirements or conventions. This template aims to provide a solid foundation for documenting your CI/CD practices in a clear and accessible manner.
