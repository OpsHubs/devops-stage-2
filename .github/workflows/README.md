# GitHub Actions Runner Controller (ARC) Setup for OpsHubs

This repository provides instructions for setting up the GitHub Actions Runner Controller (ARC) in a Kubernetes cluster to manage self-hosted runners for the "OpsHubs/devops-stage-2" repository.

## Table of Contents

- [Introduction](#introduction)
- [Prerequisites](#prerequisites)
- [Forking the Repository](#forking-the-repository)
- [Installing Actions Runner Controller (ARC)](#installing-actions-runner-controller-arc)
- [Configuring Runner Scale Set](#configuring-runner-scale-set)
- [Creating and Running a Workflow](#creating-and-running-a-workflow)
- [Conclusion](#conclusion)

## Introduction

Actions Runner Controller (ARC) is a Kubernetes operator that orchestrates and scales self-hosted runners for GitHub Actions. With ARC, you can create runner scale sets that automatically scale based on the number of workflows running in your repository, organization, or enterprise.

## Prerequisites

Before proceeding, ensure you have the following:

- A Kubernetes cluster (e.g., AKS for cloud or Minikube/Kind for local setups)
- Helm 3 installed on your local machine
- Access to the "OpsHubs" GitHub organization

## Forking the Repository

To start, fork the HNG boilerplate repository to your "OpsHubs" organization.

1. **Log in to GitHub**: Go to [GitHub](https://github.com/) and log in.
2. **Navigate to the Boilerplate Repository**: Find the HNG boilerplate repository that you want to fork.
3. **Fork the Repository**:
   - Click on the "Fork" button in the top-right corner.
   - In the dialog, select "OpsHubs."
   - The repository will now be forked to "OpsHubs" as "devops-stage-2."

## Installing Actions Runner Controller (ARC)

Install ARC in your Kubernetes cluster using Helm.

1. **Set Namespace and Installation Name**:

   ```bash
   NAMESPACE="ace-systems"
   INSTALLATION_NAME="self-hosted"
   ```
   
   

2 **Install the ARC Helm Chart**:
   ```bash
  helm install arc \
    --namespace "${NAMESPACE}" \
    --create-namespace \
    oci://ghcr.io/actions/actions-runner-controller-charts/gha-runner-scale-set-controller
  ```
3 **Generate a Personal Access Token (PAT)**:
  - Go to your GitHub account settings.
  - Generate a PAT with `repo` and `admin:org` permissions.


## Configurin Runner Scale Set
Configure the runner scale set to connect to the "OpsHubs/devops-stage-2" repository.

- Set GitHub Configuration Variables:
   ```bash
  GITHUB_CONFIG_URL="https://github.com/OpsHubs/devops-stage-2"
  GITHUB_PAT="<Your_GitHub_PAT>"
 ```

- Install the Runner Scale Set::
   ```bash
helm install "${INSTALLATION_NAME}" \
    --namespace "ace-runners" \
    --create-namespace \
    --set githubConfigUrl="${GITHUB_CONFIG_URL}" \
    --set githubConfigSecret.github_token="${GITHUB_PAT}" \
    oci://ghcr.io/actions/actions-runner-controller-charts/gha-runner-scale-set
  ```
- Verify the Installation:
  ```bash
    helm list -A
    kubectl get pods -n ace-systems
  ```

## Creating and Running a Workflow
 Create a workflow in the forked repository to test the ARC setup.

1. Add a GitHub Actions Workflow:

In the "OpsHubs/devops-stage-2" repository, create a new workflow file at .github/workflows/github-actions-demo.yml.
     
     ```bash
          name: GitHub Actions Demo
          run-name: ${{ github.actor }} is testing out GitHub Actions 🚀
          on: [push]
          jobs:
          Explore-GitHub-Actions:
           runs-on: self-hosted
          steps:
          - run: echo "🎉 The job was automatically triggered by a ${{ github.event_name }} event."
          - run: echo "🐧 This job is now running on a ${{ runner.os }} server hosted by GitHub!"
          - run: echo "🔎 The name of your branch is ${{ github.ref }} and your repository is ${{ github.repository }}."
          - name: Check out repository code
              uses: actions/checkout@v4
              - run: echo "💡 The ${{ github.repository }} repository has been cloned to the runner."
              - run: echo "🖥️ The workflow is now ready to test your code on the runner."
          - name: List files in the repository
            run: |
            ls ${{ github.workspace }}
          - run: echo "🍏 This job's status is ${{ job.status }}."  
       ```
  - Monitor Runner Pods:
     ```bash
       kubectl get pods -n ace-runners
     ```
    ### Conclusion
     You have successfully set up the GitHub Actions Runner Controller (ARC) for the "OpsHubs/devops-stage-2" repository. This setup enables automatic scaling of self-hosted runners based on the workflows running in your repository.


   
  
