<p align="left">
  <a href="https://www.guardicore.com/">
    <img src="https://www.guardicore.com/wp-content/uploads/2019/02/guardicore-press-releases-logo-banner2-845x200-1.jpg" alt="Guardicore logo" width="400" height="100">
  </a>
</p>
<h3 align="left">Asset Information</h3>
<p align="left">

<table>
<tr>
<td> Description </td> <td> OSQuery </td>
</tr>
<tr>
<td> Logged in users</td>
<td>

```sql
SELECT host,tty,type,user FROM logged_in_users;
```

</td>
</tr>
<tr>
<td> Top 10 memory consumption apps</td>
<td>

```sql
SELECT pid, name, uid, resident_size FROM processes order by resident_size desc limit 10;
```

</td>
</tr>
<tr>
<td> Count processes by #instances</td>
<td>

```sql
SELECT count(pid) as total, name FROM processes group by name order by total desc limit 10;
```

</td>
</tr>
<tr>
<td> Chrome extensions</td>
<td>

```sql
SELECT u.username, 
       ce.name, 
       ce.identifier, 
       ce.version, 
       ce.description, 
       ce.locale, 
       ce.update_url, 
       ce.author, 
       ce.persistent, 
       ce.path 
  FROM chrome_extensions ce 
  LEFT JOIN users u 
    ON ce.uid = u.uid;
 ```

</td>
</tr>
<tr>
<td> BIOS data</td>
<td>

```sql
SELECT name,data,type FROM registry WHERE key LIKE 'HKEY_LOCAL_MACHINE\HARDWARE\DESCRIPTION\System\BIOS';
```

</td>
</tr>
<tr>
<td> System uptime</td>
<td>

```sql
SELECT datetime(time.local_time - uptime.total_seconds, 'unixepoch') AS last_rebooted FROM time, uptime;
```

</td>
</tr>
<tr>
<td> System Info</td>
<td>

```sql
SELECT hostname, cpu_subtype, cpu_brand, physical_memory, hardware_vendor,hardware_model FROM system_info;
```

</td>
</tr>
<tr>
<td> OS version</td>
<td>

```sql
SELECT name, version, build, platform FROM os_version;
```

</td>
</tr>
<tr>
<td> List Listening Processes</td>
<td>

```sql
SELECT DISTINCT 
       process.name, 
       listening.port, 
       listening.address, 
       process.pid 
  FROM processes AS process 
  JOIN listening_ports AS listening 
    ON process.pid = listening.pid 
 WHERE address != '127.0.0.1';
```

</td>
</tr>
<tr>
<td> Look for specific process<br>
     (example dns port 53)</td>
<td>

```sql
SELECT DISTINCT(processes.name), process_open_sockets.local_port 
  FROM processes 
  JOIN process_open_sockets USING (pid) 
 WHERE local_port=53 AND processes.name 
  LIKE 'dns%';
```

</td>
</tr>
<tr>
<td> Open sockets with process name</td>
<td>

```sql
SELECT p.name,s.remote_address,s.remote_port,s.local_address,s.local_port,s.family,s.protocol
  FROM process_open_sockets as s
  JOIN processes as p
    ON s.pid=p.pid
 WHERE s.remote_address NOT LIKE '127.0.0.1'
   AND s.remote_address NOT LIKE '0.0.0.0'
   AND s.remote_address NOT LIKE '::'
   AND s.remote_address NOT LIKE '0';
```

</td>
</tr>
<tr>
<td> Open sockets<br> 
     + username<br> 
     + process name<br> 
     + process path</td>
<td>

```sql
SELECT u.username,
       p.pid,
       p.name,
       pos.local_address,
       pos.local_port,
       p.path,
       p.cmdline,
       pos.remote_address,
       pos.remote_port
  FROM processes as p
  JOIN users as u
    on u.uid=p.uid
  JOIN process_open_sockets as pos
    on pos.pid=p.pid
 WHERE pos.remote_port !='0' AND pos.remote_address != '127.0.0.1'
 limit 1000;
```

</td>
</tr>
<tr>
<td> Docker listening sockets</td>
<td>

```sql
SELECT l.port, p.pid, p.path, p.cmdline
  FROM listening_ports AS l
  LEFT JOIN processes p 
    ON p.pid=l.pid
 WHERE p.path LIKE "%docker%" AND port!=0;
```

</td>
</tr>
<tr>
<td> Installed programs - Windows</td>
<td>

```sql
SELECT name,install_location FROM programs;
```

</td>
</tr>
<tr>
<td> Installed programs - Debian/Ubuntu</td>
<td>

```sql
SELECT * FROM deb_packages;
```

</td>
</tr>
<tr>
<td> Installed programs - RHEL/CentOs</td>
<td>

```sql
SELECT * FROM rpm_packages;
```

</td>
</tr>
<tr>
<td> List local users</td>
<td>

```sql
SELECT g.groupname,u.username 
  FROM users as u
  JOIN user_groups as ug
    on u.uid=ug.uid
  JOIN groups as g
    on g.gid=ug.gid
 limit 1000;
```

</td>
</tr>
<tr>
<td> List local user in priv groups</td>
<td>

```sql
SELECT u.username,g.groupname 
  FROM users as u
  JOIN user_groups as ug
    on u.uid=ug.uid
  JOIN groups as g
    on g.gid=ug.gid
 WHERE g.groupname = 'Administrators' OR g.groupname = 'sudo' OR g.groupname = 'root';
```

</td>
</tr>
<tr>
<td> List Users/Processes</td>
<td>

```sql
SELECT p.pid, p.name, u.username 
  FROM processes AS p
  JOIN users AS u
    on p.uid = u.uid;
```

</td>
</tr>
<tr>
<td> Check SMBv1 still supported on system (Windows)</td>
<td>

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
</td>
</tr>
<tr>
<td> Find Windows DHCP Servers</td>
<td>

```sql
SELECT * FROM windows_optional_features WHERE state == 1 AND name == "DHCPServer";
 ```

</td>
</tr>
  <tr>
<td> Find Active Directory controllers</td>
<td>

```sql
SELECT * FROM windows_optional_features WHERE state == 1 AND name == "DirectoryServices-DomainController";
 ```

</td>
</tr>

</table>
