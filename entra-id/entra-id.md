# Microsoft Entra ID

- Entra ID is Microsoft's cloud-based identity and access management service, which helps user, sign-ins and access to Active directory-related resources
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
    - Everything included into free
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
- Allows login to Office 365, Azure and other Microsoft services like Dynamics 365, Teams, Sharepoint and other third party SaaS apps in the Azure Marketplace

## Active Directory vs Entra Id

- Active Directory Domain Services was introduces with Windows 2000 to give organizations the ability to manage multiple on-premises infrastructure components using a single identity per user
- Entra ID takes this approach to the next level by providing organizations with an Identity as a Service (IDaaS) solution for all their apps across cloud and on-premises
- AD terminology:
    - Domain: logical grouping of AD objects on a network
    - Domain Controller (DC): is a server that authenticates user identities and authorizes their access to resources
