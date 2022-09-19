---
title: "Change Maker"
tags:
- kata
---

## Description du kata

L'objectif est de créer une machine permettant de calculer la monnaie à rendre lorsqu'on achète un article. Elle doit rendre le moins de pièce possible.

Elle peut être paramétrée pour fonctionner avec n'importe quelles pièces.

Par exemple : 

- British Pound  
	- 1,2,5,10,20,50  
- US Dollar  
	- 1,5,10,25
- Norwegian Krone  
	- 1,5,10,20

On estime que l'on possède chaque pièce en nombre infini.

## Pour démarrer : 
### Compréhension des règles métiers
Il est important de montrer que l'on a bien compris les exigences métiers. Comment peux-tu le prouver ?

Vois avec un pair si ta démonstration permet de montrer que tu as compris l'exercice

### Piste de départ
Il faut déjà déterminer le type du résultat de retour. Je conseille d'utiliser un dictionnaire, mais on peut utiliser toute sorte de type.


```java
var coinDenominations = [1,5,10,25]; // coin values converted to whole numbers  
var machine = new VendingMachine(coinDenominations);  
var purchaseAmount = 1.25; // amount the item cost  
var tenderAmount = 2.00; // amount the user input for the purchase  
var change = machine.CalculateChange(purchaseAmount, tenderAmount)

```


## Pour aller plus loin
Tu peux partir du principe que l'on dispose d'un nombre fini de pièce. Cela va changer la manière de gérer l'algorithme.

## Objectifs : 
Voici une liste d'objectifs que l'on peut se fixer lors de l'exercice. Tu peux sélectionner un ou plusieurs objectifs au fur et à mesure que tu évolues sur l'exercice.

- Terminer l'exercice
- Terminer l'exercice avec TDD
- Contrainte de vitesse
	- en moins de 60 minutes
	- en moins de 45 minutes
- Effectuer un refactoring qui permette d'améliorer la lisibilité (voir l'avis d'un pair pour validation)

## Références
### Internes
- Etude détaillée : en cours de réalisation

### Externes
- Adapté depuis : http://www.tddbuddy.com/katas/Change%20Maker.pdf


