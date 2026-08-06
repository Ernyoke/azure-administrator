# Azure AD - Device Management

- Device Management is the management of physical devices such as phones, tables, laptops and desktop computes that are granted access to company resources such as printers, cloud resources via device-based Conditional Access
- There are 3 ways to get devices into Azure AD:
    - Azure AD Registered:
        - Personally ownded on mobile devices and signed with a personal Microsoft or local account
    - Azure AD Joined:
        - Owned by an organization and signed in with an Azure AD account belonging to the org
        - They exist only in the cloud
        - E.g.: Windows 10, Windows Servier 2019 VMs running in Azure (Server core is not supported)
    - Hybrid Azure AD Joined:
        - Owned by an organization and signed in with an AD DS account belonging to the org
        - They exist in the cloud and on-premises
        - E.g.: Windows 7, 8 or 10, Windows Server 2008 or newer