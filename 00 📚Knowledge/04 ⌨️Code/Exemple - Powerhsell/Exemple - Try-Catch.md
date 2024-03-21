---
connie-publish: true
connie-page-id: "200327"
---

**Tags :** #Code
**Subject :** Syntaxe des try catchs
**Created at :** *2023-07-07*
**Autors :** Ben Hur


```powershell
Try {
  . $currentExecutingPath"Libs\utils.ps1"
  . $currentExecutingPath"Libs\agresso_bdd.ps1"
}
Catch {
  Write_Message "Curr executing path : $currentExecutingPath"
  Write_Message "Not included libs in 101 subscript, test var is : $test"
  Write-Host $_
  break
}
```

