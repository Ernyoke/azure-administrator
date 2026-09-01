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
    - Self-service password change and reset for cloud-only users
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
- Tenants and subscriptions:
    - An Azure subscription is a billing and resource management boundary
    - Every subscription trusts exactly one tenant for authentication
    - A tenant can be associated with many subscriptions
    - A subscription can be transferred to another tenant, which removes all existing Azure role assignments
- A user can be a member or guest of multiple tenants and switch between them in the Azure portal

## Custom Domain Names

- Every tenant receives an initial domain in the form `<tenantname>.onmicrosoft.com`
- The initial domain cannot be renamed or deleted
- A custom domain such as `contoso.com` can be added and used in user principal names (UPNs)
- Verification:
    - Entra ID generates a TXT or MX record that must be added at the domain registrar
    - The domain stays Unverified until the record is validated
- A verified domain can be set as the primary domain used by default for new users
- A domain name can be verified in only one tenant at a time
- A domain cannot be deleted while users, groups or applications still reference it

## Microsoft Entra Domain Services

- Provides managed AD DS capabilities without requiring us to deploy, manage or patch domain controllers
- Domain services include:
    - Domain join for Azure virtual machines
    - Group Policy
    - LDAP (Lightweight Directory Access Protocol)
    - Kerberos/NTLM authentication
- Synchronizes users, groups and credentials one way from Microsoft Entra ID to the managed domain
- The managed domain is separate from and not an extension of an on-premises AD DS domain
- Deployed into a dedicated subnet of an Azure virtual network
- Only one managed domain can be created per tenant and it is deployed in a single region
    - Additional regions can be added as replica sets with the Premium SKU
- We do not receive Domain Admin or Enterprise Admin privileges on the managed domain
- Members of the AAD DC Administrators group can join machines to the domain and manage Group Policy for the AADDC Users and AADDC Computers containers
- Users must change their password after Domain Services is enabled so that credential hashes for Kerberos and NTLM are generated
- Synchronizing on-premises identities into the managed domain requires Microsoft Entra Connect to Entra ID first
- Use cases:
    - Lift and shift legacy applications that depend on AD DS protocols to Azure
    - Support Azure workloads that require domain join, LDAP, Kerberos, NTLM or Group Policy

## Entra Connect

- An on-premises Microsoft application that integrates AD DS with Microsoft Entra ID
- Provides users with a common identity for on-premises and cloud resources
- Features:
    - Password hash synchronization (PHS): synchronizes a hash of the on-premises AD DS password hash to Microsoft Entra ID, where sign-in is validated in the cloud
    - Pass-through authentication (PTA): validates passwords directly against on-premises AD DS using lightweight agents, no password hashes are stored in the cloud
    - Federation integration: redirects authentication to on-premises Active Directory Federation Services (AD FS)
    - Synchronization: creates and updates users, groups and other directory objects in Microsoft Entra ID
    - Seamless SSO: signs in users on domain-joined devices without requiring them to re-enter credentials
    - Microsoft Entra Connect Health: monitors on-premises identity infrastructure and requires P1 licensing
- Only one Entra Connect sync server can synchronize to a tenant; additional servers can be installed in staging mode for failover
- The default synchronization interval is 30 minutes
- Password hash synchronization can be enabled as a backup sign-in method for pass-through authentication and federation
- Filtering limits which objects synchronize, based on domain, organizational unit or attribute
- Microsoft Entra Cloud Sync is Microsoft's strategic direction for cloud-managed synchronization:
    - Uses lightweight provisioning agents instead of a full sync server
    - Supports synchronizing from multiple disconnected AD DS forests
    - Does not support device synchronization, pass-through authentication or federation

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
- Ways to add users:
    - Create a cloud identity directly in the tenant
    - Invite an external user as a guest
    - Synchronize from on-premises AD DS with Entra Connect or Cloud Sync
    - Provision from an application or HR source
- Bulk operations use a CSV template and support bulk create, invite, delete and download
- Attributes of synchronized users are managed on-premises and cannot be edited in Entra ID
- Deleted users are kept in a soft-deleted state for 30 days and can be restored during that period
    - After 30 days the user is permanently deleted and cannot be recovered

## Self-Service Password Reset (SSPR)

- Allows users to reset or unlock their own passwords without contacting the helpdesk
- Scope options:
    - None: SSPR is disabled
    - Selected: enabled for a single group
    - All: enabled for every user in the tenant
- Administrators always have SSPR enabled, must use two authentication methods and cannot use security questions
- Authentication methods include the Microsoft Authenticator app, email, mobile phone, office phone and security questions
- Password writeback writes cloud password changes back to on-premises AD DS
    - Requires Entra Connect or Cloud Sync and a P1 license
- Combined registration lets users register for SSPR and MFA at the same time

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

## Entra ID - Group Types

- Security group:
    - Grants access to resources, assigns licenses and applies policies
    - Can contain users, devices, service principals and other groups
- Microsoft 365 group:
    - Used for collaboration and provides a shared mailbox, calendar, SharePoint site and Teams team
    - Can contain users only and cannot contain devices
    - Can include members from outside the organization
- Membership types:
    - Assigned: members are added and removed manually
    - Dynamic User: a membership rule adds and removes users based on user attributes, such as `user.department -eq "Sales"`
    - Dynamic Device: a membership rule adds and removes devices based on device attributes, such as `device.deviceOSType -eq "Windows"`
        - Available for security groups only, not for Microsoft 365 groups
- Dynamic membership requires a P1 license for every member of the group
- Converting a group from assigned to dynamic membership removes the manually added members
- Nested groups are supported, but a group cannot receive licenses or Entra roles through nesting

## Administrative Units

- An administrative unit (AU) is a container of Entra ID objects used to restrict administrative scope
- Can contain users, groups and devices
- An Entra role assigned at AU scope applies only to the objects in that unit
    - Example: a User Administrator scoped to an AU can reset passwords only for users in that unit
- Used to delegate administration by region, department or subsidiary
- Requires a P1 license for every administrator assigned a role scoped to an AU
- Restricted management administrative units block changes to member objects by tenant-level administrators

## Entra ID - Licenses

- Licenses are assigned directly to a user or through group-based licensing
- Group-based licensing requires a P1 license
- A user's usage location must be set before licenses can be assigned, because some services are unavailable in certain countries or regions
- Removing a user from a licensed group removes the licenses inherited from that group
- Licenses assigned directly are kept when group-based licenses are removed

## Security Defaults and Conditional Access

- Security defaults:
    - Free set of baseline identity security settings enabled at tenant level
    - Requires all users to register for MFA with the Microsoft Authenticator app
    - Requires MFA for administrators and for users when needed
    - Blocks legacy authentication protocols
    - Protects privileged activities such as access to the Azure portal
    - Must be disabled before Conditional Access policies can be used
- Conditional Access:
    - Requires a P1 license
    - Works as if-then policies: if the assignments match, then the access controls are enforced
    - Assignments target users and groups, cloud apps or actions, and conditions such as sign-in risk, device platform, location, client app and device state
    - Access controls can grant access with requirements such as MFA, a compliant device or an Entra joined device, or can block access
    - Report-only mode evaluates a policy without enforcing it
    - Emergency access (break-glass) accounts should be excluded from Conditional Access policies

## Entra ID - Assign Access Rights

- Resource access can be assigned through:
    - Direct assignment
    - Group assignment
    - Rule-based assignment: dynamic membership rules add users to groups based on attributes
    - External authority assignment: an external source, such as on-premises AD DS, determines membership

## Entra ID - External ID

- Provides identity solutions for external users
- Two tenant configurations:
    - Workforce tenant: business-to-business (B2B) collaboration where partners, suppliers and guests use their own credentials to access our resources
    - External tenant: business-to-consumer (B2C) customer identity and access management (CIAM) for customer-facing applications
- Supports external identities from other Microsoft Entra tenants, Microsoft accounts, social identity providers and email one-time passcodes
- A B2B guest is created in our tenant as a user object with a user type of Guest
- External collaboration settings control:
    - Who can invite guests, such as all members, only the Guest Inviter role, or nobody
    - What guests can read in the directory
    - Allow or deny lists of partner domains
- Cross-tenant access settings control inbound and outbound B2B collaboration and whether MFA and device claims from the partner tenant are trusted

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
