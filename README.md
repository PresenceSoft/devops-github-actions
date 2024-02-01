# Build and Deploy to Google Cloud Run Workflow

This repository contains a reusable GitHub Actions workflow for building a Docker image, pushing it to Google Artifact Registry, and deploying it to Google Cloud Run.

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
    uses: PresenceSoft/devops-github-actions/.github/workflows/build-deploy-cloudrun.yaml@main
    with:
      project_id: "your-gcp-project-id"
      gar_location: "europe-west9"
      repository: "your-repository"
      service: "your-service"
      wif_provider: "your-wif-provider"
      wif_service_account: "your-wif-service-account@your-project.iam.gserviceaccount.com"
      cloudrun_region: "europe-west9"
      cloudrun_wif_provider: "your-cloudrun-wif-provider"
      cloudrun_wif_service_account: "your-cloudrun-wif-service-account@your-project.iam.gserviceaccount.com"
      cloudrun_service: "your-cloudrun-service"
      google_chat_webhook: "your-google-chat-webhook"
```

### Inputs

The workflow expects the following inputs:

- `project_id`: The GCP project ID.
- `gar_location`: The location for the Google Artifact Registry.
- `repository`: The name of the Artifact Registry repository.
- `service`: The name of the service in Artifact Registry.
- `wif_provider`: The Workload Identity Federation provider.
- `wif_service_account`: The service account for the Workload Identity Federation.
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
