---
title: "Installer approvaltests avec intellij"
date: "2022-05-07T22:47:23+02:00"
tags:
- golden-master
- approval-testing
- maven
- java
---
[[wiki/ApprovalTests|ApprovalTests]] est une librairie qui permet de simplifier la création de [[wiki/golden master|golden master]]. On s'en sert pour mettre en place des tests de caractérisation.

J'ai plusieurs fois galéré pour ajouter cette librairie dans intellij. Je pense que cela fait partie de l'utilisation standard, mais il est probable que d'autres personnes perdent du temps à trouver la réponse.

Dans un environnement **maven**, il faut procéder comme cela : 

1. Editer le fichier `pomxml` et ajouter 

```xml
<dependency>  
    <groupId>com.approvaltests</groupId>  
    <artifactId>approvaltests</artifactId>  
    <version>15.3.0</version>  
</dependency>
```

2. Dans intellij, recharger le projet (clic droit sur le fichier `pom.xml`).

3. Installer un outil de comparaison. Sous Windows, j'utilise [Winmerge](https://winmerge.org/?lang=fr)

4. Importer la classe `Approvals`  et utiliser sa méthode `verify`:

```java
import org.approvaltests.Approvals;

...

Approvals.verify("...");
```



## Reference
* https://github.com/approvals/ApprovalTests.Java

