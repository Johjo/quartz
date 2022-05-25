---
title: "Bowling"
---

## Description du kata
Nous souhaitons calculer le **score final** d'une partie de **bowling**. Pour cela, nous allons créer une application qui nous permettra de simuler une partie de bowling pour un joueur.

### Règles du bowling
- Une partie de bowling se divise en **10 carreaux** (**frame** en anglais).
- Chaque carreau démarre avec **10 quilles à faire tomber** (**pin** en anglais)
- Un carreau se termine dans l'une des conditions suivantes :
	- 2 lancers ont été effectués (**roll** en anglais)
	- les 10 quilles sont tombées
- on **ajoute au score le nombre de quilles tombés lors du carreau**
- un abat se produit lorsqu'on fait tomber les **10 quilles au premier lancer du carreau** (**strike** en anglais)
- lors d'un abat, on ajoute au score du carreau un **bonus** qui correspond aux nombres de quilles tombées lors **des deux prochains lancers**
- une réserve se produit lorsqu'on fait les **10 quilles au second lancer du carreau** (**spare** en anglais)
- lors d'une réserve, on ajoute au score du carreau un **bonus** qui correspond aux nombres de quilles tombées **lors du prochain lancer**
- si l'on fait un abat lors du dixième carreau, on a **deux lancers supplémentaires** pour calculer le bonus
- si l'on fait une réserve lors du dixième carreau, on a **un lancer supplémentaire** pour calculer le bonus

## Pour démarrer : 
### Compréhension des règles métiers
Il est important de montrer que l'on a bien compris les exigences métiers. Comment peux-tu le prouver ?

Vois avec un pair si ta démonstration permet de montrer que tu as compris l'exercice

### Point de départ
Tu vas implémenter une classe `Bowling` qui aura les deux méthodes suivantes : 
- `void roll(int pins)` : cette méthode permet d'enregistrer un lancer
- `int score()` : cette méthode donne le score de la partie une fois **celle-ci terminée**.

## Objectifs : 
Voici une liste d'objectifs que l'on peut se fixer lors de l'exercice. Tu peux sélectionner un ou plusieurs objectifs au fur et à mesure que tu évolues sur l'exercice.

- Terminer l'exercice
- Terminer l'exercice avec TDD
- Contrainte de vitesse
	- en moins de 45 minutes
	- en moins de 30 minutes
- Effectuer un refactoring qui permette d'améliorer la lisibilité et d'ajouter de l'abstraction (voir l'avis d'un pair pour validation)


## Références
### Internes
- [[blog/etude du kata bowling - one test|Etude du kata bowling en un seul test]]

### Externes
- http://butunclebob.com/ArticleS.UncleBob.TheBowlingGameKata
- https://sites.google.com/site/unclebobconsultingllc/home/articles/the-bowling-game-an-example-of-test-first-pair-programming


