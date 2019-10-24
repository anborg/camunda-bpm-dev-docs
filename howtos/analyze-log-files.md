A collection of methodology, tips and tricks to anaylze log files.

## Reduce a log file by per-line regex matching

Can for example be used to reduce a full log file to only those entries that were written by a particular thread.

### Windows (Powershell)

```
Get-content .\complete-log.txt -ReadCount 1000 | foreach { $_ -match "thread-8" } > reduced-log.txt
```
