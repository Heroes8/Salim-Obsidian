---
connie-publish: true
connie-page-id: "102083"
---

**Tags :** #Code
**Subject :**
**Created at :** *2023-08-05*
**Autors :** Ben HUR


```shell
conf t
ip arp inspection vlan 1
ip arp inspection validate src-mac
# Trusting the default gateway
int gi0/0
ip arp inspection trust
# Pour mimiter le traffic des port client
int gi0/1
ip arp inspection limit 10 
end
show arp inspection
```

