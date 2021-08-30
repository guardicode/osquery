## Top 10 memory consuming processes
### Platform: Windows, Linux, OSX, FreeBSD

```sql
SELECT pid, name, uid, resident_size FROM processes order by resident_size desc limit 10;
```