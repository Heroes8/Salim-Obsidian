---
connie-publish: true
connie-page-id: "102225"
---

**Tags :** #Code
**Subject :** Réccupérer les variables d'environnement en powershell et en créer de nouvelles
**Created at :** *2023-08-02*
**Autors :** ben HUR


```powershell
Get-ChildItem -Path Env:
# Quand il y a plusieurs valeurs pour une var d'env
$env:PSModulePath -split ';'
# Créer une nouvelle variable d'environnement
$Env:<variable-name> = "<new-value>"
```

