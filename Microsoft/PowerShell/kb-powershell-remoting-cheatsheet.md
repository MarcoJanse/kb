# PowerShell Remoting cheat sheet

- [PowerShell Remoting cheat sheet](#powershell-remoting-cheat-sheet)
  - [Configuration](#configuration)
    - [Client Computer](#client-computer)
  - [KeePass Integration for Credential Management](#keepass-integration-for-credential-management)


## Configuration

### Client Computer

- PowerShell 7.x
- Enable WinRM
  - `Enable-PSRemoting`
- Configure Trusted Hosts if you do not have client certificates

## KeePass Integration for Credential Management

- Install KeePass PowerShell extension
  - `Install-Module SecretManagement.KeePass`
- Register the KeePass vault
- `Register-KeepassSecretVault -Path C:\Users\MarcoJanse\Dropbox\TheFactory\TFMCJ.kdbx -UseMasterPassword`
- For Every use, unlock the KeePass Vault using:
  - `Unlock-SecretVault -Name TFMCJ`
- Get a Credential from KeePass and store it as a variable:
  - `$cred = Get-Secret -Name 'PBL AD admin account`
- Test WinRM
  - `Test-NetConnection -ComputerName pbl-win-bastion-westeurope-cloudapp.azure.com -CommonTCPPort WINRM`
  - `Test-NetConnection -ComputerName pbl-win-bastion.westeurope.cloudapp.azure.com -Port 5986`
