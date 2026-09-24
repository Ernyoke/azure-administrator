# Azure CLI and Azure PowerShell

## Sign-In and Subscription Context

- Azure CLI (Command-Line Interface): uses commands beginning with `az` and can run from Bash or PowerShell
- Azure PowerShell: uses cmdlets from the Az modules and passes objects through the PowerShell pipeline
- Sign in:
    - Azure CLI: `az login`
    - Azure PowerShell: `Connect-AzAccount`
- List subscriptions:
    - Azure CLI: `az account list --output table`
    - Azure PowerShell: `Get-AzSubscription`
- Inspect the current context:
    - Azure CLI: `az account show`
    - Azure PowerShell: `Get-AzContext`
- Select a subscription:
    - Azure CLI: `az account set --subscription <subscription-id>`
    - Azure PowerShell: `Set-AzContext -Subscription <subscription-id>`
- Cloud Shell provides authenticated Bash and PowerShell environments; confirm the active subscription before making changes
- CLI `--query` uses JMESPath to filter JSON output; PowerShell uses object properties and commands such as `Where-Object` and `Select-Object`

## Resource Groups and Moves

- Create a resource group:
    - Azure CLI: `az group create --name <rg> --location <region>`
    - Azure PowerShell: `New-AzResourceGroup -Name <rg> -Location <region>`
- List resource groups:
    - Azure CLI: `az group list`
    - Azure PowerShell: `Get-AzResourceGroup`
- Delete a resource group and its resources:
    - Azure CLI: `az group delete --name <rg>`
    - Azure PowerShell: `Remove-AzResourceGroup -Name <rg>`
- Move supported resources between resource groups or subscriptions:
    - Azure CLI: `az resource move`
    - Azure PowerShell: `Move-AzResource`
- A resource group or subscription move does not relocate resources to another region

## ARM and Bicep Deployments

- ARM (Azure Resource Manager) templates and Bicep use the same deployment engine
- Deploy at resource group scope:
    - Azure CLI: `az deployment group create --resource-group <rg> --template-file main.bicep`
    - Azure PowerShell: `New-AzResourceGroupDeployment -ResourceGroupName <rg> -TemplateFile main.bicep`
- Deploy at subscription scope:
    - Azure CLI: `az deployment sub create --location <region> --template-file main.bicep`
    - Azure PowerShell: `New-AzSubscriptionDeployment -Location <region> -TemplateFile main.bicep`
- Validate a resource group deployment:
    - Azure CLI: `az deployment group validate --resource-group <rg> --template-file main.bicep`
    - Azure PowerShell: `Test-AzResourceGroupDeployment -ResourceGroupName <rg> -TemplateFile main.bicep`
- Preview changes without deploying:
    - Azure CLI: `az deployment group what-if --resource-group <rg> --template-file main.bicep`
    - Azure PowerShell: `Get-AzResourceGroupDeploymentWhatIfResult -ResourceGroupName <rg> -TemplateFile main.bicep`
- Supply a JSON parameter file:
    - Azure CLI: `--parameters parameters.json`
    - Azure PowerShell: `-TemplateParameterFile parameters.json`
- Export existing resources as an ARM template:
    - Azure CLI: `az group export --name <rg>`
    - Azure PowerShell: `Export-AzResourceGroup -ResourceGroupName <rg>`
- Compile Bicep to ARM JSON: `az bicep build --file main.bicep`
- Convert ARM JSON to Bicep: `az bicep decompile --file template.json`
- Decompilation and exported templates can require manual corrections before deployment
- `New-AzResourceGroup` creates a resource group; `New-AzResourceGroupDeployment` deploys resources into one
- Match the command's deployment scope to the template and provide all required parameters
- Complete deployment mode can delete resources missing from the template; incremental is the default

## Role Assignments

- RBAC (role-based access control): grants a principal a role at a particular scope
- Create an assignment:
    - Azure CLI: `az role assignment create --assignee-object-id <object-id> --assignee-principal-type User --role Reader --scope <scope>`
    - Azure PowerShell: `New-AzRoleAssignment -ObjectId <object-id> -RoleDefinitionName Reader -Scope <scope>`
- Inspect assignments:
    - Azure CLI: `az role assignment list --scope <scope> --include-inherited`
    - Azure PowerShell: `Get-AzRoleAssignment -Scope <scope>`
- Remove an assignment:
    - Azure CLI: `az role assignment delete`
    - Azure PowerShell: `Remove-AzRoleAssignment`
- Resource group scope format: `/subscriptions/<subscription-id>/resourceGroups/<rg>`
- Use a principal's object ID, not an application's client ID, when an object ID is required
- Match the principal type to the target identity; managed identities use the service principal type
- Removing a direct assignment does not remove access inherited through a parent scope or group
- Contributor can manage resources but cannot create role assignments

## Policies, Tags and Locks

- Assign a policy definition:
    - Azure CLI: `az policy assignment create`
    - Azure PowerShell: `New-AzPolicyAssignment`
- Apply or update tags:
    - Azure CLI: `az tag update --resource-id <resource-id> --operation Merge --tags Environment=Test`
    - Azure PowerShell: `Update-AzTag -ResourceId <resource-id> -Operation Merge -Tag @{Environment='Test'}`
- Create a resource group delete lock:
    - Azure CLI: `az lock create --name protect --lock-type CanNotDelete --resource-group <rg>`
    - Azure PowerShell: `New-AzResourceLock -LockName protect -LockLevel CanNotDelete -ResourceGroupName <rg>`
- A policy assignment targets a resource scope, not a user
- Merge updates specified tags while retaining others; Replace replaces the existing tag collection
- Locks apply to control-plane operations and do not protect individual blobs from data-plane deletion

## Storage Accounts, Keys and Context

- Create an account:
    - Azure CLI: `az storage account create --name <account> --resource-group <rg> --location <region> --sku Standard_LRS --kind StorageV2`
    - Azure PowerShell: `New-AzStorageAccount -Name <account> -ResourceGroupName <rg> -Location <region> -SkuName Standard_LRS -Kind StorageV2`
- Inspect account keys:
    - Azure CLI: `az storage account keys list`
    - Azure PowerShell: `Get-AzStorageAccountKey`
- Regenerate an account key:
    - Azure CLI: `az storage account keys renew --account-name <account> --resource-group <rg> --key primary`
    - Azure PowerShell: `New-AzStorageAccountKey -Name <account> -ResourceGroupName <rg> -KeyName key1`
- Rotate one key at a time after moving dependent clients to the other key
- Regenerating a key invalidates Shared Key clients and key-signed tokens that depend on it
- Create an identity-based PowerShell storage context: `$context = New-AzStorageContext -StorageAccountName <account> -UseConnectedAccount`
- Pass `-Context $context` to supported storage data cmdlets
- Use CLI `--auth-mode login` for supported Microsoft Entra authorized data operations
- Data access requires appropriate data-plane roles; managing the account does not automatically grant access to blob contents
- Keys and generated access tokens are secrets and must not be committed to source control

## Blob Containers and Shared Access Signatures

- Create a private blob container:
    - Azure CLI: `az storage container create --name <container> --account-name <account> --auth-mode login`
    - Azure PowerShell: `New-AzStorageContainer -Name <container> -Context $context -Permission Off`
- Upload a blob:
    - Azure CLI: `az storage blob upload`
    - Azure PowerShell: `Set-AzStorageBlobContent`
- SAS (shared access signature): grants limited access through a signed token
- Generate a blob SAS:
    - Azure CLI: `az storage blob generate-sas`
    - Azure PowerShell: `New-AzStorageBlobSASToken`
- Create a stored access policy:
    - Azure CLI: `az storage container policy create`
    - Azure PowerShell: `New-AzStorageContainerStoredAccessPolicy`
- Recognize SAS permissions, expiry, protocol restrictions and signing identity
- CLI `--as-user --auth-mode login` generates a user delegation SAS for supported blob operations and requires an expiry
- PowerShell blob SAS generation with an OAuth-based storage context uses a user delegation key
- A user delegation SAS cannot reference a stored access policy; a policy-linked service SAS uses an account key
- A valid SAS does not bypass storage firewall restrictions

## AzCopy

- AzCopy is a separate data transfer utility, not an `az` subcommand or an Az PowerShell cmdlet
- Authenticate for supported identity-based transfers: `azcopy login`
- Copy data recursively: `azcopy copy '<source>' '<destination>' --recursive`
- Synchronize supported locations: `azcopy sync '<source>' '<destination>' --recursive`
- `--delete-destination=true` can delete destination data absent from the source
- Source and destination access require appropriate authorization and network connectivity

## Virtual Machines

- Create a virtual machine (VM):
    - Azure CLI: `az vm create`
    - Azure PowerShell: `New-AzVM`
- Inspect a VM:
    - Azure CLI: `az vm show`
    - Azure PowerShell: `Get-AzVM`
- Deallocate a VM:
    - Azure CLI: `az vm deallocate --resource-group <rg> --name <vm>`
    - Azure PowerShell: `Stop-AzVM -ResourceGroupName <rg> -Name <vm>`
- Start a VM:
    - Azure CLI: `az vm start --resource-group <rg> --name <vm>`
    - Azure PowerShell: `Start-AzVM -ResourceGroupName <rg> -Name <vm>`
- Resize a VM with CLI: `az vm resize --resource-group <rg> --name <vm> --size <size>`
- Resize a VM with PowerShell:
    - Retrieve the object: `$virtualMachine = Get-AzVM -ResourceGroupName <rg> -Name <vm>`
    - Change its size: `$virtualMachine.HardwareProfile.VmSize = '<size>'`
    - Save the change: `Update-AzVM -ResourceGroupName <rg> -VM $virtualMachine`
- `az vm stop` shuts down the VM but leaves compute allocated and billed
- `Stop-AzVM` deallocates by default; `-StayProvisioned` keeps compute allocated
- Resizing causes a restart and can require deallocation when the requested size is unavailable on the current host cluster

## Virtual Networks and Subnets

- Create a virtual network:
    - Azure CLI: `az network vnet create`
    - Azure PowerShell: `New-AzVirtualNetwork`
- Update an existing subnet:
    - Azure CLI: `az network vnet subnet update`
    - Azure PowerShell: `Set-AzVirtualNetworkSubnetConfig`, followed by `Set-AzVirtualNetwork`
- PowerShell configuration sequence:
    - `New-AzVirtualNetworkSubnetConfig`: creates an in-memory subnet configuration
    - `New-AzVirtualNetwork -Subnet`: deploys a new network with the supplied subnet configurations
    - `Get-AzVirtualNetwork`: retrieves an existing network object
    - `Add-AzVirtualNetworkSubnetConfig` or `Set-AzVirtualNetworkSubnetConfig`: changes the local object
    - `Set-AzVirtualNetwork`: saves the updated network to Azure
- Create peering:
    - Azure CLI: `az network vnet peering create`
    - Azure PowerShell: `Add-AzVirtualNetworkPeering`
- Configure peering in both directions and ensure address spaces do not overlap

## Network Security Groups and Routes

- NSG (network security group): filters traffic using prioritized rules
- Add an NSG rule:
    - Azure CLI: `az network nsg rule create`
    - Azure PowerShell: `Add-AzNetworkSecurityRuleConfig`, followed by `Set-AzNetworkSecurityGroup`
- Recognize direction, priority, source, destination, protocol, ports and Allow or Deny access
- Associate the NSG with the intended subnet or network interface; creating rules alone does not attach it
- Add a route:
    - Azure CLI: `az network route-table route create`
    - Azure PowerShell: `Add-AzRouteConfig`, followed by `Set-AzRouteTable`
- Recognize the destination address prefix, next-hop type and next-hop IP for virtual appliances
- Associate a route table with a subnet before expecting its routes to apply
- PowerShell commands ending in `Config` often modify local objects; identify the command that persists the change

## App Service Deployment Slots

- Create a slot:
    - Azure CLI: `az webapp deployment slot create`
    - Azure PowerShell: `New-AzWebAppSlot`
- Swap slots:
    - Azure CLI: `az webapp deployment slot swap`
    - Azure PowerShell: `Switch-AzWebAppSlot`
- Identify the source slot, destination slot and settings configured to remain with a slot
- Deployment slots require a supported App Service plan, normally Standard or higher

## Monitoring and Backup

- Configure diagnostic settings:
    - Azure CLI: `az monitor diagnostic-settings create`
    - Azure PowerShell: `New-AzDiagnosticSetting`
- Identify the source resource, selected log categories and destination workspace, storage account or event hub
- Enable VM backup:
    - Azure CLI: `az backup protection enable-for-vm`
    - Azure PowerShell: `Enable-AzRecoveryServicesBackupProtection`
- Start an on-demand backup:
    - Azure CLI: `az backup protection backup-now`
    - Azure PowerShell: `Backup-AzRecoveryServicesBackupItem`
- PowerShell backup workflows commonly retrieve the vault, policy and protected item before invoking the operation
- Specify the intended vault through supported `-VaultId` parameters or the required vault context
- Enabling protection is different from completing a successful backup job

## Identity and Container Study Notes

- For Microsoft Entra users and groups, study current Microsoft Graph or Microsoft Entra PowerShell instead of retired AzureAD and MSOnline modules
- Microsoft Graph command examples: `Connect-MgGraph`, `New-MgUser`, `New-MgGroup` and `New-MgInvitation`
- Microsoft Graph cmdlets use separate authentication and permissions from Az resource-management cmdlets
- Azure CLI directory command examples: `az ad user create` and `az ad group create`
- The container objective emphasizes portal provisioning; prioritize image access, sizing, networking and scaling over exhaustive command memorization