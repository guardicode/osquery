## Check SMBv1 is still supported on the host
### Platform: Windows

```sql
SELECT 
  CASE cnt
  WHEN 1 THEN "DISABLED"
  ELSE "ENABLED"
   END "SMBv1 Status"
  FROM (SELECT *,COUNT(*) AS cnt
  FROM registry
 WHERE path = 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters\SMB1' AND data != 1);
 ```