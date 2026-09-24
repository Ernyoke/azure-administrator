# Azure Container Instances (ACI)

- Azure Container Instances (ACIs) allow us to launch containers without the need to worry about configuring or managing the underlying virtual machine
- Azure Container Instances is designed for isolated containers:
    - Simple applications
    - Task automation
    - Build jobs
- Containers can be provisioned within seconds where VMs can take several minutes
- Containers are billed per second for the vCPU and memory allocated to the container group, with no VM to manage
- Containers have granular and custom sizing of vCPUs, Memory and GPUs where VMs sizes are predetermined
- ACI can deploy both Windows and Linux containers
- We can persist storage with Azure Files for ACI containers
- Azure Files volume mounts are supported only for Linux containers
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
    - Always (default): containers are always restarted, suited for long running tasks such as web servers
    - Never: containers run one time only, suited for one-off tasks such as background jobs
    - OnFailure: containers are restarted only when their process exits with an error

## Container Environment Variables

- Environment variables (Env Vars) allow us to pass configuration details to your containers
- Environment variables can be set via the Azure Portal, CLI or PowerShell
- Secured Environment Variables:
    - By default, environment variables are stored in plaintext
    - Use the `--secure-environment-variables` flag to hide values from the container group properties

## Deployment, Sizing and Scaling

- In the Azure portal, select the image source, operating system, CPU, memory, networking and restart policy
- For private registry images, configure registry credentials or a supported managed identity with image pull permissions
- Set CPU and memory requests for each container; group allocation depends on the requests of its containers
- ACI does not provide built-in horizontal autoscaling; use automation to create additional groups or choose Container Apps
- A virtual network deployment uses a subnet delegated to `Microsoft.ContainerInstance/containerGroups`
- Virtual network container groups use private IP addresses rather than a directly assigned public endpoint
- An Azure Files volume can persist data after a container group is deleted
- Check container events and logs for image pull failures, startup errors and exhausted memory
- OnFailure restarts a container only when its process exits with a nonzero exit code