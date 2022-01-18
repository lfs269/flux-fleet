# Flux Fleet (flux-fleet) Repository

This repository contains code to setup Flux Deployment to various kubernetes clusters in the organisation. 

This repository is managed by **Site Reliability Engineers**. 

Type of code and their respective paths are,

  * clusters/*  :  Configuration for Each Kubernetes Cluster, flux-system manifests(output of bootstrap), and what (which  projects) to on board on the cluster. 
  * infrastructure/*  : Either plain YAMLs or additinally environment specific kustomize overlays.
  * projects/* : Projects(Tenants) On Boarding Spec 

## Code Organisation Strategy with FluxCD

You would maintain 3 different repos to maintain,

  * Flux Fleet
  * Project Deployment
  * Project/App Source

  1. **Flux Fleet Repository**  to manage organisation wide deployments to Kubernetes with FluxCD. This repository would contain,
       * Cluster Configurations for all your envvironments e.g. staging, production. You would bootstrap the clusters by pointing to this repository
       * Cluster Wide Infrastructure Components, e.g. Ingress Controllers, Repository Secrets etc
       * Project on boarding configurations aka. tenants spec. This is the spec that would point to a project repo for each tenant, fetch the sync manifests and deploy

     You could call it the **flux-fleet** repository, which is managed typically by the SREs/DevOps Engineers. [Fork this repo](https://github.com/lfs269/flux-fleet) to start setting up the fleet repository.

  2. Project Deployment Repository (Tenant)
     This repository would contain,
       * Flux sync/deployment code. For example,
           * GitRepository
           * HelmRepository
           * Kustomization (FluxCD Deployments)
           * HelmRelease
       * Helm charts source code
       * YAML manifests as either
           * Plain YAML
           * With Kustomize Overlays
     This project repository serves as one tenant on the cluster, typically managed by Developers + SREs.
     **Setup** : Create a new repository as **xxxx-deploy** (xxxx=`your_app_name`), clone it and from the root of the repository path [run this script](https://raw.githubusercontent.com/lfs269/setup/main/setup_project_repo.sh). Use the following command to do so, 


      ```bash
      $ curl -fsSL https://raw.githubusercontent.com/lfs269/setup/main/setup_project_repo.sh | bash -
      ```

  3. Project/App Source Repository
       * Application source code
       * Contains source code + Dockerfile etc
       * Maintained by developers

## On Boarding a Project

  1. SREs setup fleet management of Kubernetes clusters by,
    
     Creating `flux-fleet` repo with,
       * Cluster definitions, e.g., `staging`, `QA`, `production`
       * Cluster-wide infrastructure deployment code
       * Projects (tenants) onboaring scaffold
     
     Bootstrapping clusters with,
       * Git source integration with *flux-fleet* repository
       * Flux controllers
       * CRDs
       * Policies, e.g., `RBAC`, `Network Policy`, etc
       * Git source repository interation

  2. Developers/SREs create deplpoyment repositories with,
       * Flux sync/deployment manifests
       * YAML deployment + Kustomize overlays
       * Helm charts

  3. Project owners raise a on boarding request,
       * Provide project deployment repository

  4. SREs on board the project by,
       * Genrating tenant RBAC
       * Project on boarding manifests

  5. FluxCD reconciles the cluster state by,
       * Syncing project deployment code
       * Generating `Flux` resources for the project e.g.,
           * Git Repository
           * Kustomization
           * Helm Release
       * Running reconciliation for each of the Flux resource with actual Kubernetes cluster
