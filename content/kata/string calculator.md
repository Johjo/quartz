---
title: "string calculator"
tags:
- kata
---

## Description du kata
Kata string calculator est un kata incrémental. Il est décomposé en plusieurs parties qu'il faut faire les unes après les autres.

L'objectif du kata est de créer une calculatrice qui permet de sommer plusieurs nombres.


## Pour démarrer : 
### Compréhension des règles métiers
Il est important de montrer que l'on a bien compris les exigences métiers. Comment peux-tu le prouver ?

Vois avec un pair si ta démonstration permet de montrer que tu as compris l'exercice

### Piste de départ
**Attention, tant que cela n'est pas demandé, tu n'as pas besoin de gérer des valeurs invalides.**

#### Etape 1 - Additionner 0, 1 ou 2 nombres

Vous devez créer une méthode  `calculate` avec la signature suivante :

`int calculate(string expression)`

`expression` est une chaîne de caractères qui peut accepter 0, 1 ou deux nombres entiers séparés par une virgule. La méthode `calculate` doit renvoyer la somme de ces nombres.

Par exemple
```java
calculate("0") // doit retourner 0
calculate("1") // doit retourner 1
calculate("153") // doit retourner 153
calculate("23,541") // doit retourner 564
```

La chaîne vide est un cas particulier. On doit retourner la valeur 0.
```java
calculate("") // doit retourner 0`
```


#### Etape 2 - Additionner plusieurs nombres

Vous devez améliorer la méthode `calculate` pour que le paramètre `expression` puisse également recevoir plusieurs nombres

Par exemple
```java
calculate("1,3,154") // doit renvoyer 158
calculate("100,30,24,2") // doit renvoyer 156
```

> Astuce : vous avez terminé le développement lorsque vous exécutez `calculate` avec un nombre supplémentaire et que la fonction renvoie la bonne valeur


#### Etape 3 - Calculer sur plusieurs lignes

Les nombres du paramètre `expression` peuvent désormais être indiqués sur plusieurs lignes. La méthode `calculate` doit prendre en compte les retours chariots dans le calcul.

Par exemple
```java
calculate("1\n2") // doit renvoyer 3
calculate("1,2\n3") // doit renvoyer 6
calculate("1\n2,4") // doit renvoyer 7

calculate("1,\n) // cette utilisation est invalide. Pour info, il ne faut pas gérer le fait que l'expression est invalide.
```


#### Etape 4 - Gérer les expressions invalides

Cette étape est facultative et peut être gérée plus tard. Cependant, traiter les expressions invalides à cette étape permet de simplifier la suite du kata.

Une expression est invalide lorsqu'un des éléments n'est pas un nombre. La méthode doit déclencher une exception dans ce cas.

Le message de l'exception est : "Invalid expression :" suivi de l'expression invalide.

Par exemple:
``` 
calculate("1,") // doit déclencher l'exception : Invalid expression : '1,'
```

> Attention ! La chaîne vide est autorisée. On doit renvoyer 0 dans ce cas.


#### Etape 5 - Changer le délimiteur par défaut

On doit maintenant pouvoir changer le délimiteur par défaut (la virgule `,`)  par un autre délimiteur. Pour cela, on va autoriser la syntaxe suivante pour le paramètre `expression`. Cette nouvelle syntaxe est optionnelle : 

`//[delimiter]\n[numbers…]` où 
* `[delimiter]` correspond au délimiteur sur un seul caractère
* `[numbers]` correspond à la liste des nombres à aditionner

> Attention ! Toutes les précédentes expressions doivent toujours fonctionner.


Par exemple
```java
calculate("//;\n1;2") // doit renvoyer 3
calculate("//;\n1;2\n3") // doit renvoyer 6
calculate("//;\n102") // doit renvoyer 102
calculate("//@\n10@20@30") // doit renvoyer 60

// Les précédentes expressions doivent toujours fonctionner
calculate("1,2") // doit renvoyer 3

calculate("//;\n102,5") // cette utilisation est invalide. Le séparateur par défaut est ; et non plus ,
```


#### Etape 6 - Détecter les nombres négatifs

La méthode `calculate` doit désormais déclencher une exception quand `expression` contient un ou plusieurs nombres négatifs.

Le message de l'exception sera `negatives not allowed: ` suivi de la liste des nombres négatifs.

Par exemple : 
```java
calculate("1,-2") // doit déclencher l'exception : negatives not allowed: -2
calculate("5,-4,1,-2") // doit déclencher l'exception : negatives not allowed: -4, -2
```


#### Etape 7 - Ignorer les nombres supérieurs à 1000

La méthode `calculate` doit ignorer les nombres supérieurs à 1000.

Par exemple : 
```java
calculate("1000") // doit renvoyer 1000
calculate("1001") // doit renvoyer 0
calculate("54,1020") // doit renvoyer 54
```


#### Etape 8 - Délimiteur de taille variable

Le délimiteur personnalisé peut désormais être de n'importe quelle taille grâce à la syntaxe suivante : 

`//{[delimiter]}\n[numbers]` où
* `[delimiter]` est un délimiteur de n'importe quelle taille supérieure ou égale à 1
* `[numbers]` correspond à la liste des nombres à aditionner

> Attention, la syntaxe précédente doit toujours fonctionner.

Par exemple : 
```java
calculate("//{***}\n5***20") // doit renvoyer 25 (séparateur de 3 caractères)
calculate("//{*}\n5*20") // doit renvoyer 25 (séparateur de 1 caractère)
calculate("//*\n5*20") // doit renvoyer 25 (syntaxe précédente)
```

#### Etape 9 - Plusieurs délimiteurs

Il peut désormais y avoir plusieurs délimiteurs personnalisés grâce à la syntaxe suivante

`//{[delimiter 1]}{[delimiter 2]}\n[numbers]` où
* `[delimiter 1]` est le premier délimiteur de n'importe quelle taille égale à 1
* `[delimiter 2]` est le second délimiteur de n'importe quelle taille égale à 1
* `[numbers]` correspond à la liste des nombres à aditionner

Par exemple : 
```java
    calculate("//{+}{|}\n5+2|3") // doit renvoyer 10
```

#### Etape 10 - Plusieurs délimiteurs de taille variable

Les délimiteurs personnalisés peuvent désormais être de n'importe quelle taille grâce à la syntaxe suivante : 

`//{[delimiter 1]}{[delimiter 2]}\n[numbers]` où
* `[delimiter 1]` est le premier délimiteur de n'importe quelle taille supérieure ou égale à 1
* `[delimiter 2]` est le second délimiteur de n'importe quelle taille supérieure ou égale à 1
* `[numbers]` correspond à la liste des nombres à aditionner

## Pour aller plus loin
à définir

## Objectifs : 
Voici une liste d'objectifs que l'on peut se fixer lors de l'exercice. Tu peux sélectionner un ou plusieurs objectifs au fur et à mesure que tu évolues sur l'exercice.

- Terminer l'exercice
- Terminer l'exercice avec TDD
- Contrainte de vitesse
	- en moins de 45 minutes
	- en moins de 30 minutes
- Effectuer un refactoring qui permette d'améliorer la lisibilité (voir l'avis d'un pair pour validation)

## Références
### Internes
- Etude détaillée : en cours de réalisation

### Externes
- https://kata-log.rocks/string-calculator-kata
- https://codingdojo.org/kata/StringCalculator/

