# Azure Resource Manager (ARM)

- It is a deployment and management service for Azure
- It enables us to create, update, and delete resources in our Azure account
- ARM is a management layer that allows us to:
    - Create, update, and delete resources
    - Apply management features such as access control, locks, and tags
    - Write infrastructure as code (IaC) using JSON templates
- ARM features:
    - Subscriptions
    - Management Groups
    - Resource Groups
    - Resource Providers
    - Resource Locks
    - Azure Blueprints
    - Resource Tags
    - Azure role-based access control (Azure RBAC)
    - Azure Policy
    - ARM templates

## ARM - Scoping

- A scope is a boundary control for Azure resources
- It is a way to govern resources by placing them in logical groupings and applying controls such as policies
- Scopes are:
    - Management group: a logical container for subscriptions and other management groups
    - Subscription: a logical container for resources that is linked to an Azure account and billing
    - Resource group: a logical container for related resources

## Subscriptions

- Azure offers multiple subscription types, such as:
    - Free Trial
    - Pay-As-You-Go
    - Azure for Students
- At the subscription level, we can:
    - Set resource tags
    - Manage access control
    - Create resource groups

## Management Groups

- Allow subscriptions to be organized into a hierarchical structure
- Each Microsoft Entra tenant has a single top-level management group called the tenant root group
- Subscriptions and child management groups inherit governance settings applied to their parent management group

## Resource Groups and Resource Providers

- Resource group: a container that holds related resources for an Azure solution
- Resource provider: a service that supplies Azure resources, such as `Microsoft.Compute`
- A subscription must be registered with a resource provider before its resources can be used
- Many resource providers are registered automatically

## Resource Tags

- A tag is a key/value pair that we can assign to Azure resources
- Tags allow us to organize resources in the following ways:
    - Resource management: specific workloads and environments, such as a development environment
    - Cost management and optimization: cost tracking, budgets, alerts
    - Operations management: business commitments and service-level agreement (SLA) operations
    - Security: classification of data and security impact
    - Automation
    - Workload optimization

## Resource Locks

- An administrator can lock a subscription, resource group, or resource to prevent accidental deletion or modification of critical resources
- In the Azure portal, we can set the following lock levels:
    - `CanNotDelete`: authorized users can still read and modify the resource, but they cannot delete it
    - `ReadOnly`: authorized users can read the resource, but they cannot delete or update it

## Azure Blueprints

- **Azure Blueprints (Preview)** enable **quick creation** of **governed subscriptions**
- Blueprints compose artifacts based on common or organization-based patterns into reusable packages
- The service is designed to help with *environment setup*
- Blueprints are a declarative way to orchestrate the deployment of resource templates and other artifacts such as:
    - Role assignments
    - Policy assignments
    - Azure Resource Manager templates (ARM templates)
    - Resource groups
- The Azure Blueprints service is backed by the globally distributed **Azure Cosmos DB**
- Blueprint objects are replicated to multiple Azure regions
- Phased retirement began on July 31, 2026, and the service retires on January 31, 2027
- Migrate blueprint definitions and assignments to Azure Deployment Stacks and template specs
- **ARM templates vs Azure Blueprints**:
    - Nearly everything included for deployment in Azure Blueprints can be accomplished with an ARM template
    - **ARM templates**:
        - Can be stored locally, in source control, or as Azure template specs
        - Standalone templates have no active connection or relationship to deployed resources
    - **Azure Blueprints**:
        - Maintain a relationship between the blueprint definition, which describes what should be deployed, and the blueprint assignment, which records what was deployed
        - Can upgrade several subscriptions at once when they are governed by the same blueprint
        - Support **improved tracking and auditing of deployments**

## Azure Deployment Stacks

- Azure deployment stack: an Azure resource that manages a collection of Azure resources as a single unit
- Uses a Bicep file, ARM template or template spec to define the managed resources
- Unlike a standard ARM template deployment, a deployment stack maintains a relationship with its managed resources
- A stack can be created at resource group, subscription or management group scope
- Updating the template and redeploying the stack adds, updates or removes managed resources
- `actionOnUnmanage`: controls what happens when a resource is removed from the template or the stack is deleted:
    - `detachAll`: stops managing resources and resource groups without deleting them
    - `deleteResources`: deletes managed resources but detaches resource groups
    - `deleteAll`: deletes managed resources and managed resource groups
- Detached resources continue to exist in Azure but are no longer managed by the stack
- Deny settings protect explicitly managed resources from unauthorized control-plane operations:
    - `None`: does not deny operations
    - `DenyDelete`: prevents deletion
    - `DenyWriteAndDelete`: prevents modification and deletion
- Deny settings can apply to child scopes and can exclude specific actions or Microsoft Entra principals
- Deny settings do not protect data-plane operations or resources created implicitly by another resource
- Built-in deployment stack roles:
    - Azure Deployment Stack Contributor: manages stacks but cannot create or delete their deny assignments
    - Azure Deployment Stack Owner: manages stacks and their deny assignments
- Deployment stacks can be managed through the Azure portal, Azure CLI or Azure PowerShell

## Moving Resources to a New Resource Groups

- Reference: https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/move-resource-group-and-subscription?tabs=azure-cli
- Azure resources can't be moved if a read-only lock exists on the source, destination resource group, or subscription
- Limitations for moving App Services across subscriptions/resource groups:
    - The destination resource group must not have any existing App Service resources. App Service resources include:
        - Web apps
        - App Service plans
        - Uploaded or imported TLS/SSL certificates
        - App Service Environments
    - We must move all App Service resources in the resource group together
    - We cannot move App Service Environments to a new resource group or subscription. We can recreate the environment and use backup and restore feature
    - We cannot move apps with private endpoints and with virtual network integration
    - We can only move App Service resources from the resource group where we originally created them