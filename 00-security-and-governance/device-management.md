# Microsoft Entra ID - Device Management

- Device Management is the management of physical devices such as phones, tablets, laptops and desktop computers that are granted access to company resources such as printers, cloud resources via device-based Conditional Access
- Azure AD was renamed to Microsoft Entra ID; the device states are now Microsoft Entra registered, Microsoft Entra joined and Microsoft Entra hybrid joined
- There are 3 ways to get devices into Microsoft Entra ID:
    - Microsoft Entra Registered:
        - Personally owned on mobile devices and signed with a personal Microsoft or local account
    - Microsoft Entra Joined:
        - Owned by an organization and signed in with a Microsoft Entra account belonging to the org
        - They exist only in the cloud
        - E.g.: Windows 10 or 11, Windows Server 2019 or later VMs running in Azure (Server Core is not supported)
    - Microsoft Entra Hybrid Joined:
        - Owned by an organization and signed in with an AD DS account belonging to the org
        - They exist in the cloud and on-premises
        - E.g.: Windows 10 or 11, Windows Server 2016 or newer

## Microsoft Entra Registered Devices

- Definition: Registered to Microsoft Entra ID without requiring organizational account to sign into the device
- Primary audience: Bring your own device (BYOD), mobile devices
- Device ownership: user or organization
- Operating systems: Windows 10 or 11, iOS, Android, macOS and Ubuntu
- Provisioning:
    - Windows 10 or 11 - Settings
    - iOS/Android - Company Portal or Microsoft Authenticator apps
- Device sign in options:
    - End-user local credentials, Password, Windows Hello, PIN
    - Biometrics or Pattern for other devices
- Device management:
    - Mobile Device Management (example: Microsoft Intune)
    - Mobile Application Management
- Key capabilities:
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
- Microsoft Intune is a cloud-based endpoint management service
- Microsoft Endpoint Manager was an umbrella brand for Microsoft Intune and Configuration Manager
- Microsoft Enterprise Mobility + Security (EMS) is a licensing suite that includes Microsoft Intune and Microsoft Entra ID

### EMS

- Microsoft Enterprise Mobility + Security is an intelligent mobility management and security platform
- Protects and secures organizations and empowers employees to work in new and flexible ways
- EMS is an umbrella of multiple Microsoft and Azure services, such as Microsoft Entra ID and Microsoft Intune

### Microsoft Authenticator App

- It is an application that allows us to securely sign in for all online accounts using:
    - MFA
    - Passwordless authentication
    - Password autofill

## Microsoft Entra Joined Devices

- Definition: joined only to Microsoft Entra ID requiring organizational account to sign-in to the device
- Primary audience: 
    - Suitable for both cloud-only and hybrid organizations
    - Applicable to all users in an organization
- Device ownership: organization
- Operating systems:
    - Windows 10 and Windows 11 devices, except Home editions
    - Windows Server 2019 or later virtual machines running in Azure (Server Core is not supported)
- Provisioning:
    - Self-service: Windows OOBE or Settings, Bulk enrollment, Windows Autopilot
- Device sign in options:
    - Organizational accounts using Password, Windows Hello for Business, FIDO2.0 security keys
- Device management:
    - Mobile Device Management (Microsoft Intune)
    - Co-management with Microsoft Intune and Microsoft Configuration Manager
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
- FIDO2 security keys support passwordless, phishing-resistant authentication using public key cryptography
- A security key combined with a PIN or biometric can satisfy multifactor authentication without a password

## Microsoft Entra Hybrid Joined Devices

- Definition: Joined to on-premises AD and Microsoft Entra ID, requiring organizational account to sign into the device
- Primary audience:
    - Suitable for hybrid organizations with existing on-premises AD infrastructure
    - Applicable to all users in an organization
- Device ownership: organization
- Supported operating systems:
    - Windows 10 (Pro, Enterprise, Education — Home not supported)
    - Windows 11 (Pro, Enterprise, Education)
    - Windows Server 2016, 2019, 2022
- Provisioning:
    - Device is domain-joined to on-premises Active Directory
    - Device is automatically registered with Microsoft Entra ID using:
        - Microsoft Entra Connect (formerly Azure AD Connect)
        - AD FS (legacy / less common)
    - Supported provisioning scenarios:
        - IT-managed domain join
        - Windows Autopilot (Hybrid Join scenario)
- Device sign-in options:
    - Users sign in with on-premises Active Directory domain accounts using:
        - Password
        - Windows Hello for Business
        - FIDO2 security keys
- Device management:
    - Group Policy
    - Microsoft Configuration Manager
    - Co-management with Microsoft Intune (recommended)
- Key capabilities:
    - Single Sign-On (SSO) to:
        - On-premises resources
        - Microsoft Entra ID–integrated cloud apps
    - Conditional Access
        - Enforced through Microsoft Entra ID
        - Enhanced when devices are Intune-managed or co-managed
    - Self-Service Password Reset (SSPR)
    - Windows Hello for Business PIN reset
    - Enterprise State Roaming

## Device Identity vs Device Management

- Microsoft Entra registration and join create a device identity in Microsoft Entra ID
- Intune enrollment places a device under Mobile Device Management (MDM)
- A device can have a Microsoft Entra identity without being managed by Intune
- A device can be:
    - Registered or joined
    - Intune-enrolled or unenrolled
    - Compliant or noncompliant
    - Enabled or disabled in Microsoft Entra ID
- Device compliance is evaluated by Intune and can be used as a Conditional Access signal

## Device Registration Settings

- Microsoft Entra ID > Devices > Device settings controls who can join devices
- Users may join devices to Microsoft Entra ID:
    - All: any user can join devices
    - Selected: only members of selected groups can join devices
    - None: users cannot join devices
- Users may register their devices with Microsoft Entra ID is a separate setting from join permissions:
    - All: users can register devices
    - None: users cannot register devices
    - Configuring Microsoft Intune enrollment requires All and makes None unavailable
- Conditional Access can require multifactor authentication for the Register or join devices user action
- Maximum number of devices per user limits how many devices a user can register or join:
    - Default: 50 devices per user
    - Configurable up to 100 devices or Unlimited
    - Does not apply to Microsoft Entra hybrid joined devices
- Additional local administrators on Microsoft Entra joined devices can be selected globally
- Global Administrators and the user who performs the join become local administrators by default

## Intune Enrollment

- Automatic MDM enrollment can enroll Microsoft Entra joined devices into Intune
- Automatic MDM enrollment requires an Intune subscription and Microsoft Entra ID P1 or P2
- MDM user scope determines which users are automatically enrolled:
    - None
    - Some
    - All
- Intune app protection policies are assigned to user groups and can protect supported apps without device enrollment
- Enrollment restrictions can control:
    - Supported device platforms
    - Personally owned devices
    - Operating system versions
    - Maximum number of enrolled devices per user
- Microsoft Entra join and Intune enrollment are separate operations even when automatic enrollment performs both during setup

## Windows Autopilot

- Collection of technologies used to set up and pre-configure new devices for productive use
- Initial deployment:
    - Uses the Original Equipment Manufacturer (OEM)-optimized version of Windows 10 preinstalled on the device
    - Removes the need to maintain custom images and drivers for each device model
    - Transforms the existing Windows 10 installation into a business-ready state without re-imaging
- Devices can be managed after deployment with:
    - Microsoft Intune
    - Windows Update for Business
    - Microsoft Configuration Manager
    - Other similar tools

## Compliance Policies

- Intune compliance policies define requirements a device must meet
- Common compliance settings include:
    - Require encryption
    - Require a password or PIN
    - Block rooted or jailbroken devices
    - Require a minimum operating system version
    - Require an acceptable device threat level
- Devices without an assigned compliance policy can be marked compliant or noncompliant according to the tenant-wide compliance setting
- A compliance policy does not block access by itself
- Conditional Access enforces access decisions by using the device compliance state
- A grace period can give users time to remediate a noncompliant device before it is marked noncompliant

## Conditional Access for Devices

- Conditional Access can use device-based grant controls:
    - Require device to be marked as compliant
    - Require Microsoft Entra hybrid joined device
- Require device to be marked as compliant requires the device to be enrolled and compliant in Intune
- Require Microsoft Entra hybrid joined device is commonly used for organization-owned domain-joined Windows devices
- Policies should exclude emergency access accounts to prevent tenant lockout
- Report-only mode evaluates a policy without enforcing it
- Conditional Access requires Microsoft Entra ID P1 or P2

## Device Administration

- Device administrators can perform common device tasks in the Microsoft Entra admin center
- Cloud Device Administrator can enable, disable and delete devices in Microsoft Entra ID
- Intune Administrator can manage devices, enrollment and compliance in Intune
- Disabling a device prevents it from authenticating with Microsoft Entra ID but keeps the device object
- For lost or stolen devices, disable the device first rather than immediately deleting its identity
- Deleting a device is nonrecoverable and removes its Microsoft Entra identity and attached details, including stored BitLocker recovery keys
- Retire or wipe Intune-managed devices before deleting their Microsoft Entra identity
- Revoking a user's refresh tokens forces reauthentication but does not delete the device
- BitLocker recovery keys for eligible devices can be stored in Microsoft Entra ID and retrieved by authorized users or administrators

## Device Troubleshooting

- Device status can be checked on Windows with `dsregcmd /status`
- Important `dsregcmd /status` fields include:
    - AzureAdJoined: device is joined to Microsoft Entra ID
    - DomainJoined: device is joined to on-premises Active Directory Domain Services
    - WorkplaceJoined: a work or school account is registered on the device
- A hybrid joined device normally reports both AzureAdJoined and DomainJoined as YES
- The Microsoft Entra sign-in logs show Conditional Access results and the device information used during sign-in
- Intune reports show enrollment, configuration and compliance failures
