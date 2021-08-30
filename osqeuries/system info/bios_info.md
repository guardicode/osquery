## Get BIOS configuration saved in the Windows registry
### Platform: Windows

```sql
SELECT name,data,type FROM registry WHERE key LIKE 'HKEY_LOCAL_MACHINE\HARDWARE\DESCRIPTION\System\BIOS';
```