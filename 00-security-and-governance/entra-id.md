# Microsoft Entra ID

- Microsoft Entra ID is Microsoft's cloud-based identity and access management service
- Can be used to sign in to:
    - External resources:
        - Microsoft 365
        - Azure portal
        - SaaS applications
    - Internal resources:
        - Applications within an internal network
        - Access to workstations on-premises

## Entra ID Editions

- Free:
    - Security defaults with multifactor authentication (MFA)
    - Single sign-on (SSO)
    - Basic security and usage reports
    - Directory synchronization
- Premium P1:
    - Includes Free features
    - Included with Microsoft 365 E3 and Microsoft 365 Business Premium
    - Conditional Access
    - Dynamic groups and group-based licensing
    - Hybrid identity capabilities
- Premium P2:
    - Includes P1 features
    - Included with Microsoft 365 E5
    - Microsoft Entra ID Protection
    - Privileged Identity Management (PIM)
    - Access reviews and entitlement management capabilities
- Entra Suite:
    - Includes Microsoft Entra Private Access, Internet Access, ID Governance, ID Protection and premium Verified ID capabilities
    - Requires an existing P1 subscription or a package that includes P1

## Entra ID Use Cases

- Authenticates identities and authorizes access to cloud and on-premises applications
- Supports hybrid identity with on-premises Active Directory Domain Services (AD DS)
- Microsoft Entra External ID allows external users to sign in with an identity provider (IdP), such as Google or Facebook
- Provides SSO to Microsoft 365, Azure, Dynamics 365 and third-party software as a service (SaaS) applications

## Active Directory vs Entra ID

- AD DS provides centralized identity, authentication and management for on-premises domain resources
- Microsoft Entra ID provides identity as a service (IDaaS) for cloud and on-premises applications
- AD terminology:
    - Domain: logical grouping of AD objects on a network
    - Domain controller (DC): server that authenticates identities and authorizes access to domain resources
    - Domain computer: computer joined to an AD DS domain and represented by a computer object
    - AD object: directory entry such as a user, group, computer or printer
    - Group Policy Object (GPO): collection of policy settings applied to users and computers
    - Organizational unit (OU): container used to organize users, groups, computers and other objects within a domain
    - Directory service: stores directory data and makes it available to users, administrators and applications

## Tenant

- A tenant represents an organization within Entra ID
- It is a dedicated Entra ID service instance
- Created automatically when an organization signs up for a Microsoft cloud service such as Azure, Intune or Microsoft 365
- Each tenant is distinct from other tenants and provides a boundary for managing identities, applications and access

## Microsoft Entra Domain Services

- Provides managed AD DS capabilities without requiring us to deploy, manage or patch domain controllers
- Domain services include:
    - Domain join for Azure virtual machines
    - Group Policy
    - LDAP (Lightweight Directory Access Protocol)
    - Kerberos/NTLM authentication
- Synchronizes users, groups and credentials one way from Microsoft Entra ID to the managed domain
- The managed domain is separate from and not an extension of an on-premises AD DS domain
- Use cases:
    - Lift and shift legacy applications that depend on AD DS protocols to Azure
    - Support Azure workloads that require domain join, LDAP, Kerberos, NTLM or Group Policy

## Entra Connect

- An on-premises Microsoft application that integrates AD DS with Microsoft Entra ID
- Provides users with a common identity for on-premises and cloud resources
- Features:
    - Password hash synchronization: synchronizes a hash of a user's on-premises AD DS password hash with Microsoft Entra ID
    - Pass-through authentication: sign-in method, allows users to use the same password on-premises and in the cloud
    - Federation integration: configures and manages hybrid environments that use Active Directory Federation Services (AD FS)
    - Synchronization: creates and updates users, groups and other directory objects in Microsoft Entra ID
    - Seamless SSO: signs in users on domain-joined devices without requiring them to re-enter credentials
    - Microsoft Entra Connect Health: monitors on-premises identity infrastructure and requires P1 licensing
- Microsoft Entra Cloud Sync is Microsoft's strategic direction for cloud-managed synchronization

## Entra ID - Users

- A user object represents an identity for a person
- A user can have credentials used to sign in to the Azure portal and other applications
- Microsoft Entra roles and Azure roles can be assigned to users
- Users can be added to groups
- We can enforce authentication methods such as MFA
- User sign-ins can be tracked
- Sign-in and device information can be used by Conditional Access policies to allow or block access
- We can assign Microsoft licenses to users
- Entra ID has two kinds of users:
    - User: belongs to our organization
    - Guest user: belongs to another organization

## Entra ID - Groups

- Groups allow access and licenses to be assigned to multiple users instead of individually
- Groups contain:
    - Owners: manage the group and its membership
    - Members: receive access assigned to the group
- Assignment:
    - Applications and licenses can be assigned to groups
    - Microsoft Entra roles can be assigned only to role-assignable groups
- Request to join a group:
    - Owners can allow users to request membership instead of assigning them directly
    - Requests can be approved automatically or require owner approval
    - Self-service group management requires P1 or P2 licensing

## Entra ID - Assign Access Rights

- Resource access can be assigned through:
    - Direct assignment
    - Group assignment
    - Rule-based assignment: dynamic membership rules add users to groups based on attributes
    - External authority assignment: an external source, such as on-premises AD DS, determines membership

## Entra ID - External ID

- Provides identity solutions for external users
- Use cases:
    - External tenants: business-to-business (B2B) collaboration with partners, suppliers and guests
    - External tenants: business-to-consumer (B2C) customer identity and access management (CIAM) for customer-facing applications
    - Supports external identities from other Microsoft Entra tenants, social identity providers and one-time passcodes

## Service Principal

- A service principal is the local representation of an application in a Microsoft Entra tenant
- Defines:
    - Who can access the application
    - What the application can access in that tenant
- An application object is the global definition of an application and exists in its home tenant
- A service principal is created in each tenant where the application is used and references the application object
    - Application (client) ID: globally unique identifier shared by the application object and its service principals
    - Object ID: unique identifier for a specific application object or service principal object in a tenant
    - App registration creates an application object and a service principal in the home tenant
    - Consent to a multitenant application creates a service principal in the consuming tenant

## Managed Identities

- Managed identities provide Azure resources with automatically managed identities in Microsoft Entra ID
- Azure resources use managed identities to request tokens for services that support Microsoft Entra authentication
- Benefits:
    - Removes the need to store or manually manage credentials
    - Prevents sensitive authentication details from being shared over the network
    - Simplifies and automates authentication between Azure resources
- Example: Azure Front Door can use a managed identity to retrieve a secret from Azure Key Vault without exposing credentials
- Types:
    - System-assigned identity:
        - Enabled on and tied to a specific Azure resource
        - Only that Azure resource can use the identity to request tokens from Entra ID
        - Deleted when the Azure resource is deleted
    - User-assigned identity:
        - Created as a separate Azure resource
        - Can be assigned to and shared across multiple Azure resources
        - Its lifecycle is independent of the resources that use it
