---
connie-publish: true
connie-page-id: "36517"
---

**Tags :** #DailyNote 
**Subject** *(Eventual) :*  Comment expliquer en quoi le partage de connaissance dépend autant du support de l'information (donc de l'outil) que la manière de l'uiliser et de le faire adopter (point de vue organisationnel)
**Created at :** *2023-08-15*

# Content :

**Source :** https://youtu.be/C9EjXYZUqUs
- Architecture d'un ordinateur : short term memory + long term memory :
	- long term = mémoire morte (HDD harddrive / SSD flashdrive)
		- Capacity : Big
		- Access speed : Slow
		- Type of information stored : **Anything**
		- All content is stored and will survive even if the the system has been shutdown
	- short term = Random access memory (RAM)
		- Capacity : Small 
			- needs to **evicts data often**
			- memory managment problems often causes stack overflow errors in certain processes
		- Accees speed : super fast
		- Type of information stored : **Information directly used by the CPU**
		- All content is lost when system is powered off
- **SWAP / page file** is an extention of short term memory (RAM) stored on long term memory (disk)
	- slower than RAM medium
	- But : provides space for **seldom-used data** that still needs to be at hand

![[Comic strip RAM - SWAP - Harddisk]]