## List Google Chrome extensions for all users
### Platform: Windows, Linux, OSX, FreeBSD

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