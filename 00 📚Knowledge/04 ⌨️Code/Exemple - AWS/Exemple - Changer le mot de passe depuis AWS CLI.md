---
connie-publish: true
connie-page-id: "36581"
---

**Tags :** #Code
**Subject :** Depuis un compte administrateur dont les clés d'accès ont été renseignées sur AWS CLI
**Created at :** *2023-06-22*
**Autors :** HUR


## Pour réccupérer le nom d'utilisateur en fonction de la clé d'accès

```shell
 aws iam get-access-key-last-used --access-key-id 
```


## Pour changer le mot de passe

```shell
aws iam update-login-profile --user-name Hugo_Rihet --password
```

