# workflows

Repository for reusable workflows.

Supports:

- Maven-specific builds
- Python-specific (w/nox) builds
- Non-specific builds (i.e. node)
- Pre-built image deploys (i.e. mongodb or demo-deploys)

## Usage

### The reusable workflow
In order to make the workflow work properly, you need to provide all required inputs and secrets.

```Code
name: Reusable build, test, publish & deploy

on:
  workflow_call:
    inputs:
      app_name:
        required: true
        type: string
      caller_sha:
        required: true
        type: string
      repo:
        required: true
        type: string
      environment:
        required: true
        type: string
      cluster:
        required: true
        type: string
      actor:
        required: true
        type: string
    secrets:
      GH_TOKEN:
        required: true
      GCP_SA_DIGDIR_FDK_GCR_KEY:
        required: true
      DIGDIR_FDK_AUTODEPLOY:
        required: true
      SLACK_WEBHOOK_URL:
        required: true
```

### How to call a reusable workflow
In the caller-workflow in your repository, you need to specify a job like the following:
```Code
jobs:
  # Create a job that use a suitable .yaml-file from the workflows-repo
  build-and-deploy-staging:
    name: Call reusable workflow
    uses: Informasjonsforvaltning/workflows/.github/workflows/build-deploy.yaml@main
    # Here we have selected the build-deploy workflow that builds a docker image, 
    # then deploys to a GCP cluster (based on input)
    with:
      # The job will need some input from the caller workflow
      app_name: example-app-name
      caller_sha: ${{ github.sha }}
      repo: ${{ github.repository }}
      actor: ${{ github.actor }}
      environment: 'staging'
      cluster: 'digdir-fdk-dev'
    secrets:
      # Secrets also need to be added
      GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      GCP_SA_DIGDIR_FDK_GCR_KEY: ${{ secrets.GCP_SA_DIGDIR_FDK_GCR_KEY }}
      DIGDIR_FDK_AUTODEPLOY: ${{ secrets.DIGDIR_FDK_DEV_AUTODEPLOY }}
      SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
```

