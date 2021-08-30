## List all currently logged in users and their host comuter if connected remotely
### Platform: Windows, Linux, OSX, FreeBSD

```sql
SELECT host,tty,type,user FROM logged_in_users;
```