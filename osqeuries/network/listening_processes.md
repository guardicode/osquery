## List all processes that are listening for network connections
### Platform: Windows, Linux, OSX, FreeBSD

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

## All sockets opened by the docker process
### Platform: Windows, Linux, OSX, FreeBSD

```sql
SELECT l.port, p.pid, p.path, p.cmdline
  FROM listening_ports AS l
  LEFT JOIN processes p 
    ON p.pid=l.pid
 WHERE p.path LIKE "%docker%" AND port!=0;
```
