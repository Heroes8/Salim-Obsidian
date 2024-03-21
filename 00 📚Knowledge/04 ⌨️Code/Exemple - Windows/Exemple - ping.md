---
connie-publish: true
connie-page-id: "36468"
---

**Tags :** #Code
**Subject :** Description des options de ping
**Created at :** *2023-08-05*
**Autors :** Ben HUR


```powershell
# Revert ip to domain name
ping -a 8.8.8.8
# Record route
ping -R 8.8.8.8
# Display more info
ping -v 8.8.8.8
# test si le port réseau est shut
ping -m 8.8.8.8 21 
# test si l'ip est traité par une ACL
ping 8.8.8.8 -s 192.168.1.25
```

