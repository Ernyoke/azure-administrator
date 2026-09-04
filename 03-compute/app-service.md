# Azure AppService

- Azure App Service is a PaaS (Platform as a Service) offering that lets us deploy and manage web apps without managing the underlying infrastructure
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
- Slots require the Standard tier or higher and run on the same App Service Plan as the production slot
- Slots can be swapped to perform blue-green deployments
- A staging slot can be promoted to production by swapping it with the production slot
- Swapping warms up the target slot before the swap so there is no downtime
- If a problem occurs after a swap, the slots can be swapped back
- Auto swap automatically swaps a slot into production after a successful deployment and warm-up
- Some settings are swapped and others stay with the slot:
    - Swapped: general settings, connection strings, handler mappings, public certificates, WebJobs content
    - Not swapped (slot settings): publishing endpoints, custom domain names, TLS/SSL bindings, scale settings, always on, diagnostic log settings
- App settings and connection strings can be marked as a deployment slot setting so they stay with the slot
- Traffic can be routed to a slot by percentage for testing in production

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

## Azure App Service Plan

- An App Service Plan defines the region, operating system, instance size and number of instances that host our apps, and it is what we are billed for
- Apps in the same plan share the same compute resources, so the plan is the unit of scale
- Scaling the plan up or out affects every app in that plan
- Windows and Linux apps cannot share the same App Service Plan
- We are billed for the plan even when it contains no apps, except for the Free tier
- There are three pricing tiers: Shared, Dedicated and Isolated (v2)
- Shared tier:
    - Apps run on shared virtual machines alongside apps from other customers
    - Free plan:
        - 1 GB disk space
        - Up to 10 apps per plan
        - No SLA for availability
        - Compute quota of 60 CPU minutes per day per app
        - No custom domains, no TLS/SSL bindings, no scaling
    - Shared plan:
        - 1 GB disk space
        - Up to 100 apps per plan
        - No SLA for availability
        - Compute quota of 240 CPU minutes per day per app
        - Supports custom domains but not TLS/SSL bindings
    - Shared tiers do not support Linux-based instances
- Dedicated tier:
    - Apps run on dedicated virtual machines that only host our apps
    - Basic plan:
        - 10 GB disk space
        - Unlimited apps
        - Three sizes (B1, B2, B3) with varying compute, memory and disk storage
        - Scales out manually to 3 instances
        - No autoscale, no deployment slots, no Traffic Manager staging support
    - Standard plan:
        - 50 GB disk space
        - Three sizes (S1, S2, S3)
        - Scales out to 10 instances with autoscale
        - Supports 5 deployment slots, daily backups and Traffic Manager
    - PremiumV2 and PremiumV3 plans:
        - 250 GB disk space
        - Scale out to 30 instances
        - Support 20 deployment slots and more frequent backups
        - Multiple hardware sizes, P1v2 to P3v2 and P1v3 to P3v3
        - PremiumV3 offers the latest hardware generation with faster processors and better scaling
- Dedicated and Isolated tiers have an SLA of 99.95% availability
- Isolated tier:
    - Runs on dedicated virtual machines inside an App Service Environment (ASE) deployed into our own virtual network
    - Provides full network and compute isolation
    - Scales out to 100 instances
    - Supports 20 deployment slots
    - Isolated v2 is used with ASE v3 and is recommended for new workloads, the original Isolated tier is still supported but not advised for new deployments

## Azure App Service - Scaling

- Scale up: move the plan to a larger pricing tier or instance size to get more CPU, memory and disk, and unlock features such as slots and custom domains
- Scale out: increase the number of instances running the apps in the plan
- Manual scale: we set a fixed instance count
- Autoscale: rules add or remove instances based on a metric or a schedule
- Autoscale requires the Standard tier or higher
- Autoscale rules are based on:
    - Metrics such as CPU percentage, memory percentage, HTTP queue length and data in or out
    - A schedule for recurring or one-off time windows
- Autoscale settings define minimum, maximum and default instance counts
- Scale-out instances all run the same apps, and App Service load balances requests across them

## Azure App Service - WebJobs

- WebJobs: App Service feature that runs a program or script in the same instance as a web app, API app or mobile app
- There is no additional cost to use WebJobs
- All App Service Plans support WebJobs
- WebJobs are supported on both Windows and Linux apps
- WebJobs are not supported in custom Linux containers based on Alpine Linux, which includes the Java 8 and Java 11 Linux runtime stacks
- Supported file types on Windows:
    - .cmd, .bat, .exe using Windows cmd
    - .ps1 using PowerShell
    - .sh using Bash
    - .js using Node.js
    - .jar using Java
- Supported file types on Linux:
    - .sh using Bash
    - Any script written in the language runtime of the app, such as .py for a Python app
- A WebJob is uploaded as a .zip file containing the executable or script and its supporting files
- WebJob types:
    - Continuous:
        - Starts immediately when the WebJob is created and typically runs an endless loop until stopped
        - Runs on all instances of the app by default
        - Supports remote debugging
        - Deployed under `\site\wwwroot\app_data\Jobs\Continuous`
    - Triggered:
        - Runs only when triggered manually or on a schedule
        - Runs on a single instance selected by Azure for load balancing
        - Exposes a webhook that can be called to start the job
        - Does not support remote debugging
        - Deployed under `\site\wwwroot\app_data\Jobs\Triggered`
- Scheduled WebJobs are triggered WebJobs that use an NCRONTAB expression, which defaults to UTC
- The Always On setting should be enabled so continuous and scheduled WebJobs run reliably, and it requires the Basic tier or higher
- WebJob scale, available only for continuous WebJobs:
    - Multi instance: runs the WebJob on all instances of the App Service Plan and is not available in the Free or Shared tiers
    - Single instance: keeps only a single copy of the WebJob running regardless of the instance count of the App Service Plan
- The `WEBJOBS_STOPPED` app setting with a value of 1 stops all WebJobs on the site
- The `WEBJOBS_DISABLE_SCHEDULE` app setting with a value of 1 disables triggered WebJobs, useful on a staging slot when marked as a deployment slot setting
