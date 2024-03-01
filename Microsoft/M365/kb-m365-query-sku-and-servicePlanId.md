# Microsoft 365: Licenses, SKUs and ServicePlan IDs

## Index

- [Microsoft 365: Licenses, SKUs and ServicePlan IDs](#microsoft-365-licenses-skus-and-serviceplan-ids)
  - [Index](#index)
  - [Introduction](#introduction)
  - [Query using PowerShell](#query-using-powershell)
  - [References](#references)

## Introduction

For an Intune project, I wanted to create two dynamic user groups based on licenses for M365 Business Premium and M365 F3 to use these in policy assignments.

In the M365 admin en Entra Id portal, license names have a meaning display name. However, under water it's a mess.
Have a look at the link in the [References](#references) section to see what I mean.
For example, the two licenses I mentioned have these wonderful String_IDs that are so meaningless and are the result of many rebranding and other product changes over time:

- **Product Name:**  _Microsoft 365 Business Premium_
    String ID: _SPB_
    GUID: `cbdc14ab-d96c-4c30-b9f4-6ada7cdc1d46`
- **Product Name:**: _Microsoft 365 F3_
  - String ID: _SPE_F1_
  - GUID: `66b55226-6b4f-492c-910c-a3b7a3c9d993`

> **To-Do** tidy up

## Query using PowerShell

Connect to Azure AD

```PowerShell
Connect-AzureAD
```

```powershell
# Get Skus
$varSkus = Get-AzureADSubscribedSku | Select-Object skuPartnumber, SkuId, ConsumedUnits

# Query Serviceplans
$varServicePlans = Get-AzureADSubscribedSku | ForEach-Object { $_.ServicePlans | Select-Object ServicePlanName, ServicePlanId }
```

If you look at the `$varSkus` you will see the above listed String ID's and the same GUIDS as SkuIds:

```powershell
# Display all Skus
$varSkus

SkuPartNumber          SkuId                                ConsumedUnits
-------------          -----                                -------------
VISIOCLIENT            c5928f49-12ba-48f7-ada3-0d743a3601d5             1
WINDOWS_STORE          6470687e-a428-4b7a-bef2-8a291ad947c9             0
FLOW_FREE              f30db892-07e9-47e9-837c-80727f46fd3d           592
SPB                    cbdc14ab-d96c-4c30-b9f4-6ada7cdc1d46           256
POWERAPPS_VIRAL        dcb1a3ae-b33f-4487-846a-a640262fadf4             1
EXCHANGESTANDARD       4b9405b0-7788-4568-add1-99614e613b69             1
POWER_BI_STANDARD      a403ebcc-fae0-4ca2-8c8c-7a907fd6c235           593
RMSBASIC               093e8d14-a334-43d9-93e3-30589a8b47d0             0
SPE_F1                 66b55226-6b4f-492c-910c-a3b7a3c9d993           346
RIGHTSMANAGEMENT_ADHOC 8c4ce438-32a7-4ac5-91a6-e22ae08d9c8b             0

# Query specific SKU:
Get-AzureADSubscribedSku | Where-Object { $_.SkuPartNumber -eq 'SPE_F1' }
Get-AzureADSubscribedSku | Where-Object { $_.SkuPartNumber -eq 'SPB' }
```

Now, to get all serviceplans inside an SKU:

```powershell
Get-AzureADSubscribedSku | Where-Object { $_.SkuPartNumber -eq 'SPE_F1' } | ForEach-Object { $_.ServicePlans | Select-Object ServicePlanName, ServicePlanId }
```

But you can also use the table in the [References](#references) to see al serviceplans.
I will use the Exchange service plan for my Dynamic group query, as both licenses should have this servicePlan enabled for each user:

```powershell
et-AzureADSubscribedSku | Where-Object { $_.SkuPartNumber -eq 'SPE_F1'} | ForEach-Object { $_.ServicePlans | Where-Object {$_.ServicePlanName -match 'Exchange' } | Select-Object ServicePlanName, ServicePlanId }

et-AzureADSubscribedSku | Where-Object { $_.SkuPartNumber -eq 'SPB'} | ForEach-Object { $_.ServicePlans | Where-Object {$_.ServicePlanName -match 'Exchange' } | Select-Object ServicePlanName, ServicePlanId }
```

For Business Premium, there are 3 serviceplans, I will use the `Exchange_S_STANDARD`
These serviceplans can be used in a Dynamic group query.

```json
// Query SPE_F1 Exchange ServicePlan ID
(user.assignedPlans -any (assignedPlan.servicePlanId -eq "4a82b400-a79f-41a4-b4e2-e94f5787b113" and assignedPlan.capabilityStatus -eq "Enabled"))

// Query SPB Exchange_S_STANDARD Serviceplan ID
(user.assignedPlans -any (assignedPlan.servicePlanId -eq "9aaf7827-d63c-4b61-89c3-182f06f82e5c" and assignedPlan.capabilityStatus -eq "Enabled"))
```

If you want to check a user's licenses by the way, here's how to do that:

```powershell
# Query users
$varUsers = Get-AzureADUser | Where-Object { $_.UserPrincipalName -match "testuser" }

# Query the first user's licenses
Get-azureADUser -ObjectId $varUsers[1].ObjectId | select-Object AssignedLicenses | fl
```

## References

- [Product names and service plan identifiers for licensing](https://learn.microsoft.com/en-us/entra/identity/users/licensing-service-plan-reference)
  