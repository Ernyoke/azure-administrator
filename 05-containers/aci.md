# Azure Container Instances (ACI)

- Azure Container Instances (ACIs) allow us to launch containers without the need to worry about configuring or managing the underlying virtual machine
- Azure Container Instances is designed for isolate containers:
    - Simple applications
    - Task automation
    - Bbuild jobs
- Containers can be provisioned within seconds where VMs can take several minutes
- Containers are billed per second where VMs are billed per hour (greater savings)
- Containers have granular and custom sizing of vCPUs, Memory and GPUs where VMs sizes are predetermined
- ACI can deploy both Windows and Linux containers
- WE can persist storage with Azure Files for your ACI containers
- ACIs are accessed via a fully qualified domain name (FQDN) eg customlabel.azureregion.azurecontainer.io

## Container Groups

- Container Groups are collections of containers that get scheduled on the same host machine. The containers in a container group share:
    - Lifecycle
    - Resources
    - Local network
    - Storage volumes
- Container Groups are similar to a Kubernetes pod
- Multi-container groups currently support only Linux containers
- Multi-container groups can be deployed with:
    - ARM templates
    - YAML files

## Container Restart Policies

- A container restart policy specifies what a container should do when their process has completed
- Azure Container Instances has 3 restart-policy options:
    - Always (default) Containers are always restarted. Suited for long running tasks eg. web-servers
    - Never Containers run one time only. Suited for one off tasks. eg. background jobs
    - OnFailure Containers that encounter an error

## Container Environment Variables

- Environment variables (Env Vars) allow us to pass configuration details to your containers
- Environment variables can be set via the Azure Portal, CLI or PowerShell
- Secured Environment Variables:
    - By default, Environment Variables are store in plaintext
    - If we need to secure your environment variables you can use the --secure-environment-variables flag