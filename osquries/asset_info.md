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
SELECT data,name,type FROM registry WHERE key LIKE 'HKEY_LOCAL_MACHINE\HARDWARE\DESCRIPTION\System\BIOS';
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
</table>
