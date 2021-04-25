# FLux Fleet (flux-fleet) Repo 

This repository contains code to setup Flux Deployment to various kubernetes clusters in the organisation. 

This repo is managed by **Site Reliability Engineers**. 

Type of code and their respective paths are,

  * clusters/*  :  Configuration for Each Kubernetes Cluster, flux-system manifests(output of bootstrap), and what (which  projects) to on board on the cluster. 
  * infrastructure/*  : Either plain YAMLs or additinally environment specific kustomize overlays.
  * projects/* : Projects(Tenants) On Boarding Spec 


## Code Organisation Strategy with FluxCD

You would maintain 3 Different Repos to maintain
  * Flux Fleet
  * Project Deployment
  * Project/App Source

  1. **Flux Fleet Repository**  to manage organisation wide deployments to Kubernetes  with FluxCD.
     This repository would contain
       * Cluster Configurations for all your envvironments e.g. staging, production. You would bootstrap the clusters by pointing to this repo.
       * Cluster Wide Infrastructure Components
           e.g. Ingress Controllers, Repository Secrets etc.
       * Project on boarding configurations aka. tenants spec. This is the spec that would point to a project repo for each tenant, fetch the sync manifests and deploy.
     You could call it a  **flux-fleet** repository, which is managed typically by the SREs. [Fork this repo](https://github.com/lfs269/flux-fleet) to start seting up  fleet repo.
     Who maintains Fleet Repo ==> Site Reliability Engineers / Devops/Platform Engineers.

  2. Project Deployment Repository (Tenant).
     This repository would contain,
       * Flux Sync/Deployment Code. For example,
           * GitRepository
           * HelmRepository
           * Kustomization (FluxCD Deployments)
           * HelmRelease
       * Helm Charts Source Code
       * YAML Manifests as either
           * Plain YAML
           * With Kustomize Overlays
     This project repository serves as one tenant on the cluster.
     Who maintains Fleet Repo ==> Developers + SREs.
     **Setup** : Create a new repository as **xxxx-deploy** (xxxx= your project/app name), clone it and from the root of the repository path  [download repository run this script](https://raw.githubusercontent.com/lfs269/setup/main/setup_project_repo.sh). Use the following command to do do so, 
     ```
       curl -fsSL https://raw.githubusercontent.com/lfs269/setup/main/setup_project_repo.sh | bash -
     ```

  3. Project/App Source Repository
       * Application Source Code
       * Self Explainatory
       * Contains Source Code + Dockerfile etc.
     Who maintains Fleet Repo ==> Developers.


## On Boarding a Project

  1. SREs Setup Fleet Management of Kubernetes Clusters by
     Creating  flux-fleet repo with
       * Cluster Definitions e.g. staging, qa, production
       * Cluster-wide  Infrastructure Deployment Code
       * Projects (Tenants) Onboaring Scaffold
     Bootstrapping Clusters with
       * Git Source Integration with *flux-fleet* repo (its GitOps you know...)
       * Flux Controllers
       * CRDs
       * Policies : RBAC, Network Policy etc.
       * Git Source Repo Interation (its GitOps you know...)

  2. Developers/SREs create Deplpoyment Repo with
       * Flux Sync/Deployment Manifests
       * YAML Deployment + Kustomize Overlays
       * Helm Charts

  3. Project owners raise a On Boarding Request
       * Provide Project Deployment Repository

  4. SREs On Board the Project by
       * Genrating Tenant RBAC
       * Project On Boarding Manifests

  5. FluxCD Reconciles the Cluster State by
       * Syncing Project Deployment Code
       * Genrating Flux Resources for the Project e.g.
           * GitRepository
           * Kustomization
           * HelmRelease
       * Running Reconciliation for each of the Flux Resource with actual Kubernetes Cluster.
