---
connie-publish: true
connie-page-id: "102211"
---

**Tags :** #Code
**Subject :**
**Created at :** *2023-07-19*
**Autors :** Ben HUR


```powershell
# Première méthode qui peut marcher dans certains cas
Get-Member -InputObject $ExcelPackage.Workbook.Worksheets.Item
Get-Member -InputObject $ExcelPackage.Workbook.Worksheets.Item("ACADEM")
Get-Member -InputObject $AcademMaquete.Tables -MemberType Method | Select-Object Definition
# Deuxième méthode qui marche mieux
$WorkSheet_Modele.Cells.Item(1,1).Style.Font.PSObject.Properties | Format-Table
```

