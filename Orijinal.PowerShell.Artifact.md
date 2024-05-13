```
name: Orijinal.PowerShell.Artifact

parameters:
   - name: Command
     description: Enter Cmd/PowerShell Command .
     default: "Get-CimInstance -Namespace root/SecurityCenter2 -ClassName AntiVirusProduct"

sources:
  - precondition:
      SELECT OS From info() where OS = 'windows'

    query: |
      LET PowershellScript = Command + '''| Format-Table | ConvertTo-Json'''
      SELECT * FROM foreach(
        row={
            SELECT Stdout FROM execve(argv=["Powershell", "-ExecutionPolicy",
              "unrestricted", "-c", PowershellScript], length=1000000)
            }, query={
              SELECT * FROM parse_json_array(data=Stdout)
            })
```
