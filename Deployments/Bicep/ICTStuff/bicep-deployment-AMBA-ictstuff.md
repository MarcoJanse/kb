# Bicep Deployment Azure Monitoring Baseline Alerts (AMBA) for ICTStuff

## Index

- [Bicep Deployment Azure Monitoring Baseline Alerts (AMBA) for ICTStuff](#bicep-deployment-azure-monitoring-baseline-alerts-amba-for-ictstuff)
  - [Index](#index)
  - [Introduction](#introduction)
  - [Prerequisites](#prerequisites)
  - [Deployment steps](#deployment-steps)

## Introduction

## Prerequisites

## Deployment steps

- Log in using `Connect-AzAccount`
- Make sure a subscription is selected that's part of the correct Azure tenant.
  - You can use `Get-AzContext` to view that
    - If not, use the following to set the right subscription:

```powershell
Set-AzContext -Subscription (Get-AzSubscription | Where-Object Name -match '<unique part of subscription name>').Id
```

```powershell
$inputObject = @{
  DeploymentName        = 'ictstuff-AmbaDeploy-{0}' -f (-join (Get-Date -Format 'yyyyMMddTHHMMssffffZ')[0..63])
  TemplateUri           = "https://raw.githubusercontent.com/Azure/azure-monitor-baseline-alerts/main/patterns/alz/alzArm.json"
  TemplateParameterFile = "patterns\alz\alzArm.ictstuff.param.json"
  Location              = "westeurope"
  ManagementGroupId     = "alz-mg"
}
```

```powershell
New-AzManagementGroupDeployment @inputObject -WhatIf
```
