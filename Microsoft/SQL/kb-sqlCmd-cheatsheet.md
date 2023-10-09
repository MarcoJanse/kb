# SqlCmd Cheat sheet

## PowerShell connection examples

> **NOTE**
>
> Please make sure that SQL server firewall allows access from your IP when testing/working with SqlCmd

### Using SQL authentication username and password

```powershell
# Variables
$credentials        = Get-Credential
$connectionString   = 'sqlServerName.database.windows.net'
$database           = 'databaseName'
$sqlCmdQuery        = "SELECT @@VERSION"

# parameter splatting

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

### Multi-line query example (with validation)

```powershell
# Variables
$ConnectionString   = 'sqlServerName.database.windows.net'
$Database           = 'sqlDatabaseName'
$Token              = (Get-AzAccessToken -ResourceUrl https://database.windows.net).token

# Multi-line query using Access token to map a group to a SQl database with DB Readers

$sqlCmdQuery = @"
IF NOT EXISTS (
    SELECT 1 FROM sys.database_principals WHERE name = 'AD Group Name'
)
    BEGIN
        CREATE USER [AD Group Name] FROM EXTERNAL PROVIDER;
        EXEC sp_addrolemember 'db_datareader', 'AD Group Name';
        SELECT 'User created and role assigned' as ActionTaken;
    END
ELSE
    BEGIN
        SELECT 'User already exists' as ActionTaken;
    END
"@


$params = @{
    ServerInstance  = $connectionString
    Database        = $database
    AccessToken     = $Token
    Query           = $sqlCmdQuery
}

#Execute the SqlCmd command

Invoke-SqlCmd @params
```

### Multi-line query example (without validation)

```powershell
# Variables
$ConnectionString   = 'klik-sql-shared-tst.database.windows.net'
$Database           = 'klik-azsqldb-platform-tst'
$Token              = (Get-AzAccessToken -ResourceUrl https://database.windows.net).token

# Multi-line query using Access token to map a group to a SQl database with DB Readers

$sqlCmdQuery = @"
IF NOT EXISTS (
    SELECT 1 FROM sys.database_principals WHERE name = 'AD Group Name'
)
    BEGIN
        CREATE USER [AD Group Name] FROM EXTERNAL PROVIDER;
        EXEC sp_addrolemember 'db_datareader', 'AD Group Name';
    END
"@


$params = @{
    ServerInstance  = $connectionString
    Database        = $database
    AccessToken     = $Token
    Query           = $sqlCmdQuery
}

#Execute the SqlCmd command

Invoke-SqlCmd @params
```
