---
connie-publish: true
connie-page-id: "68678"
---

**Tags :** #Code
**Subject :** méthode moins obsolète pour ne pas mettre les password en clair
**Created at :** *2023-03-23*

```powershell
Import-Module SqlServer
$IDAGR = "325"
Invoke-Sqlcmd -Query "USE AGRPRODM7;
SELECT [address]
      ,[address_id]
      ,[address_type]
      ,[agr_user_id]
      ,[attribute_id]
      ,[client]
      ,[country_code]
      ,[description]
      ,[dim_value]
      ,[e_mail]
      ,[e_mail_cc]
      ,[ean]
      ,[last_update]
      ,[place]
      ,[pos_title]
      ,[province]
      ,[sequence_no]
      ,[tax_system]
      ,[telephone_1]
      ,[telephone_2]
      ,[telephone_3]
      ,[telephone_4]
      ,[telephone_5]
      ,[telephone_6]
      ,[telephone_7]
      ,[url_path]
      ,[user_id]
      ,[wf_state]
      ,[zip_code]
      ,[agrtid]
  FROM [AGRPRODM7].[dbo].[agladdress]
  WHERE attribute_id = 'C0' AND dim_value = $IDAGR AND address_type = 1" -ServerInstance "obelix.campus.villejuif"

## Prompt the user for their password.  
#$pwd = read-host -AsSecureString -Prompt "Password"  
  # FOR SQL AUTH -Username "USERNAME" -Password $pwd
```

## OU 

**Non vérifié**

```powershell
Import-Module SimplySql
$Creds = Get-Credentials
Open-MySQLConnection -Server "obelix.campus.villejuif" -Database "AGRPRODM7"
```
