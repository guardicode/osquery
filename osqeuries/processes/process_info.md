## List all currently running processes
### Platform: Windows, Linux, OSX, FreeBSD

```sql
SELECT pid, name, path FROM processes;
```

## List Top 10 common process names (on the same host)
### Platform: Windows, Linux, OSX, FreeBSD

```sql
SELECT count(pid) as total, name FROM processes group by name order by total desc limit 10;
```

## List all processes and their owning user
### Platform: Windows, Linux, OSX, FreeBSD

```sql
SELECT p.pid, p.name, u.username 
FROM processes AS p
JOIN users AS u
ON p.uid = u.uid;
```

## List all processes from a certain user
### Platform: Windows, Linux, OSX, FreeBSD

```sql
SELECT p.pid, p.name, u.username 
FROM processes AS p
JOIN users AS u
ON p.uid = u.uid
WHERE u.username='administrator';
```