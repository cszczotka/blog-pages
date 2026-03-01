## Azure SQL users namagment

Few tips how to manage users in Azure SQL

---

### Select db users


```sql
select name as username,
       create_date,
       modify_date,
       type_desc as type,
       authentication_type_desc as authentication_type
from sys.database_principals
where type not in ('A', 'G', 'R', 'X')
      and sid is not null
order by username;
```

### Show uses and roles

```sql
SELECT
DP1.name AS DatabaseRoleName
,ISNULL(DP2.name, 'No members') AS DatabaseUserName
,DP2.principal_id
,DP2.create_date
FROM sys.database_role_members AS DRM
RIGHT OUTER JOIN sys.database_principals AS DP1 ON DRM.role_principal_id = DP1.principal_id
LEFT OUTER JOIN sys.database_principals AS DP2 ON DRM.member_principal_id = DP2.principal_id
WHERE DP1.type = 'R'
ORDER BY DP1.name, ISNULL(DP2.name, 'No members');
```

### Register Manage Identity ( for example which repesent ADF ) as external user

```sql
CREATE USER [adf-mi] FROM EXTERNAL PROVIDER;
 
EXEC sys.sp_addrolemember   
    @rolename = N'db_datareader',  
    @membername = [adf-mi]
 
EXEC sys.sp_addrolemember   
    @rolename = N'db_datawriter',  
    @membername = [adf-mi]  
 
  
EXEC sys.sp_addrolemember   
    @rolename = N'db_owner',  
    @membername = [adf-mi]
```

### Create database user

Run on master db

```sql
CREATE LOGIN sqluser-dbw-dev-westeu-001-read  WITH PASSWORD = 'xxx'
```

Run on your db
```sql
CREATE USER [sqluser-dbw-dev-westeu-001-read]  FOR LOGIN [sqluser-dbw_dev-westeu-001-read]  WITH DEFAULT_SCHEMA = dbo;
```