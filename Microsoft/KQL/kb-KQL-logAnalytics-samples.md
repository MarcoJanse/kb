# KQL samples for Log Analytics workspace

## Get all connected computers

```sql
Heartbeat
| where ComputerEnvironment contains "Azure"
| summarize arg_max(TimeGenerated, *) by Computer
```
