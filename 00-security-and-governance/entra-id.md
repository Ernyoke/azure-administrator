# Microsoft Entra ID

- Entra ID is Microsoft's cloud-based identity and access management service, which helps users sign in and access Active Directory-related resources
- Can be used to login into:
    - External resources:
        - Microsoft 365
        - Azure Portal
        - SaaS applications
    - Internal resources:
        - Applications within our internal networking
        - Access to workstations on-premises

## Entra ID Editions

- Free:
    - MFA
    - SSO
    - Basic Security and Usage Reports
    - Directory synchronizations
- Premium 1 (P1) - included with Microsoft 365 E3 and Business Premium
    - Everything included in free
    - Hybrid architecture
    - Advanced group access
    - Conditional access (it is free with other cloud providers such as AWS or Google Cloud)
- Premium 2 (P2) - Available with Microsoft 365 E5 license
    - Everything from P1
    - Identity protection
    - Identity governance
- Entra Suite:
    - Adds advanced security governance and identity verification solutions
    - Requires P1 subscription (adds additional capacities to an existing Premium edition)

## Entra ID Use Cases

- Entra ID can authorize and authenticate to multiple resources, such as:
    - To our on-premises AD
    - To our web-applications
- Allows users to login with their IdP (Identity Provider) such as Facebook or Google
- Allows login to Office 365, Azure and other Microsoft services like Dynamics 365, Teams, SharePoint and other third party SaaS apps in the Azure Marketplace

## Active Directory vs Entra ID

- Active Directory Domain Services was introduced with Windows 2000 to give organizations the ability to manage multiple on-premises infrastructure components using a single identity per user
- Entra ID takes this approach to the next level by providing organizations with an Identity as a Service (IDaaS) solution for all their apps across cloud and on-premises
- AD terminology:
    - Domain: logical grouping of AD objects on a network
    - Domain Controller (DC): is a server that authenticates user identities and authorizes their access to resources
    - Domain Computer: a computer that is registered with a central authentication database. A domain computer is an AD Object
    - AD Object: is the basic element of an AD such as Users, Groups, Printers, Computers, Shared folders
    - Group Policy Objects (GPO): a virtual collection of policy settings, controls what AD Objects have access to
    - Organizational Units (OU): a subdivision within an AD into which we can place users, groups, computers
    - Directory Service (AD DS): provides the methods for storing directory data and making this data available to network users and administrators. A DS runs on a Domain Controller

## Tenant

- A tenant represents an organization within Entra ID
- It is a dedicated Entra ID service instance
- It is automatically created when we sign up for either: Azure, Intune, Microsoft 365
- Each Entra ID tenant is distinct and separate from other Entra ID tenants, allowing us to manage users, groups and resources within that scope

## Entra Domain Services

- Provides managed domain services
- Domain services include:
    - Domain joins: connect on-premises and cloud-based resources
    - Group policies: apply consistent security settings across users and devices
    - LDAP (Lightweight Directory Access Protocol)
    - Kerberos/NTLM authentication
- No need to deploy, manage or patch DCs in the cloud, ideal for lift-and-shift migrations from on-premises to Azure
- Use cases:
    - Simplifies moving traditional workloads to cloud
    - Maintains compatibility with existing on-premises applications

## Entra Connect

- It is a hybrid service that connects on-premises Active Directory to Entra ID
- Allows for seamless SSO from on-premises workstations to Azure
- Has the following features:
    - Password hash synchronization: sign-in method, synchronizes a hash of user's on-premises AD password with Entra ID
    - Pass-through authentication: sign-in method, allows users to use the same password on-premises and in the cloud
    - Federation integration: supports hybrid environments using on-premises AD FS infrastructure, for certificate renewal
    - Synchronization: responsible for creating users, groups and other objects, ensures on-prem and cloud data matches
    - Health monitoring: robust monitoring and provides a central location in the Azure portal to view this activity (Microsoft Entra Connect Health)

## Entra ID - Users

- Users represent an identity for a person or employee in the domain
- A user has login credentials and can use them to log into Azure portal
- Roles and administrative roles can be assigned to users
- Users can be added to groups
- We can enforce authentication methods such as MFA
- User sign-ins can be tracked
- We can track devices from where the user logs in. We can allow/deny devices
- We can assign Microsoft licenses to users
- Entra ID has two kinds of users:
    - User: belongs to our organization
    - Guest user: belongs to another organization

## Entra ID - Groups

- Groups in Microsoft Entra ID let the resource owner assign a set of access permissions to all the members at once, instead of granting rights individually
- Groups contain:
    - Owners: have permission to add/remove members
    - Members: have permission to do things
- Assignment:
    - We can assign roles directly to a group
    - We can assign applications directly to a group
- Request to join a group:
    - The group owner can let users find their own groups to join, instead of assigning them
- Owners can configure groups to:
    - Automatically accept all join requests, or require approval
    - This feature is only available with Entra ID P1 or P2 licenses

## Entra ID - Assign Access Rights

- There are 4 ways we can assign resource access rights to users:
    - Direct assignment
    - Group assignment
    - Rule-based assignment: we can use rules to assign resources to group members
    - External authority assignments: we can assign access via external sources, e.g. on-premises AD, SaaS app

## Entra ID - External ID

- Lets external users access our apps and resources using their credentials
- Use cases:
    - Enables B2B collaboration by sharing apps with workforce or external users
    - Develop apps for multi-tenant or single tenant environments
    - Allows creation of custom-branded consumer apps
    - Allows managing customer identities and access for consumer apps

## Service Principal

- It is a security identity used by registered apps or service to access specific Azure resources
- Service principals define:
    - Who can access the application
    - What resources the application can access
- A service princial is created in each tenant where the application is used and references the globally unique application obejct
    - The ApplicationID represents the global application across all tenants
    - The ObjectD is an unique value for an application object
    - For Registered Apps, the service principal will be created when a user from a tenant consents to the using the app or API