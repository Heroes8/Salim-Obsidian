---
connie-publish: true
connie-page-id: "68644"
---
#ESXiSHELL #PowerCLI #Efrei #vSAN


## Contexte :

Afin de rélaiser un test de déploiement, il m'a été confié la tâche de prendre en main la mallette ILKI composée de 8 Intel NUC (mini PC), ainsi que d'un 9ième faisant office de master de gestion hébergeant les VM suivantes :
- labnas
- labadmin
- lab-pfsense

Pour se faire, il m'a donc fallu procéder à une résinstallaiton complête des hôtes *slave* en upgradant la version utilisée en l'etat (v6.5 vers v7u3)

Pour des raisons d'imcompatibilité matérielle, j'ai dû créer une nouvelle image ESXi custominsée incluant des driver de cartes réseau.

Voici le constat de l'echec en utilisant l'image d'instalation fournie dans le partage réseau de la mallette :
![[pb initial réinstall.jpg]]

```ad-note
Il est important de préciser que les plateformes de type *Intel NUC* ne sont à ce jour pas supporté par VMWare pour des déploiement d'ESXi, ce qui nous ammène à utiliser des plugin / driver communautaires
```

## Documentation et DAT :

Des articles et document d'architecture ont été utilisés pour la poursuite des étapes décrites dans ce document. Voici les **URL** accessible en ligne :
- https://infrageeks.com/post/2016-03-29.my-new-mobile-lab/
- https://infrageeks.com/post/2020-10-14.mobile-lab-v2-part-1/

ou en local :
- [[Lab mobile.pdf]] *(DAT)*
- [[Article 1 ERIK.pdf]] *(Article de blog)*
- [[Article 2 ERIK.pdf]] *(Article de blog)*

*Articles suggérés par Erik :* 
- Réseau :
	- https://williamlam.com/2022/02/usb-network-adapters-without-using-the-usb-network-native-driver-for-esxi.html
- Stockage :
	- https://www.v-front.de/p/esxi-customizer-ps.html

## Steps de réinstallation

Les étapes de la réinstallation ont été documentées dans cette note :
[[Process - create custom ESXi iso install]]

Le driver qui a été inclu dans la création de l'image d'installation est disponible à partir de cette URL : https://flings.vmware.com/community-networking-driver-for-esxi

*Documentation :* https://www.virten.net/2022/12/esxi-on-12th-gen-intel-nuc-pro-wall-street-canyon/

Celui-ci m'a permis de procéder à l'installation sans interruption, car la carte réseau interne des hôtes a été découverte.

Un second driver permettant de détécté la carte réseau externe (adaptateur USB 3.1 Ethernet) a également été uploadé sur chaque hôte et activé. Peut être réccupérer à partir de l'URL suivante :
https://flings.vmware.com/usb-network-native-driver-for-esxi

*Documentation :* https://www.virten.net/2020/04/how-to-add-the-usb-nic-fling-to-esxi-7-0-base-image/

```ad-note
Seulement 4 hôtes sur les 8 ont été capables de remonté le VMNIC correspondant à la carte externe suite à leur redémarrage
```


## Troubleshoot : Partie Stockage

### Cas de figure initial

**vSphere ne détecte pas de périphérique de stockage :**

![[Pasted image 20230118124506.png]]

```ad-note
Ce cas de figure est apparu sur les hôte suivants :
- lab1
- lab4
- lab5
- lab6
```

```ad-warning
Les autres hôtes de la mallette, rencontre un second problème de création de banque de données en utilisant les périphériques de stockage détéctés
```

**Vérification que le contrôleur SATA est détécté :**
- Input :
```shell
lspci -v | grep storage -A 1
```
- Output :
```shell
0000:00:17.0 Mass storage controller SATA controller: Intel Corporation Sunrise Point-LP AHCI Controller [vmhba0]
         Class 0106: 8086:9d03
```

**Vérification qu'un driver de stockage esiste :**
- Input :
```shell
localcli storage core adapter list
```
- Ouput :
```shell
HBA Name  Driver  Link State  UID  Capabilities  Description
--------  ------  ----------  ---  ------------  -----------
```
(Donc rien)

### Résolution des problèmes bloquant

Dans l'optique d'obtenir une configuraiton homogène entre les différents serveurs slave, nous devons réinistaller une deuxième fois chacun des serveurs en préparant une meilleure image custom cette fois si en v7.0.0 au lieu de v7u3 :

```powershell
 Import-Module VMWare.PowerCLI
 Get-EsxImageProfile
 Add-EsxSoftwareDepot .\ESXi-7.0.0-15843807-standard.zip
 Add-EsxSoftwareDepot .\ESXi703-VMKUSB-NIC-FLING-55634242-component-19849370.zip
 New-EsxImageProfile -CloneProfile "ESXi-7.0.0-15843807-standard" -name "ESXi-7.0.0-15843807-USBNIC" -Vendor "virten.net"
 Add-EsxSoftwareDepot .\ESXi-7.0.0-15843807-standard.zip
 Add-EsxSoftwareDepot .\ESXi700-VMKUSB-NIC-FLING-39035884-component-16770668.zip
 Add-EsxSoftwarePackage -ImageProfile "ESXi-7.0.0-15843807-USBNIC" -SoftwarePackage "vmkusb-nic-fling"
 Add-EsxSoftwareDepot .\Net-Community-Driver_1.2.7.0-1vmw.700.1.0.15843807_19480755.zip
Get-EsxSoftwareDepot
Add-EsxSoftwarePackage -ImageProfile "ESXi-7.0.0-15843807-USBNIC" -SoftwarePackage "net-community"
Add-EsxSoftwareDepot .\nvme-community-driver_1.0.1.0-3vmw.700.1.0.15843807-component-18902434.zip
```

## Install VCSA

- Supprétion de l'ancienne VM en 6.7
- Upload de l'iso d'installation 7.0.0 standard
- Lancement de l'insatlleur Windows GUI
- Config :
	- Deployment target:
		- ESXi Host : labmaster.mobile.lab
		- User name: root
		- Password: Infrageek$
	- Appliance VM
		- VM Name: vcsa-lab
		- root password: 1nfraGeek$
	- Deployment Size: Tiny
	- Datastore: vms
	- Network:
		- Network: Infrastructure
		- IP Version: v4
		- IP Assignment: static
		- System Name: vcsa-lab.mobile.lab
		- IP Address: 10.0.0.4
		- Subnet mask: 255.255.0.0
		- Default Gateway: 10.0.254.254
		- DNS Servers: 10.0.254.254

## Re troubleshooting sur la configuration du stockage vSAN

**En vérifiant le driver de stockage utilisé :**

```shell
HBA Name  Driver            Link State  UID          Capabilities  Description
--------  ----------------  ----------  -----------  ------------  -----------
vmhba32   vmkusb_nic_fling  link-n/a    usb.vmhba32                () Unknown
```

Cela est peut être dû à la ligne 29 de la 3ième phase :
```powershell
# Advanced setting to force the use of the old AHCI driver to avoid vSAN bugs
    # Ref: https://vdr.one/esxi-6-5-vmw_ahci-ssd-extreme-latency/
    $esxcli.system.module.set.Invoke(@{enabled='false';module='vmw_ahci'})
```

**En vérifiant les disques dispo :**

```shell
/dev/disks/mpx.vmhba32:C0:T0:L0
/dev/disks/mpx.vmhba32:C0:T0:L0:1
/dev/disks/mpx.vmhba32:C0:T0:L0:5
/dev/disks/mpx.vmhba32:C0:T0:L0:6
/dev/disks/mpx.vmhba32:C0:T0:L0:7
/dev/disks/vml.01000000004343364a5a52335a43304f56444646474d6173732053
/dev/disks/vml.01000000004343364a5a52335a43304f56444646474d6173732053:1
/dev/disks/vml.01000000004343364a5a52335a43304f56444646474d6173732053:5
/dev/disks/vml.01000000004343364a5a52335a43304f56444646474d6173732053:6
/dev/disks/vml.01000000004343364a5a52335a43304f56444646474d6173732053:7
```

On retrouve systématiquement le device ID corrrepondant à la clé USB d'installation :

![[Pasted image 20230131113814.png]]

**Résolution :**

Après réactivation de l'ancien module de controller de stockage (AHCI), et redémarrage d'un serveur on obtient :

```shell
[root@lab1:~] localcli storage core adapter list
HBA Name  Driver            Link State  UID          Capabilities  Description
--------  ----------------  ----------  -----------  ------------  -----------
vmhba0    vmw_ahci          link-n/a    sata.vmhba0                (0000:00:17.0) Intel Corporation Sunrise Point-LP AHCI Controller
vmhba32   vmkusb_nic_fling  link-n/a    usb.vmhba32                () Unknown
```

On vérifie donc les disques disponibles :
 ```shell
 [root@lab1:~] ls /dev/disks/* -d
/dev/disks/mpx.vmhba32:C0:T0:L0
/dev/disks/mpx.vmhba32:C0:T0:L0:1
/dev/disks/mpx.vmhba32:C0:T0:L0:5
/dev/disks/mpx.vmhba32:C0:T0:L0:6
/dev/disks/mpx.vmhba32:C0:T0:L0:7
/dev/disks/t10.ATA_____Samsung_SSD_850_EVO_500GB_______________S3R3NB0J903812E_____
/dev/disks/t10.ATA_____TS32GMTS800_____________________________D919530039__________
/dev/disks/vml.01000000004343364a5a52335a43304f56444646474d6173732053
/dev/disks/vml.01000000004343364a5a52335a43304f56444646474d6173732053:1
/dev/disks/vml.01000000004343364a5a52335a43304f56444646474d6173732053:5
/dev/disks/vml.01000000004343364a5a52335a43304f56444646474d6173732053:6
/dev/disks/vml.01000000004343364a5a52335a43304f56444646474d6173732053:7
/dev/disks/vml.0100000000443931393533303033392020202020202020202054533332474d
/dev/disks/vml.0100000000533352334e42304a39303338313245202020202053616d73756e
```

Le NVMe de mise en cache et le ssd de données sont donc dispo et affectables :

![[Pasted image 20230131115641.png]]

On vérifie également s'ils sont éligibles pour la réclamation au service vSAN :
```shell
[root@lab1:~]  vdq -q
[
   {
      "Name"     : "t10.ATA_____TS32GMTS800_____________________________D919530039__________",
      "VSANUUID" : "",
      "State"    : "Eligible for use by VSAN",
      "Reason"   : "None",
      "IsSSD"    : "1",
"IsCapacityFlash": "0",
      "IsPDL"    : "0",
      "Size(MB)" : "30533",
   "FormatType" : "512n",
   },

   {
      "Name"     : "mpx.vmhba32:C0:T0:L0",
      "VSANUUID" : "",
      "State"    : "Ineligible for use by VSAN",
      "Reason"   : "Has partitions",
      "IsSSD"    : "0",
"IsCapacityFlash": "0",
      "IsPDL"    : "0",
      "Size(MB)" : "14950",
   "FormatType" : "512n",
   },

   {
      "Name"     : "t10.ATA_____Samsung_SSD_850_EVO_500GB_______________S3R3NB0J903812E_____",
      "VSANUUID" : "",
      "State"    : "Eligible for use by VSAN",
      "Reason"   : "None",
      "IsSSD"    : "0",
"IsCapacityFlash": "0",
      "IsPDL"    : "0",
      "Size(MB)" : "476940",
   "FormatType" : "512n",
   },

]

```

Good !!! (plus qu'a le faire sur les autre)


## Réinstallation systématiquement failed sur lab4

Lors du redémarrage du 4ième hotes du cluster, il ne parvient plus à trouver sa partition EFI pour booter normalement.

![[20230206_155018.jpg]]

Pour le résoudre j'ai essayé de relancer une nouvelle réinstallation systématiquement interrompue par le message ci dessous

![[20230203_203045(1).jpg]]

## Groupe de disque réclamée pour le cluster vSAN en etat de défaillance

Après avoir créer le cluster vSAN sur les 7 serveurs restents, je remarque qu'après quelques jours, les disques de données réclamées par le groupe de disque affecté au stockage vSAN apparait en état deffectueux et du à une perte de disque permanente. 

![[Pasted image 20230206154700.png]]

L'erreur est présente sur 3 hôtes du cluster (lab2, lab3 et lab8) et rend disfonctiennel l'accès au datastore vSAN inaccessible en écriture même après suppression des groupes de disques d'hôte impactés

![[Pasted image 20230206155614.png]]