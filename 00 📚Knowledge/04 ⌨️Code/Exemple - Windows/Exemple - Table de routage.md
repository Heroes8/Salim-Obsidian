---
connie-publish: true
connie-page-id: "68782"
---

**Tags :** #Code
**Subject :** Afficher la table de routage , et configuration de route statique sur Windows
**Created at :** *2023-08-05*
**Autors :** Ben HUR


```powershell
# Affiche la table de routage complète
route PRINT
# affiche les routes statiques d'un sous réseau spécifdique
route print 10.*
# ajout de la route par défaut
route add 0.0.0.0 mask 0.0.0.0 192.168.12.1
# route vers un sous réseau spécifique
route add 10.41.0.0 mask 255.255.0.0 10.27.0.1
# Ajout d'une route persistante qui restera après un reboot
route /p add 10.41.0.0 mask 255.255.0.0 10.27.0.1
# Pour supprimer une route vers un sous réseau spécifique
route delete 10.41.0.0 mask 255.255.0.0
# Pour changer une route spécifique
route change 10.41.0.0 mask 255.255.0.0 10.27.0.25
```

