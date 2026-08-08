# ARM Templates

## Infrastructure as Code

- IaC (Infrastructure as Code): the process of managing and provisioning infrastructure through machine-readable definition files instead of manual hardware configuration or interactive tools
- IaC uses scripts or definition files, such as JSON, to deploy and configure cloud services
- IaC approaches:
    - Declarative: defines the desired end state and lets the platform determine how to reach it
    - Imperative: defines the commands and steps required to reach the desired state

## ARM Templates

- ARM (Azure Resource Manager) templates are JSON files that define Azure resources to provision and services to configure
- ARM templates are declarative and deploy resources in a consistent, repeatable state
- ARM templates provide:
    - Rapid deployment: create, remove and share complete architectures in minutes
    - Consistency: reduce manual configuration mistakes
    - Compliance: establish a known architecture baseline
    - Modularity: split an architecture across linked or nested templates and reuse components
    - Extensibility: run PowerShell or Bash deployment scripts
    - Testing: validate templates with the ARM Template Toolkit (`arm-ttk`)
    - Preview changes: use the what-if operation to see proposed changes before deployment
    - Built-in validation: validate a template before Azure deploys its resources
    - Tracked deployments: retain deployment history and track architecture changes over time
    - Policy as code: deploy Azure Policy definitions and assignments to enforce compliance
    - CI/CD (Continuous Integration and Continuous Delivery) integration
    - Exportable code: export existing resource groups or resources as ARM templates
    - Authoring tools: use Visual Studio Code features to create and validate templates
- ARM templates can deploy nearly all artifacts previously supported by Azure Blueprints
- An ARM template is stored locally or in source control and has no active relationship with resources after deployment
- Azure Blueprints maintained relationships between blueprint assignments and deployed resources, but the service was retired on July 11, 2026

