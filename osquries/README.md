<p align="left">
  <a href="https://www.guardicore.com/">
    <img src="https://www.guardicore.com/wp-content/uploads/2019/02/guardicore-press-releases-logo-banner2-845x200-1.jpg" alt="Guardicore logo" width="400" height="100">
  </a>
</p>
<h3 align="left">Basic Information</h3>
<p align="left">

| Description | Query |
| --- | --- |
| `Osquery version` | *SELECT version FROM osquery_info;* |
| `List of tables` | *.tables* |
| `Table description` | *.schema <table_name>* |

<h3 align="left">Asset / IR information</h3>

| Description | Query |
| --- | --- |
| `Logged in users` | SELECT host,tty,type,user FROM logged_in_users; |
| `Top 10 memory consumption apps` | SELECT pid, name, uid, resident_size FROM processes order by resident_size desc limit 10; |
| `Count processes by #instances` | SELECT count(pid) as total, name FROM processes group by name order by total desc limit 10; |
| `Chrome extensions` | SELECT u.username, ce.name, ce.identifier, ce.version, ce.description, ce.locale, ce.update_url, ce.author, ce.persistent, ce.path FROM chrome_extensions ce LEFT JOIN users u ON ce.uid = u.uid; |
| `BIOS data` | SELECT data,name,type FROM registry WHERE key LIKE 'HKEY_LOCAL_MACHINE\HARDWARE\DESCRIPTION\System\BIOS' |
| `System uptime` | SELECT datetime(time.local_time - uptime.total_seconds, 'unixepoch') AS last_rebooted FROM time, uptime; |
| `System Info` | SELECT hostname, cpu_subtype, cpu_brand, physical_memory, hardware_vendor,hardware_model FROM system_info; |
| `OS version` | SELECT name, version, build, platform FROM os_version; |
| `List Listening Processes` | SELECT DISTINCT process.name, listening.port, listening.address, process.pid FROM processes AS process JOIN listening_ports AS listening ON process.pid = listening.pid WHERE address != '127.0.0.1'; |
| `Look for specific process` | SELECT DISTINCT(processes.name), process_open_sockets.local_port FROM processes JOIN process_open_sockets USING (pid) WHERE local_port=53 AND processes.name LIKE 'dns%'; |
| `exec removed FROM disk` | SELECT name, path, pid FROM processes WHERE on_disk = 0; |
| `Open sockets` | <p>SELECT remote_address,remote_port,local_address,local_port,family,protocol,state<br>FROM process_open_sockets<br>WHERE remote_address NOT LIKE '127.0.0.1'<br>AND remote_address NOT LIKE '0.0.0.0'<br>AND remote_address NOT LIKE '::'<br>AND remote_address NOT LIKE '0'</p> |
| `Open sockets + username + process name + process path` | <p>SELECT * u.username,p.pid,p.name,pos.local_address,pos.local_port,p.path,p.cmdline,pos.remote_address,pos.remote_port<br>FROM processes as p<br>JOIN users as u<br>on u.uid=p.uid<br>JOIN process_open_sockets as pos<br>on pos.pid=p.pid<br>WHERE pos.remote_port !='0' AND pos.remote_address != '127.0.0.1'<br>limit 1000;</p> |
| `Docker listening sockets` | <p>SELECT l.port, p.pid, p.path, p.cmdline<br>FROM listening_ports AS l<br>LEFT JOIN processes p ON p.pid=l.pid<br>WHERE p.path LIKE "%docker%" AND port!=0;</p> |
| `Installed programs - Windows` | select name,install_location FROM programs; |
| `Installed programs - Debian/Ubuntu` | select * FROM deb_packages; |
| `Installed programs - RHEL/CentOs` | SELECT * FROM rpm_packages; |
| `List local users` | <p>SELECT g.groupname,u.username FROM users as u<br>JOIN user_groups as ug<br>on u.uid=ug.uid<br>JOIN groups as g<br>on g.gid=ug.gid<br>limit 1000;</p> |
| `List local user in priv groups` | <p>SELECT u.username,g.groupname FROM users as u<br>JOIN user_groups as ug<br>on u.uid=ug.uid<br>JOIN groups as g<br>on g.gid=ug.gid<br>WHERE g.groupname = 'Administrators' OR g.groupname = 'sudo' OR g.groupname = 'root';</p> |
| `List Users/Processes` | <p>SELECT p.pid, p.name, u.username FROM processes AS p<br>JOIN users AS u<br>ON p.uid = u.uid;</p> |
| `look for specific program` | SELECT * FROM programs WHERE name LIKE '%wireshark%'; |
| `Get hash of running processes` | <p>SELECT DISTINCT h.md5, p.name, p.path, u.username<br>FROM processes AS p<br>INNER JOIN hash AS h ON h.path = p.path<br>INNER JOIN users AS u ON u.uid = p.uid<br>ORDER BY start_time DESC<br>LIMIT 20;</p> |
| <p>`Find parent processes`<br>`(replace pid with one you look for)`</p> | <p>WITH RECURSIVE<br>rc(pid, parent, name) AS (<br>SELECT pid, parent, name FROM processes WHERE pid = 14380<br>UNION ALL<br>SELECT p.pid, p.parent, p.name FROM processes AS p, rc<br>WHERE p.pid = rc.parent<br>AND p.pid != 0)<br>SELECT pid, parent, name FROM rc LIMIT 20;<p> | 
| <p>`Find processes run by powershell`<br>`(ignore conhost from legit path)`</p> | <p>select p.pid,p.name,p.path,p.parent,parents.name as parent_name from processes as p<br>inner join processes as parents on p.parent = parents.pid<br>where parents.name = 'powershell.exe' AND p.path != 'C:\Windows\System32\conhost.exe';</p> |
| <p>`Look for svchost instances that are not running legitimately`<br>`(not spawned from services, not in their right path or have no flag arguments)`</p> | <p>select p.pid, p.path, p.parent, p.cmdline, par.name as parent_name, par.cmdline as parent_cmdline from processes as p<br>inner join processes as par on p.parent=par.pid<br>where p.name='svchost.exe'<br> and<br> (par.name!='services.exe' or p.path not like '%windows\system32\svchost.exe' or p.cmdline not like '%-%');</p> |
| <p>`FritzFrog detector`<br>`Checks for listening port 1234 and running service name ifconfig or nginx`</p> | <p>SELECT <br>CASE <br>WHEN EXISTS<br>(SELECT 1<br>FROM listening_ports as l<br>JOIN processes p ON p.pid=l.pid<br>WHERE <br>l.port IN ('1234') AND <br>p.name IN ('nginx','ifconfig'))<br>THEN 'POSSIBLE_infected'<br>ELSE 'SYSTEM_IS_CLEAN'<br>END AS FRITZ_FROG_INFECTED;</p> |

<h3 align="left">FIM</h3>

| Description | Query |
| --- | --- |
| `Hash of a file` | SELECT * FROM hash WHERE path = 'C:\Users\Administrator\Desktop\password.txt'; |
| `Hash of files in folder` | SELECT * FROM hash WHERE path LIKE 'C:\Users\Administrator\Desktop\%'; |
| `Hash of files in many folders` | SELECT * FROM file WHERE path LIKE "C:\Users\%\Desktop\%"; |
| `Search for a file` | SELECT * FROM hash WHERE path LIKE 'C:\%\%\%\password.txt'; |

