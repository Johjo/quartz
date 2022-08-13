---
title: "TDD - la Technique du Double Dragon"
date: "2022-08-13T06:49:18+02:00"
draft: true
tags:
- TDD
---

La technique du double dragon, aussi appelée la technique de la triche constante, ou la technique du double code m'a permis de me simplifier la vie dans la pratique du Test Driven Development. 

Elle repose sur les principes suivants : 
- Red - Green - Refactoring
- TPP
- La maîtrise du code
- La clarification
- Les baby steps
- La mise en évidence de la duplication
- La suppression de la duplication
- La triche
- Le recyclage de la triche



## Pourquoi cette technique ?
Lorsque je code, je vise la fluidité maximale, c'est à dire que mon taux d'interruption pour savoir quel code écrire doit être minimal. Je ne veux pas qu'après avoir écrit un test, je me demande comment le faire passer. Pour arriver à cela, la phase Green doit être la plus rapide possible, de l'ordre des 10 secondes, écriture du code comprise.

Bien sûr, il y a des limites. Avec un langage très verbeux, ce temps est rallongé. La complexité du code et l'architecture que l'on utilise impacte aussi la fluidité. 

Quoiqu'il en soit, mes premières expérimentations me donnent l'impression d'être littéralement guidé par les tests. Ma réflexion est minimale lors de l'étape Green. Au contraire, c'est plutôt mécanique car j'ai une petite liste de règles que j'applique pour faire évoluer le code.

## Comment ça fonctionne ?
Avant d'aller plus loin dans le fonctionnement (désolé pour le teasing), il faut déjà connaître les principes que j'applique. Je vais essayer de les résumer en quelques mots.

### Red - Green - Refactoring
C'est le principe même de TDD. Si tu ne le connais pas, je t'invite à étudier cette méthode avant de poursuivre l'article, car tout repose dessus.

- Red : On pose un test qui échoue
- Green : On écrit le code de production qui fait passer ce test
- Refactoring : On retravaille le code de test et de production pour l'améliorer 

L'étape Green est souvent très compliquée pour les dévellopeur.e.s parce qu'il faut trouver le code à écrire. Une part importante de la reflexion se trouve ici. Je pense que ce n'est pas la bonne manière de faire. Cette étape doit être la plus rapide possible. et doit se faire sans réflexion. Si tu dois réfléchir, fais le aux deux autres étapes.

L'étape Red consiste à faire apparaître les concepts du code au moyen des tests. Si l'on s'y prend mal, on peut en faire apparaître plusieurs en même temps. Dans ce cas, la phase Green devient plus complexe. Il faut prendre du temps pour écrire le test qui introduit le moins de concept possible par rapport au code actuel.

L'étape Refactoring consiste à retravailler le code. On va faire évoluer la structure du code (et non pas le comportement) pour se simplifier la vie. Une grosse part de la réfléxion doit se trouver ici.

L'une des erreurs que je rencontre souvent, c'est de pratiquer la phase Refactoring en même temps que la phase Green. Le développeur cherche à écrire un code avec la meilleure structure possible du premier coup. 

Bien qu'on puisse appliquer des règles précises, la structure du code peut évoluer dans plusieurs directions. Les TPP (transformation priority premise) nous aident à choisir une structure cohérente. 

### TPP - Transformation Priority Premise

Les TPP, c'est une liste écrite par Robert C. Martin aka Uncle Bob. Elle indque  les transformations que l'on peut appliquer au code afin de le faire évoluer. Elles sont ordonnées par priorité d'utilisation. Plus on descend dans la liste, plus les transformations rendent le code générique.

Il est très important de maîtriser cette liste. Si tu ne connais pas, je t'invite à te renseigner dessus. Je trouve qu'il y a très peu de publications à ce sujet. Ce qui est dommage car le concept n'a pas été assez poussé.

Il vaut mieux lire l'article suivant avant de continuer : https://blog.cleancoder.com/uncle-bob/2013/05/27/TheTransformationPriorityPremise.html

Pour moi, cette liste est incomplète mais elle reste fondamentale et je retarde au maximum l'application de certaines transformations. On a tendance à les utiliser lors de l'étape Green.  Pour ma part, **je les applique lors de l'étape du refactoring**.

J'applique quelque chose qui ressemble à ça : 
- Constante : j'indique la valeur attendue
- Variable : j'extrais une variable depuis une constante
- Décomposition : Si la variable est construite à partir de plusieurs valeurs, je la décompose
```
word = "fizzbuzz"
```

donne 
```
word = ""
word += "fizz"
word += "buzz"
```
- Variable un peu plus complexe : j'introduis à ce stade un tableau ou une map
- Condition : j'ajoute une condition, parfois avec un else
- Fonction : lorsque un concept apparaît clairement, je fais un extract méthode. Cette étape est subtile. Trop tôt et le code devient plus compliqué à faire évoluer, trop tard et on perd du temps à cause de la duplication de code
- Boucle: J'attend le dernier moment pour faire apparaître une boucle. En général, les tests ne vont que sur quelques itérations. Dans ce cas, un simple copier coller du traitement d'une itération permet de traiter un élément de plus

Cette liste n'est pas exhaustive, c'est un ordre d'idée. Tout dépend du code sur lequel on travaille. L'entraînement permet de la faire évoluer et de mieux gérer les transformations. Et je le répète, on transforme à l'étape du refactoring (ou du green si c'est évident).

### La maîtrise du code
Ce principe est l'un des piliers du développement que chaque développeur.e devrait maîtriser. Mais qu'est-ce que c'est ?

C'est tout simplement la capacité de se passer de debugger. Le code est un ensemble d'actions qui sont effectuées selon certaines conditions. Lorsqu'on écrit un test, on attend un résultat. Celui-ci est en général obtenu à partir d'un ensemble de transformations (je ne parle pas des TPP ici) que l'on appelle instruction. Tu dois être en mesure de pouvoir dire en permanence où ces transformations ont lieu, sinon, tu n'as pas la maîtrise du code.

Cette compétence ne 