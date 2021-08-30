## List all users on a host
### Platform: Windows, Linux, OSX, FreeBSD

```sql
SELECT u.username FROM users;
```

## List all users and their groups
### Platform: Windows, Linux, OSX, FreeBSD

```sql
SELECT g.groupname,u.username 
  FROM users as u
  LEFT JOIN user_groups as ug
    on u.uid=ug.uid
  LEFT JOIN groups as g
    on g.gid=ug.gid
 limit 1000;
```