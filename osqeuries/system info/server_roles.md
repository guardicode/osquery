## Check if host is a DNS server
### Platform: Windows

```sql
SELECT * FROM windows_optional_features WHERE state == 1 AND name == "DNS-Server-Full-Role";
```

## Check if host is a DHCP server
### Platform: Windows

```sql
SELECT * FROM windows_optional_features WHERE state == 1 AND name == "DHCPServer";
```

## Check if host is an Active Directory controller
### Platform: Windows

```sql
SELECT * FROM windows_optional_features WHERE state == 1 AND name == "DirectoryServices-DomainController";
```