<p align="left">
  <a href="https://www.guardicore.com/">
    <img src="https://www.guardicore.com/wp-content/uploads/2019/02/guardicore-press-releases-logo-banner2-845x200-1.jpg" alt="Guardicore logo" width="400" height="100">
  </a>
</p>
<h3 align="left">Vulnerability and Patch mgmt with OSQuery</h3>
<p align="left">

<table>
<tr>
<td> Description </td> <td> OSQuery </td>
</tr>
<tr>
<td> List of installed Security Updates</td>
<td>

```sql
SELECT hotfix_id, installed_on FROM patches WHERE description = "Security Update";
```

</td>
</tr>
<tr>
<td> CVE-2020-0601<br>
     <br>
     Windows CryptoAPI Spoofing Vulnerability<br>
     Simple Patch check</td>
<td>

```sql
SELECT 
    CASE 
     WHEN NOT EXISTS
     (SELECT 1
    FROM patches
    WHERE hotfix_id IN (
       'KB4534306', 
       'KB4534271', 
       'KB4534276', 
       'KB4534293', 
       'KB4534273',
       'KB4535550', 
       'KB4528760'))
     THEN 'PATCH_MISSING'
     ELSE 'SYSTEM_IS_PATCHED'
      END AS CVE_2020_0601_vulnerable;
```

</td>
</tr>
<tr>
<td> CVE-2020-0796<br>
     <br>
     Windows SMBv3 Client/Server Remote Code Execution Vulnerability<br>
     Patch check + OS build
</td>
<td>

```sql
  SELECT name, build, 
    CASE 
    WHEN EXISTS (
  SELECT patches.hotfix_id,
         os_version.build 
    FROM patches,os_version 
   WHERE (
          patches.hotfix_id < 'KB4551762' AND (os_version.build = '1903' OR os_version.build = '1909')
         	)
    )
    THEN "Vulnerable" 
	  ELSE "Not Vulnerable" 
	   END "CVE-2020-0796" FROM os_version;
```

</td>
</tr>
<tr>
<td> CVE-2020-1206<br>
     <br>
     Windows SMBv3 Client/Server Information Disclosure Vulnerability
</td>
<td>

```sql
SELECT name, build, 
 CASE 
  WHEN EXISTS (
SELECT patches.hotfix_id,os_version.build FROM patches,os_version 
 WHERE (
       patches.hotfix_id < 'KB4560960' AND (os_version.build = '1903' OR os_version.build = '1909')
       ) OR (
       patches.hotfix_id < 'KB4557957' AND os_version.build = '2004'
       )
  )
  THEN "Vulnerable" 
  ELSE "Not Vulnerable" 
   END "CVE-2020-1206" FROM os_version;
 ```

</td>
</tr>
<tr>
<td> CVE-2020-1350<br>
     <br>
     DNS RCE<br>
     Patch check + process + port + workaround
</td>
<td>

```sql
WITH 
dns AS
(SELECT COUNT(DISTINCT processes.name) AS cnt,
        1 AS one
 FROM processes,listening_ports
 WHERE processes.name = 'dns.exe' AND listening_ports.port = '53'),

kb AS
(SELECT COUNT(*)*2 AS cnt,
      1 AS one
   FROM patches
  WHERE hotfix_id = 'KB4561616' OR
        hotfix_id = 'KB4565541' OR
        hotfix_id = 'KB4565540' OR
        hotfix_id = 'KB4565537' OR
        hotfix_id = 'KB4565535'
        ),
    wrk AS
(SELECT COUNT(*)*4 AS cnt,
        1 AS one
   FROM registry 
  WHERE key='HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\DNS\Parameters' AND name='TcpReceivePacketSize' AND data < '65281')
SELECT
   CASE (kb.cnt+dns.cnt+wrk.cnt)
         WHEN 1 THEN "CRITICAL_PATCH_MISSING"
         WHEN 3 THEN "SYSTEM_IS_PATCHED"
         WHEN 7 THEN "SYSTEM_IS_PATCHED"
         WHEN 5 THEN "SYSTEM_HAS_A_WRKARND"
         ELSE "PATCH_NOT_APPLICABLE"
    END "CVE-2020-1350:DNS RCE"
   FROM kb
   JOIN dns USING (one)
   JOIN wrk USING (one);
```

</td>
</tr>
<tr>
<td> CVE-2020-0594<br>
     <br>
     Out-of-bounds read in IPv6 subsystem in Intel(R) AMT and Intel(R)
</td>
<td>

```sql
WITH
B1 AS
  (SELECT 1 as one, version as 'IME_VERSION',
    CASE
      WHEN Version LIKE '11.8%' AND Version >= '11.8.77' THEN 'NOT Vulnerable'
      WHEN Version LIKE '11.12%' AND Version >= '11.12.77' THEN 'NOT Vulnerable'
      WHEN Version LIKE '11.22%' AND Version >= '11.22.77' THEN 'NOT Vulnerable'
      WHEN Version LIKE '12.0%' AND Version >= '12.0.64' THEN 'NOT Vulnerable'
   ELSE 'Vulnerable to CVE-2020-0594'
    END 'CVE_STATUS'
   FROM intel_me_info), 
B2 AS
  (SELECT HARDWARE_MODEL, 1 AS one FROM system_info),
B3 AS
  (SELECT VERSION, 1 as one FROM platform_info)
SELECT
  B1.IME_VERSION,
  B1.CVE_STATUS,
  B2.HARDWARE_MODEL,
  B3.VERSION
FROM B1
JOIN B2 USING(one)
JOIN B3 USING(one);
```

</td>
</tr>
<tr>
<td> Search Vulnerable app by version</td>
<td>

```sql
SELECT name,version FROM programs WHERE name LIKE '%Apache%' AND version >= '8.5.0' and version <= '8.5.50'; 
```

</td>
</tr>

</table>
