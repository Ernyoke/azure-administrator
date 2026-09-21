# Azure Container Registry

- Azure Container Registry (ACR): managed private registry for container images and related artifacts
- A registry stores images but does not run containers
- Repository: collection of related images within a registry
- Tag: human-readable image version that can be reassigned, such as `web:v1`
- Digest: immutable identifier for a specific image manifest
- Use the registry's login server in image references, such as `<login-server>/web:v1`

## Service Tiers

- Basic: entry-level storage and throughput for smaller workloads
- Standard: increased included storage and throughput
- Premium: adds geo-replication, private endpoints and customer-managed encryption keys
- Geo-replication distributes images across regions while retaining a single registry endpoint

## Authentication and Authorization

- Prefer Microsoft Entra identities and managed identities over stored passwords
- For registries using RBAC (role-based access control) Registry Permissions mode:
    - AcrPull: pull images
    - AcrPush: push and pull images
    - Assign AcrPull to the managed identity of a workload that only needs to download images
- For registries using RBAC Registry + ABAC (attribute-based access control) Repository Permissions mode:
    - Use Container Registry Repository Reader, Writer or Contributor roles instead of AcrPull and AcrPush
    - ABAC conditions can restrict access to individual repositories
    - Repository catalog listing requires the separate Container Registry Repository Catalog Lister role
- The optional admin account provides shared registry-wide credentials and should not be the default for production workloads
- A private endpoint provides private network access but does not replace image pull permissions

## Create and Manage Images

- In the Azure portal, select a subscription, resource group, unique registry name, region and pricing plan
- Push a local image after authenticating and tagging it with the registry's login server
- `az acr login --name <registry>`: authenticate a local container client
- `az acr build --registry <registry> --image web:v1 .`: build and push an image using ACR Tasks
- `az acr import`: copy an image from another registry without downloading it to the local machine
- In the portal, inspect repositories and image tags before selecting an image for ACI or Container Apps
- For image pull failures, check the image name and tag, identity permissions, registry firewall and DNS resolution