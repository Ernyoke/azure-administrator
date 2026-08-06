# Azure AD - Device Management

- Device Management is the management of physical devices such as phones, tables, laptops and desktop computes that are granted access to company resources such as printers, cloud resources via device-based Conditional Access
- There are 3 ways to get devices into Azure AD:
    - Azure AD Registered:
        - Personally owned on mobile devices and signed with a personal Microsoft or local account
    - Azure AD Joined:
        - Owned by an organization and signed in with an Azure AD account belonging to the org
        - They exist only in the cloud
        - E.g.: Windows 10, Windows Server 2019 VMs running in Azure (Server core is not supported)
    - Hybrid Azure AD Joined:
        - Owned by an organization and signed in with an AD DS account belonging to the org
        - They exist in the cloud and on-premises
        - E.g.: Windows 7, 8 or 10, Windows Server 2008 or newer

## Azure AD Registered Devices

- Definition: Registered to Azure AD without requiring organizational account to sign into the device
- Primary audience: Bring your own device (BYOD), mobile devices
- Device ownership: user or organization
- Operating systems: Windows 10, iOS, Android and MacOS
- Provisioning:
    - Windows 10 - Settings
    - iOS/Android - Company Portal or Microsoft Authenticator apps
- Device sign in options
    - End-user local credentials, Password, Windows Hello, PIN
    - Biometrics or Pattern for other devices
- Device management
    - Mobile Device Management (example: Microsoft Intune)
    - Mobile Application Management
- Key capabilities
    - SSO to cloud resources
    - Conditional Access when Enrolled into Intune
    - Conditional Access via App protection policy
    - Enables Phone sign in with Microsoft Authenticator app

### Windows Hello

- Gives Windows 10/11 users an alternative way to log into their devices and applications using:
    - Fingerprints
    - Iris scan
    - Facial recognition

### MDM and MAM

- Mobile Device Management (MDM):
    - Allows us to control the entire device, so we can wipe data from it and also reset it to factory settings
- Mobile Application Management (MAM):
    - Publish, push, configure, secure, monitor and update mobile apps for users
- MDM and MAM are managed with Microsoft Intune
- Intune is included in the following licenses:
    - Microsoft 365 E5
    - Microsoft 365 E3
    - Enterprise Mobility + Security E5
    - Enterprise Mobility + Security E3
    - Microsoft 365 Business Premium
    - Microsoft 365 F1
    - Microsoft 365 F3
    - Microsoft 365 Government G5
    - Microsoft 365 Government G3
    - Intune for Education
- Microsoft Intune is part of Microsoft Endpoint Manager
- Microsoft Endpoint Manager and Intune are part of Microsoft Enterprise Mobility + Security (EMS)
- Intune = Endpoint Manager = EMS

### EMS

- Microsoft Enterprise Mobility + Security is an intelligent mobility management and security platform
- Protects and secures organizations and empowers employees to work in new and flexible ways
- EMS is an umbrella of multiple Microsoft and Azure services, such as Azure Active Directory and Microsoft Intune

### Microsoft Authenticator App

- It is an application that allows us to securely sing-in for all online accounts using:
    - MFA
    - Passwordless authentication
    - Password autofill

## AD Joined Devices

- Definition: joined only to Azure AD requiring organizational account to sign-in to the device
- Primary audience: 
    - Suitable for both cloud-only and hybrid organizations
    - Applicable to all users in an organization
- Device ownership: organization
- Operating systems:
    - All Windows 10 devices except Windows 10 Home
    - Windows Server 2019 Virtual Machine running in Azure (Server Core is not supported)
- Provisioning:
    - Self-service: Windows OOBE or Settings, Bulk enrollment, Windows Autopilot
- Device sign in options:
    - Organizational accounts using Password, Windows Hello for Business, FIDO2.0 security keys
- Device management:
    - Mobile Device Management (Microsoft Intune)
    - Co-management with Microsoft Intune and Microsoft Endpoint Configuration Manager
- Key capabilities:
    - SSO to both cloud and on-premises resources
    - Conditional Access through MDM enrollment and MDM compliance evaluation
    - Self-service Password Reset and Windows Hello PIN reset on lock screen
    - Enterprise State Roaming across devices

### FIDO2.0 Security Keys

- FIDO Alliance has published 3 sets of open specification for simpler, stronger user authentication:
    - FIDO Universal Second Factor (FIDO U2F)
    - FIDO Universal Authentication Framework (FIDO UAF)
    - Client to Authenticate Protocols (CTAP)
        - CTAP is complementary to the W3C's Web Authentication (WebAuthn) specification; together they are known as FIDO2