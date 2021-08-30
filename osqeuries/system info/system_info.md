## Get general system info
### Platform: Windows, Linux, OSX, FreeBSD

```sql
SELECT hostname, cpu_subtype, cpu_brand, physical_memory, hardware_vendor,hardware_model FROM system_info;
```

## OS version
### Platform: Windows, Linux, OSX, FreeBSD

```sql
SELECT name, version, build, platform FROM os_version;
```