# Azure Roles

- Types of Azure roles:
    - Classic subscription administrator roles:
        - Original role system, fully retired as of May 2026
    - Azure roles:
        - Authorization system known as Azure role-based access control (Azure RBAC)
        - Built on top of Azure Resource Manager
    - Microsoft Entra roles:
        - Used to manage Microsoft Entra resources in a directory

## Identity and Access Management (IAM)

- The Access control (IAM) page is used to manage access to Azure resources
- Azure role definitions have two types:
    - Built-in role: a Microsoft-managed role that cannot be modified
    - Custom role: a role created with permissions that meet specific requirements
- Role assignment: attaches a role definition to a user, group, service principal or managed identity at a particular scope
- Deny assignments:
    - Block users from performing specific actions even if a role assignment grants them access
    - Cannot be created directly and are created and managed by Azure
    - Deny settings on deployment stacks create deny assignments

## Role-Based Access Control (RBAC)

- Azure role-based access control (Azure RBAC) helps us manage who has access to Azure resources, what they can do with those resources, and what areas they have access to
- Role assignments are the way access to Azure resources is controlled
- A role assignment consists of three elements: security principal, role definition and scope
- Security principal: an identity requesting access to an Azure resource
    - Can be a user, group, service principal or managed identity
- Scope: the set of resources that access for the role assignment applies to
    - Controls access at the management group, subscription, resource group or resource level
- Role definition: a collection of permissions
    - Lists the operations that can be performed, such as read, write and delete
    - Can be high-level, such as Owner, or specific, such as Virtual Machine Reader
- Azure provides built-in roles and supports custom roles
- Five fundamental built-in roles:
    - Owner: full access to all resources, including the ability to assign roles
    - Contributor: full access to manage resources, but cannot assign roles
    - Reader: can view resources but cannot make changes
    - Role Based Access Control Administrator: can manage Azure RBAC role assignments but cannot manage access through services such as Azure Policy
    - User Access Administrator: can manage user access and read control-plane information for all Azure resource types, but has no data-plane access to values such as secrets

## Microsoft Entra Roles

- Microsoft Entra roles manage Microsoft Entra resources in a directory, such as:
    - Creating or editing users
    - Assigning administrative roles
    - Resetting user passwords
    - Managing user licenses
    - Managing domains
- Important built-in Microsoft Entra roles:
    - Global Administrator: manages all administrative features in Microsoft Entra ID and Microsoft services that use Microsoft Entra identities
        - Can reset passwords for all users and administrators
        - Does not have access to Azure resources by default, but can elevate access
    - User Administrator: manages users and groups, assigns licenses and resets passwords for permitted users and limited administrators
        - Cannot manage MFA or reset credentials for every administrator
    - Billing Administrator: makes purchases, manages subscriptions and support tickets and monitors service health
- Built-in Microsoft Entra roles are free
- Custom Microsoft Entra roles require a Microsoft Entra ID P1 license for every user assigned a custom role

## Anatomy of an Azure Custom Role

- Property names and structure differ between Azure PowerShell and Azure CLI output
- `Name` (`roleName`): display name of the custom role
- `Id` (`name`): unique role definition ID, automatically generated when a new role is created with Azure PowerShell or Azure CLI
- `IsCustom` (`roleType`): identifies a custom role as `true` in Azure PowerShell or `CustomRole` in Azure CLI
- `Description` (`description`): description of the custom role
- `Actions` (`actions`): control-plane operations that the role allows
- `NotActions` (`notActions`): control-plane operations excluded from `Actions`
    - Does not create a deny rule and another role assignment can still grant the excluded operation
- `DataActions` (`dataActions`): data-plane operations that the role allows, such as reading blob or queue data
- `NotDataActions` (`notDataActions`): data-plane operations excluded from `DataActions`
    - Does not create a deny rule and another role assignment can still grant the excluded operation
- `AssignableScopes` (`assignableScopes`): scopes where the custom role is available for assignment
    - Supports management group, subscription and resource group scopes
    - Can contain up to 2000 scopes but only one management group
    - Cannot use the root scope (`/`) or wildcards
    - A custom role containing `DataActions` cannot be assigned at management group scope
- `Actions`, `NotActions`, `DataActions` and `NotDataActions` support one wildcard (`*`) in an operation string
    - A wildcard matches every operation represented by its position in the string
    - Explicit permissions are recommended because a wildcard can grant future operations added by Azure

## Azure Policy vs Azure Roles

- Azure Policy:
    - Enforces organizational standards and assesses resource compliance
    - Evaluates resource properties represented in Azure Resource Manager and properties exposed by some resource providers
    - Primarily evaluates whether resource state complies with business rules, regardless of who made the change or their permissions
    - Can audit, deny or modify resource changes and can deploy related resources through policy effects
    - Can block certain resource actions with the `denyAction` effect
    - Can block a create or update that would produce a non-compliant resource even when Azure RBAC permits the user to perform it
- Azure role-based access control (Azure RBAC):
    - Controls who can access Azure resources, what actions they can perform and at which scopes
    - Focuses on authorization for user, group, service principal and managed identity actions
- Azure Policy and Azure RBAC work together to provide governance and access control

## Microsoft Entra Roles vs Azure Roles

- Microsoft Entra roles:
    - Control access to Microsoft Entra ID resources
    - Examples include users, groups, licenses, application registrations and enterprise applications
    - Some roles also manage billing-related tasks for Microsoft services
- Azure roles:
    - Control access to Azure resources through Azure RBAC
    - Examples include virtual machines, databases, storage accounts and virtual networks
- Microsoft Entra roles and Azure roles use separate authorization systems and do not grant access across both systems by default
- A Global Administrator does not have access to Azure resources by default
- A Global Administrator can gain access to Azure resources by:
    - Receiving an explicit Azure role assignment, such as Owner or User Access Administrator, at an appropriate scope
    - Elevating access, which assigns User Access Administrator at root scope (`/`) and enables management of access to all Azure subscriptions and management groups in the tenant
- Elevated access should be removed after the required access changes are complete