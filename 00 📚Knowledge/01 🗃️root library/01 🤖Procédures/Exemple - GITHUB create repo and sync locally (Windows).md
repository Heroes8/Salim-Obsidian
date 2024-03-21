---
connie-publish: true
connie-page-id: "36248"
---

**Tags :** #Code #git
**Subject :**
**Created at :** *2023-07-13*
**Autors :** HUR

## Réccupérer l'URL du repo github

![[Pasted image 20230713165529.png]]


## Configuration de l'accès au repo

```powershell
 ssh-keygen
 cd "C:\Users\HugoRihet/.ssh"
 cat id_rsa.pub
 # Copier tout le contenu de la clé
```

![[Pasted image 20230713173226.png]]

![[Pasted image 20230713173312.png]]

![[Pasted image 20230713173401.png]]

![[Pasted image 20230713173513.png]]

## Configurer Git pour renseigner le username / mail de GutHub

```powershell
git config --global --edit
```


## Création du repo local et synchro avec le repo remote

```powershell
git --version
cd .\folder
git init
git commit -m "firt commit"
git branch -M main
git remote add origin git@github.com:goHuuu/AcademScripts.git
git push -u origin main
```

```ad-warning
![[Pasted image 20230713173836.png]]
Si vous avez ce message il faut modifier le fichier de conf de git pour y renseigner votre username et votre email GitHub 

```

```ad-warning
Si on a ce message d'erreur :
![[Pasted image 20230811151551.png]]
il faut faire cette commande


git config --global --add safe.directory '*'
```
