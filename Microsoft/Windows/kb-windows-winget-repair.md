# WinGet: Repair WinGet

## Introduction

When `Winget upgrade --all` of some other functionality does not work properly anymore there might be a quick fix by repairing WinGet.

## Repair WinGet

Use the following command to try to repair WinGet:

```powershell
Get-AppxPackage -Name 'Microsoft.DesktopAppInstaller' | Reset-AppxPackage
```
