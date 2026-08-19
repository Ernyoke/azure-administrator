# Azure Roles

- Types of Azure roles:
    - Classic subscription administrator roles:
        - This is the original role system
    - Azures roles:
        - This is an authorization system known as Role-Based Access Control (RBAC)
        - Built on top of Azure Resource Manager
    - Azure Active Directory roles:
        - Azure AD roles are used to manage Azure AD resources in a directory

## Identity Access Management (IAM)

- Allows us to create and assign roles to users
- Azure Roles (RBAC system)​:
    - Roles restrict access to resource actions (also known as operations). There are two types of roles:​
        -BuiltInRole – Managed Microsoft roles are read-only pre-created roles for you to use​
        - CustomRole – A role created by you with your own custom logic​
- Role assignment: is when we apply a role to a service principal, user or group
- Deny assignment: 
    - Block users from performing specific actions even if a role assignment grants them access
    - The only way to apply Deny assignments is through Azure Blueprints