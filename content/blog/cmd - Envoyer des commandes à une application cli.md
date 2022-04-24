---
title: "cmd : Envoyer des commandes à une application cli"
date: "2022-04-24T08:54:44+02:00"
draft: true
tags:
- windows
- claire
- cli
---

Créer un fichier command.txt qui contient les commandes 

```
load("hello")
main()
q
```

puis lancer les commandes avec 
```
cclaire.exe < command.txt
```



## Références
* https://stackoverflow.com/questions/10552812/defining-and-using-a-variable-in-batch-file
