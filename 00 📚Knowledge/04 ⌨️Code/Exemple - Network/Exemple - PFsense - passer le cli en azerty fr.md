
**Tags :** #Code
**Subject :**
**Created at :** *2023-10-18*
**Autors :** ben HUR


```
https://blogmotion.fr/internet/pfsense-clavier-azerty-16564

- Web Console Interface : 
	- Install package : Shellcmd : menu System > Package Manager > Available Packages.
	- once the package is installed : Services > Shellcmd add entry:

- command: 
	- kbdcontrol -l /usr/share/syscons/keymaps/fr.iso.kbd 
- Shellcmd Type: shellcmd 
- Description: Azerty

Then reboot the server.
```

Source : https://docs.netgate.com/pfsense/en/latest/troubleshooting/locked-out.html