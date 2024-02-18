# Bicep Deployment Azure Landing Zone for ICTStuff using ALZ-Accelerator

## Index

- [Bicep Deployment Azure Landing Zone for ICTStuff using ALZ-Accelerator](#bicep-deployment-azure-landing-zone-for-ictstuff-using-alz-accelerator)
  - [Index](#index)
  - [Introduction](#introduction)
  - [Prerequisites](#prerequisites)
  - [Preparations](#preparations)
    - [ALZ-Accelerator repo](#alz-accelerator-repo)
    - [RBAC set-up](#rbac-set-up)
      - [SPN for Azure DevOps deploy pipelines](#spn-for-azure-devops-deploy-pipelines)
      - [Add emergency account as owner on tenant root](#add-emergency-account-as-owner-on-tenant-root)
      - [Add PIM enabled group as owner on tenant root](#add-pim-enabled-group-as-owner-on-tenant-root)

## Introduction

These are some personal notes during deployment of the Azure Landing Zone on my personal Azure tenant (ICTStuff) using the [ALZ Accelerator](https://github.com/Azure/ALZ-Bicep/wiki/Accelerator).

## Prerequisites

- A Microsoft 365 developer subscription.
- One or more Visual Studio Enterprise Azure subscriptions that has monthly Azure credits.
- An Entra Id admin account with Global admin permissions and elevated user.access administrator permissions on the Root tenant group in Azure.
- Properly secured M365/Entra ID environment with Conditional Access and - preferably, PIM groups configured.
- An emergency access account with permanent Global admin permissions.
- An Azure DevOps environment with a project that will contain the ALZ-Accelerator repository.
- VSCode, Git, Git Credential Manager, PowerShell v7 and Bicep Cli installed.
  - The PowerShell Az modules installed.
  - The ALZ-PowerShell module installed
- VSCode extensions installed
  - Bicep
  - PowerShell
  - GitLens

## Preparations

### ALZ-Accelerator repo

### RBAC set-up

#### SPN for Azure DevOps deploy pipelines

Create an SPN for use in Azure DevOps deployment pipelines

```powershell
# Connect to Azure
Connect-AzAccount

# Create spn and add it to tenant root as owner
$sp = New-AzADServicePrincipal -DisplayName "spn-azureDeploy" -Role "Owner" -Scope "/"

# Get the spn secret and save this in your keyVault or password vault.
$sp = New-AzADServicePrincipal -DisplayName "spn-azureDeploy" -Role "Owner" -Scope "/"
```

#### Add emergency account as owner on tenant root

While still connected to Azure PowerShell:

```powershell
$user = Get-AzADUser -UserPrincipalName '<your emegency account UPN'
New-AzRoleAssignment -Scope '/' -RoleDefinitionName 'Owner' -ObjectId $user.Id
```

#### Add PIM enabled group as owner on tenant root

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

