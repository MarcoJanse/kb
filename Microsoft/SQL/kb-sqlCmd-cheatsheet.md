# SqlCmd Cheat sheet

## PowerShell connection examples

> **NOTE**
>
> Please make sure that SQL server firewall allows access from your IP when testing/working with SqlCmd

### Using SQL authentication username and password

```powershell
# Variables
$credentials = Get-Credential
$connectionString = 'sqlServerName.database.windows.net'
$database = 'databaseName'
$sqlCmdQuery = "SELECT @@VERSION"

$params = @{
    ServerInstance  = $connectionString
    Database        = $database
    Username        = $credentials.Username
    Password        = $credentials.GetNetworkCredential().Password
    Query           = $sqlCmdQuery
}

Invoke-SqlCmd @params
```

### Using Access token

> **NOTES**
>
> Make sure that you are connected to Az PowerShell with an account that is Microsoft Entra ID admin (direct or via Entra Group)
> Please make sure that your token is only valid for 1 hour

```powershell
$token = (Get-AzAccessToken -ResourceUrl https://database.windows.net).token

Invoke-SqlCmd -ServerInstance $connectionString -Database $database -AccessToken $token -Query "SELECT @@VERSION"
```
