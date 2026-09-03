# Azure AppService

- Azure App Service is a PaaS (Platform as a Service) offering that lets our deploy and manage web apps without managing the underlying infrastructure
- HTTP-based service for hosting:
    - Web applications
    - REST APIs
    - Mobile back ends
- We can choose our programming language and either a Windows or a Linux environment
- Takes care of the underlying infrastructure:
    - Security patches for the OS and languages
    - Load balancing
    - Autoscaling
    - Automated management
- Makes it easy to implement common integrations and features:
    - Azure DevOps for deployments
    - GitHub integration
    - Docker Hub integration
    - Package management
    - Staging environments
    - Custom domains
    - Attaching TLS/SSL certificates
- Can run single-container or multi-container Docker workloads
- The app name must be globally unique because it becomes a fully qualified domain name

## App Service Plan Tiers

- We pay based on the Azure App Service Plan
- Shared tier:
    - Free and Shared plans
    - Linux is not supported
- Dedicated tier:
    - Basic, Standard, Premium, PremiumV2 and PremiumV3 plans
- Isolated tier

## Azure App Services - Runtimes

- Runtime environment: software and instructions that execute while an application is running
- A runtime generally defines the:
    - Programming language
    - Libraries
    - Framework
- An Azure App Service runtime is a predefined container with a programming language and commonly used libraries installed
- A runtime is selected when configuring an Azure App Service
- Available runtimes include:
    - .NET
    - .NET Core
    - Java
    - Ruby
    - Node.js
    - PHP
    - Python
- Azure App Service generally provides multiple current versions of a programming language, such as Ruby 2.6 and 2.7
- Cloud providers retire support for older runtime versions, requiring customers to upgrade and apply current security patches

## Azure App Service - Custom Containers

- Azure App Service supports custom containers for Windows and Linux
- Custom containers can provide:
    - A different runtime
    - Additional packages or software
- Custom container deployment workflow:
    - Create a Docker container in the local environment
    - Push the container image to Azure Container Registry (ACR)
    - Deploy the container image to Azure App Service

## Azure App Service - Deployment Slots

- Deployment slots create separate environments for a web application, each associated with a different hostname
- Useful for staging and quality assurance (QA) environments
- Provide a way to quickly clone the production environment for other uses
- Slots can be swapped to perform blue-green deployments
- A staging slot can be promoted to production by swapping it with the production slot
- If a problem occurs after a swap, the slots can be swapped back

## Azure App Service - App Service Environment

- App Service Environment (ASE): fully isolated and dedicated environment for securely running App Service apps at high scale
- ASE v3 is the current version and provides advantages and feature differences over earlier versions
- ASE v1 and v2 were retired on August 31, 2024 and are no longer supported
- An ASE can host:
    - Windows web apps
    - Linux web apps
    - Docker containers
    - Mobile apps
    - Function apps
- Appropriate for workloads requiring:
    - Very high scale
    - Isolation and secure network access
    - High memory utilization
- Multiple ASEs can be deployed within one Azure region or across multiple regions
- Supports horizontal scaling of stateless application tiers for high requests per second (RPS) workloads
- Uses the Isolated pricing tier
- Can be used to implement security architectures
- Access to apps can be gated by upstream devices such as web application firewalls (WAFs)
- Can be deployed across Availability Zones for zone redundancy or pinned to a specific zone
- ASE deployment types:
    - External ASE:
        - Exposes ASE-hosted apps through an internet-accessible IP address
        - Can access resources in its virtual network (VNet) without additional configuration
        - Can access on-premises resources when the VNet is connected to the on-premises network
    - Internal load balancer (ILB) ASE:
        - Exposes ASE-hosted apps through an IP address inside the VNet
        - Uses an internal load balancer as its internal endpoint

## Azure App Service - Deployment

- Deployment: action of pushing changes or updates from a local environment or repository to a remote environment
- Azure App Service deployment methods include:
    - Run from package
    - Deploy a ZIP or Web Application Archive (WAR) file using Kudu
    - Deploy using File Transfer Protocol (FTP)
    - Deploy using cloud sync with Dropbox or OneDrive
    - Deploy continuously from GitHub, Bitbucket or Azure Repos using Kudu and Azure Pipelines
    - Deploy a custom container through a continuous integration and continuous delivery (CI/CD) pipeline from Docker Hub or Azure Container Registry
    - Deploy from local Git using the Kudu build server
    - Deploy code or containers using GitHub Actions
    - Deploy using Azure Resource Manager (ARM) templates
- Run from package:
    - Mounts the ZIP package directly as the read-only `wwwroot` directory
    - Does not copy package files into the `wwwroot` directory
    - Avoids file-lock conflicts and partially updated files during deployment
- File-based deployments that do not use run from package deploy files to:
    - Windows: `D:\home\site\wwwroot`
    - Linux: `/home/site/wwwroot`
- Deploying to the directory used by the running app can cause:
    - Deployment failures due to file-lock conflicts
    - Unpredictable behavior while only some files have been updated
- ZIP and WAR deployment:
    - Uses the Kudu service that powers continuous integration-based deployments
    - Kudu is the open-source deployment engine behind Git deployments in Azure App Service
    - Kudu can also run outside Azure
    - Supports deleting files left over from a previous deployment
    - Can enable the default build process, including package restore
    - Supports custom deployment scripts
    - Provides deployment logs
    - Supports files up to 2,048 MB
    - Can be initiated using:
        - Azure CLI
        - Azure REST API, including cURL
        - Azure portal
- FTP deployment:
    - Requires an FTP client
    - Credentials are available from Deployment Center
    - Files are uploaded directly with the FTP client
- Dropbox and OneDrive cloud-sync deployment:
    - Dropbox is a third-party cloud storage service
    - OneDrive is Microsoft's cloud storage service
    - Configured through Deployment Center
    - Enabling sync creates a deployment folder:
        - OneDrive: `Apps\Azure Web Apps`
        - Dropbox: `Apps\Azure`
    - Updating files in the cloud deployment folder synchronizes them with `/home/site/wwwroot`
