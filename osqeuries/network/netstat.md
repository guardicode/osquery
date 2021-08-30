## List all open sockets and the processes holding them
### Platform: Windows, Linux, OSX, FreeBSD

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

## List all open sockets and the processes holding them, and the users running those processes
### Platform: Windows, Linux, OSX, FreeBSD

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

## List all open sockets, but filter by process name and port
### Platform: Windows, Linux, OSX, FreeBSD

```sql
SELECT DISTINCT(processes.name), process_open_sockets.local_port 
  FROM processes 
  JOIN process_open_sockets USING (pid) 
 WHERE local_port=5432 AND processes.name 
  LIKE 'postgres%';
```