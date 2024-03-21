---
connie-publish: true
connie-page-id: "68694"
---

**Tags :** #Code #Powershell 
**Subject :** appel du constructeur en var globale & méthodes utiles
**Created at :** *2023-03-23*

## Cast à partir d'une variable liste de string

```powershell
$listState = "NON-VENU","NONREINSCRIT","NON-DIPLOME","NONADMIS","EXCLUSION","DIPLOME","DEMISSION","BLOQUE"
$STATES = [System.Collections.ArrayList]$listState
```

## Constructeur

```powershell
$global:AllObjects = [System.Collections.ArrayList]::new()
```


## Liste d'objet custom

```powershell
function addUserToCSV ($Prenom, $Nom, $U4_PrincipalAddress, $AAD_Anomalie, $AAD_SecondaryHomonymeAlias, $AAD_HomonymeAADuser, $resource_id) {
    #Write-Host "id :" $AD_Login " Prenom : " $AD_FirstName " Nom : " $AD_LastName " password : " $AD_PassWord " email : " $AD_Email
    $rowObject = New-Object PSObject
    $rowObject | Add-Member -MemberType NoteProperty -Name Prenom -Value $Prenom -Force
    $rowObject | Add-Member -MemberType NoteProperty -Name Nom -Value $Nom -Force
    $rowObject | Add-Member -MemberType NoteProperty -Name U4_PrincipalAddress -Value $U4_PrincipalAddress -Force
    $rowObject | Add-Member -MemberType NoteProperty -Name AAD_SecondaryHomonymeAlias -Value $AAD_SecondaryHomonymeAlias -Force
    $rowObject | Add-Member -MemberType NoteProperty -Name AAD_HomonymeAADuser -Value $AAD_HomonymeAADuser -Force
    $rowObject | Add-Member -MemberType NoteProperty -Name resource_id -Value $resource_id -Force
    $global:AllObjects.Add($rowObject) | Out-Null
    #Write-Host -ForegroundColor Yellow ("la taille du tableau :" + $global:AllObjects.count)
}
```

## Export de la liste

```powershell
function ExportToCSV {

    $OutRep = $PSScriptRoot + "\Source\export"

    $ExpFile = "$OutRep\$csvFileName"

    #$resultsArray | Select-Object * | export-csv -Path "$currentExecutingPath\040.csv"

    $global:AllObjects | Export-Csv -Encoding UTF8 -NoTypeInformation -Delimiter ";" -Path "$ExpFile"

}
```