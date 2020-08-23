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
| `Logged in users` | *SELECT host,tty,type,user FROM logged_in_users;* |
| `Top 10 memory consumption apps` | *SELECT pid, name, uid, resident_size FROM processes order by resident_size desc limit 10;* |
| `Count processes by #instances` | *SELECT count(pid) as total, name FROM processes group by name order by total desc limit 10;* |
| `Chrome extensions` | *SELECT u.username, ce.name, ce.identifier, ce.version, ce.description, ce.locale, ce.update_url, ce.author, ce.persistent, ce.path FROM chrome_extensions ce LEFT JOIN users u ON ce.uid = u.uid;* |
| `BIOS data` | *SELECT data,name,type FROM registry WHERE key LIKE 'HKEY_LOCAL_MACHINE\HARDWARE\DESCRIPTION\System\BIOS'* |
| `System uptime` | *SELECT datetime(time.local_time - uptime.total_seconds, 'unixepoch') AS last_rebooted FROM time, uptime;* |
| `System Info` | *SELECT hostname, cpu_subtype, cpu_brand, physical_memory, hardware_vendor,hardware_model FROM system_info;* |
| `OS version` | *SELECT name, version, build, platform FROM os_version;* |
| `List Listening Processes` | *SELECT DISTINCT process.name, listening.port, listening.address, process.pid FROM processes AS process JOIN listening_ports AS listening ON process.pid = listening.pid WHERE address != '127.0.0.1';* |
| `Look for specific process` | *SELECT DISTINCT(processes.name), process_open_sockets.local_port FROM processes JOIN process_open_sockets USING (pid) WHERE local_port=53 AND processes.name LIKE 'dns%';* |
| `exec removed FROM disk` | *SELECT name, path, pid FROM processes WHERE on_disk = 0;* |
| `Open sockets` | <p>SELECT remote_address,remote_port,local_address,local_port,family,protocol,state<br>    FROM process_open_sockets<br>      WHERE remote_address NOT LIKE '127.0.0.1'<br>       AND remote_address NOT LIKE '0.0.0.0'<br>       AND remote_address NOT LIKE '::'<br>       AND remote_address NOT LIKE '0'</p> |
| `value` | <ul><li>value 1</li><li>value 2</li></ul> |
| `value` | <ul><li>value 1</li><li>value 2</li></ul> |
| `Open sockets + username + process name + process path` | SELECT * u.username,
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
limit 1000;* |
| `Docker listening sockets` | * select l.port, p.pid, p.path, p.cmdline
  FROM listening_ports AS l
       LEFT JOIN processes p ON p.pid=l.pid
WHERE p.path LIKE "%docker%"
  AND port!=0;* |
| `Installed programs - Windows` | *select name,install_location FROM programs;* |
| `Installed programs - Debian/Ubuntu` | *select * FROM deb_packages;* |
| `Installed programs - RHEL/CentOs` | *SELECT * FROM rpm_packages;* |
| `List local users` | *SELECT g.groupname,u.username FROM users as u
JOIN user_groups as ug
    on u.uid=ug.uid
JOIN groups as g
    on g.gid=ug.gid
limit 1000;* |
| `List local user in priv groups` | *SELECT u.username,g.groupname FROM users as u
JOIN user_groups as ug
    on u.uid=ug.uid
JOIN groups as g
    on g.gid=ug.gid
WHERE g.groupname = 'Administrators' OR g.groupname = 'sudo' OR g.groupname = 'root';* |
| `List Users/Processes` | *SELECT p.pid, p.name, u.username FROM processes AS p
JOIN users AS u
    ON p.uid = u.uid;* |
| `look for specific program` | *SELECT * FROM programs WHERE name LIKE '%wireshark%';* |
| `Get hash of running processes` | *SELECT DISTINCT h.md5, p.name, p.path, u.username
FROM processes AS p
INNER JOIN hash AS h ON h.path = p.path
INNER JOIN users AS u ON u.uid = p.uid
ORDER BY start_time DESC
LIMIT 20;* |
| `Find parent processes 
(replace pid with one you look for)` | *WITH RECURSIVE
rc(pid, parent, name) AS (
 SELECT pid, parent, name FROM processes WHERE pid = 14380 
 UNION ALL
 SELECT p.pid, p.parent, p.name FROM processes AS p, rc
 WHERE p.pid = rc.parent
 AND p.pid != 0)
SELECT pid, parent, name FROM rc LIMIT 20;* | 
| `Find processes run by powershell  
(ignore conhost from legit path)` | *select p.pid,p.name,p.path,p.parent,parents.name as parent_name from processes as p
inner join processes as parents on p.parent = parents.pid
where parents.name = 'powershell.exe' AND p.path != 'C:\Windows\System32\conhost.exe';* |
| `Look for svchost instances that are not running legitimately 
(not spawned from services, not in their right path or have no flag arguments)` | *select p.pid, p.path, p.parent, p.cmdline, par.name as parent_name, par.cmdline as parent_cmdline from processes as p
inner join processes as par on p.parent=par.pid
where p.name='svchost.exe' and (par.name!='services.exe' or p.path not like '%windows\system32\svchost.exe' or p.cmdline not like '%-%');* |
| `FritzFrog detector
Checks for listening port 1234 and running service name ifconfig or nginx` | *SELECT 
   CASE 
	 WHEN EXISTS
	 (SELECT 1
	 FROM listening_ports as l
	 JOIN processes p ON p.pid=l.pid
	 WHERE 
	  l.port IN (
        '1234') 
	 AND 
	  p.name IN (
        'nginx',
        'ifconfig')
	 )
THEN 'POSSIBLE_infected'
ELSE 'SYSTEM_IS_CLEAN'
END AS FRITZ_FROG_INFECTED;* |







| Description | Query |
| --- | --- |
| `Osquery version` | List all *new or modified* files |
| `List of tables` | Show file differences that **haven't been** staged |
</p>
