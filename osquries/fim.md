<p align="left">
  <a href="https://www.guardicore.com/">
    <img src="https://www.guardicore.com/wp-content/uploads/2019/02/guardicore-press-releases-logo-banner2-845x200-1.jpg" alt="Guardicore logo" width="400" height="100">
  </a>
</p>
<h3 align="left">FIM with OSQuery</h3>
<p align="left">

<table>
<tr>
<td> Description </td> <td> OSQuery </td>
</tr>
<tr>
<td> Hash of specific file</td>
<td>

```sql
SELECT * FROM hash WHERE path = 'C:\Users\Administrator\Desktop\password.txt';
```

</td>
</tr>
<tr>
<td> Hash of files in folder</td>
<td>

```sql
SELECT * FROM hash WHERE path LIKE 'C:\Users\Administrator\Desktop\%';
```

</td>
</tr>
<tr>
<td> Hash of files in many folders</td>
<td>

```sql
SELECT * FROM file WHERE path LIKE 'C:\Users\%\Desktop\%';
```

</td>
</tr>
<tr>
<td> Search for a file</td>
<td>

```sql
SELECT * FROM hash WHERE path LIKE 'C:\%\%\%\password.txt';
 ```

</td>
</tr>

</table>
