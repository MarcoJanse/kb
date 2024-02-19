# Bicep Deployment Azure Landing Zone for ICTStuff using ALZ-Accelerator

## Index

- [Bicep Deployment Azure Landing Zone for ICTStuff using ALZ-Accelerator](#bicep-deployment-azure-landing-zone-for-ictstuff-using-alz-accelerator)
  - [Index](#index)
  - [Introduction](#introduction)
  - [Prerequisites](#prerequisites)
  - [ALZ-Accelerator repo](#alz-accelerator-repo)
    - [Updating ALZ release version](#updating-alz-release-version)
  - [RBAC set-up](#rbac-set-up)
    - [SPN for Azure DevOps deploy pipelines](#spn-for-azure-devops-deploy-pipelines)
    - [Add emergency account as owner on tenant root](#add-emergency-account-as-owner-on-tenant-root)
    - [Add PIM enabled group as owner on tenant root](#add-pim-enabled-group-as-owner-on-tenant-root)
  - [Azure DevOps setup](#azure-devops-setup)
    - [Service Connection](#service-connection)

## Introduction

These are some personal notes during deployment of the Azure Landing Zone on my personal Azure tenant (ICTStuff) using the [ALZ Accelerator](https://github.com/Azure/ALZ-Bicep/wiki/Accelerator).

## Prerequisites

- A Microsoft 365 developer subscription.
- One or more Visual Studio Enterprise Azure subscriptions that has monthly Azure credits.
- An Entra Id admin account with Global admin permissions and elevated user.access administrator permissions on the Root tenant group in Azure.
- Properly secured M365/Entra Id environment with Conditional Access and - preferably, PIM groups configured.
- An emergency access account with permanent Global admin permissions.
- An Azure DevOps environment with a project that will contain the ALZ-Accelerator repository.
- VSCode, Git, Git Credential Manager, PowerShell v7 and Bicep Cli installed.
  - The PowerShell Az modules installed.
  - The ALZ-PowerShell module installed
- VSCode extensions installed
  - Bicep
  - PowerShell
  - GitLens

## ALZ-Accelerator repo

The complete instructions for the initial setup of the repository using Azure DevOps using the ALZ PowerShell module are found here: [Getting Started if you're using Azure DevOps Pipelines | ALZ-Bicep wiki on GitHub](https://github.com/Azure/ALZ-Bicep/wiki/Accelerator#getting-started-if-youre-using-azure-devops-pipelines)

### Updating ALZ release version

- Make sure your local cloned branch has no pending commits and main branch is up-to-date.
- Check the releases page on ALZ-Bicep: [Releases | ALZ-Bicep on GitHub](https://github.com/Azure/ALZ-Bicep/releases)
  - Read all changes since the last version you worked with in the _upstream-releases_ folder.
- Create a new branch from main containing the version number, like: `feat-alz-bicep-v0.17.1`
- Switch to the new branch.
- In the CLI, go up one level and use the following command to add a new version to _upstream-releases_ folder in the ALZ-Accelerator repo
  - `Get-ALZGithubRelease -i "bicep" -o .\ALZ-Accelerator\`
- If you want to download a specific version release use this command:
  - `Get-ALZGithubRelease -i "bicep" -v "0.17.1" -o .\ALZ-Accelerator\`
- After downloading, commit all changes to the feature branch, except the files not under the `upstream-releases` folder, like the `.env` file. (that's probably the only file modified outside of that folder)
- Compare the files in the custom-parameters with the files in the `upstream-releases` folder. Below are the reference files.
  - `\infra-as-code\bicep\modules\policy\assignments\alzDefaults\parameters\alzDefaultPolicyAssignments.parameters.all.json`
  - `\infra-as-code\bicep\modules\policy\definitions\parameters\customPolicyDefinitions.parameters.all.json`
  - `\infra-as-code\bicep\modules\customRoleDefinitions\parameters\customRoleDefinitions.parameters.all.json`
  - `\infra-as-code\bicep\modules\hubNetworking\parameters\hubNetworking.parameters.all.json`
  - `\infra-as-code\bicep\modules\logging\parameters\logging.parameters.all.json`
  - `\infra-as-code\bicep\modules\managementGroups\parameters\managementGroups.parameters.all.json`
  - `\infra-as-code\bicep\orchestration\mgDiagSettingsAll\parameters\mgDiagSettingsAll.parameters.all.json`
  - etc.

## RBAC set-up

### SPN for Azure DevOps deploy pipelines

Create an SPN for use in Azure DevOps deployment pipelines

```powershell
# Connect to Azure
Connect-AzAccount

# Create spn and add it to tenant root as owner
$sp = New-AzADServicePrincipal -DisplayName "spn-azureDeploy" -Role "Owner" -Scope "/"

# Get the spn secret and save this in your keyVault or password vault.
$sp = New-AzADServicePrincipal -DisplayName "spn-azureDeploy" -Role "Owner" -Scope "/"
```

### Add emergency account as owner on tenant root

While still connected to Azure PowerShell:

```powershell
$user = Get-AzADUser -UserPrincipalName '<your emegency account UPN'
New-AzRoleAssignment -Scope '/' -RoleDefinitionName 'Owner' -ObjectId $user.Id
```

### Add PIM enabled group as owner on tenant root

In ICTStuff, I have the following PIM enabled groups: 

- `sg-pim-az-mg-root-owner`
- `sg-pim-az-mg-root-reader`

These have been added to the root management group using Azure PowerShell

```powershell
# Owners
$group = Get-AzADGroup -DisplayName "sg-pim-az-mg-root-owner"
New-AzRoleAssignment -Scope '/' -RoleDefinitionName 'Owner' -ObjectId $group.Id

# Readers
$group = Get-AzADGroup -DisplayName "sg-pim-az-mg-root-reader"
New-AzRoleAssignment -Scope '/' -RoleDefinitionName 'Reader' -ObjectId $group.Id
```

## Azure DevOps setup

### Service Connection

- In the project settings go to service connection and add a new service connection using Azure Resource Manager using Service Principal (manual)
- Fill in the details from your service principal:
  - Environment: Choose "Azure Cloud" unless you're working with a government cloud.
  - Scope level: Choose "Subscription". Even though you're aiming for tenant-level access, Azure DevOps uses the subscription field for the connection context. You will specify the root scope ("/") in your deployments, not here.
  - Subscription ID: Enter a subscription ID that the SPN has access to. This is required for the form, but your SPN's broader permissions will apply at runtime.
  - Subscription Name: Enter a descriptive name.
  - Service principal ID: This is the Application (client) ID of your SPN.
  - Service principal key: The secret associated with your SPN.
  - Tenant ID: The Directory (tenant) ID.
- Verify and save the connection.

