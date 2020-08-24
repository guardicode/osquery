<p align="left">
  <a href="https://www.guardicore.com/">
    <img src="https://www.guardicore.com/wp-content/uploads/2019/02/guardicore-press-releases-logo-banner2-845x200-1.jpg" alt="Guardicore logo" width="400" height="100">
  </a>
</p>
<h3 align="left">Threat Hunting queries</h3>
<p align="left">

<table>
<tr>
<td> Description </td> <td> OSQuery </td>
</tr>
<tr>
<td> Find running executable where file removed FROM disk</td>
<td>

```sql
SELECT name, path, pid FROM processes WHERE on_disk = 0;
```

</td>
</tr>
<tr>
<td> Find a runing program</td>
<td>

```sql
SELECT * FROM programs WHERE name LIKE '%wireshark%';
```

</td>
</tr>
<tr>
<td> Get Hash of running processes</td>
<td>

```sql
SELECT DISTINCT h.md5, p.name, p.path, u.username
  FROM processes AS p
 INNER JOIN hash AS h ON h.path = p.path
 INNER JOIN users AS u ON u.uid = p.uid
 ORDER BY start_time DESC
 LIMIT 100;
```

</td>
</tr>
<tr>
<td> Find the parent processes<br>
     (replace pid with one you look for)</td>
<td>

```sql
WITH RECURSIVE rc(pid, parent, name) 
     AS (
 SELECT pid, parent, name FROM processes WHERE pid = 14380
  UNION ALL
 SELECT p.pid, p.parent, p.name FROM processes AS p, rc
  WHERE p.pid = rc.parent
    AND p.pid != 0)
 SELECT pid, parent, name FROM rc LIMIT 20;
 ```

</td>
</tr>
<tr>
<td> Find processes run by powershell<br>
     (ignore conhost from legit path)</td>
<td>

```sql
SELECT p.pid,
       p.name,
       p.path,
       p.parent,
       parents.name 
    AS parent_name 
  FROM processes as p
 INNER JOIN processes 
    AS parents on p.parent = parents.pid
 WHERE parents.name = 'powershell.exe' AND p.path != 'C:\Windows\System32\conhost.exe';
```

</td>
</tr>
<tr>
<td> Look for svchost instances that<br> 
     are not running legitimately<br>
     (not spawned from services, not in <br>
     their right path or have no flag arguments)</td>
<td>

```sql
SELECT p.pid, 
       p.path, 
       p.parent, 
       p.cmdline, 
       par.name 
    AS parent_name, 
       par.cmdline 
    AS parent_cmdline 
  FROM processes 
    AS p
 INNER JOIN processes 
    AS par ON p.parent=par.pid
 WHERE p.name='svchost.exe'
   AND (par.name!='services.exe' or p.path not like '%windows\system32\svchost.exe' or p.cmdline not like '%-%');
```

</td>
</tr>
<tr>
<td> FritzFrog detector<br>
     Checks for listening port 1234 <br>
     and running service name ifconfig or nginx</td>
<td>

```sql
SELECT CASE
  WHEN EXISTS
(SELECT 1
   FROM listening_ports as l
   JOIN processes p 
     ON p.pid=l.pid
  WHERE l.port IN 
        ('1234') 
    AND p.name IN 
        ('nginx',
        'ifconfig')
)
  THEN 'POSSIBLE_infected'
  ELSE 'SYSTEM_IS_CLEAN'
   END AS FRITZ_FROG_INFECTED;
```

</td>
</tr>

</table>
