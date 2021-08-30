## List all installed programs Debian/Ubuntu
### Platform: Linux

```sql
SELECT * FROM deb_packages;
```

## List all installed programs RedHat/CentOS
### Platform: Linux

```sql
SELECT * FROM rpm_packages;
```

## List all installed programs
### Platform: Windows

```sql
SELECT name,install_location FROM programs;
```