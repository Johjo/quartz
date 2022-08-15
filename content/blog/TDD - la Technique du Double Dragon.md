---
title: "TDD - la Technique du Double Dragon"
date: "2022-08-13T06:49:18+02:00"
tags:
- TDD
---

La technique du double dragon, aussi appelée la technique de la triche constante, ou la technique du double code m'a permis de me simplifier la vie dans la pratique du Test Driven Development. 

## TL;DR

Si tu n'as pas le temps de lire tout l'article, je te montre un exemple de la technique dans cette vidéo, sur le kata prime factor en python : 

https://www.youtube.com/watch?v=iP7FW1PPdVo

## Pourquoi cette technique ?
Lorsque je code, je vise la fluidité maximale, c'est à dire que mon taux d'interruption pour savoir quel code écrire doit être minimal. Je ne veux pas qu'après avoir écrit un test, je me demande comment le faire passer. Pour arriver à cela, la phase Green doit être la plus rapide possible, de l'ordre de la diazine de secondes, écriture du code comprise.

Bien sûr, il y a des limites. Avec un langage très verbeux, ce temps est rallongé. La complexité du code et l'architecture que l'on utilise impacte aussi la fluidité. 

Quoiqu'il en soit, mes premières expérimentations me donnent l'impression d'être littéralement guidé par les tests. Ma réflexion est minimale lors de l'étape Green. Au contraire, c'est plutôt mécanique car j'ai une petite liste de règles que j'applique pour faire évoluer le code.

## Comment ça fonctionne ?
Avant d'aller plus loin dans le fonctionnement (désolé pour le teasing), il faut déjà connaître les principes que j'applique. Je vais essayer de les résumer en quelques mots.

Elle repose sur les principes suivants : 
- Red - Green - Refactoring
- Transformation Priority Premise (TPP)
- La maîtrise du code
- Les baby steps
- La triche
- La clarification
- La mise en évidence de la duplication
- La suppression de la duplication
- Le copier / coller

### Red - Green - Refactoring
C'est le principe même de TDD. Si tu ne le connais pas, je t'invite à étudier cette méthode avant de poursuivre l'article, car tout repose dessus.

- Red : On pose un test qui échoue
- Green : On écrit le code de production qui fait passer ce test
- Refactoring : On retravaille le code de test et de production pour l'améliorer 

L'étape Green est souvent très compliquée pour les dévellopeur.e.s parce qu'il faut trouver le code à écrire. J'ai constaté que l'on passe parfois un long temps de réfléxion pour valider l'étape Green. Je pense qu'au contraire, cette étape devrait être la plus rapide possible.  

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

Cette compétence n'est pas simple à acquérir, mais elle est primordiale. Pour la maîtriser, voici quelques conseils : 
- Diminue, voire interdit l'utilisation du debugger
- Extrais les bonnes abstractions au bon moment
- Suis les TPP et ne rend pas ton code trop générique trop tôt

### Les baby-steps
Si l'on souhaite coder de manière fluide, ce principe est incontournable. Comme son nom l'indique, il consiste à faire des pas de bébé dans le code. Mais qu'est-ce que cela signifie réellement ?

Le code doit être validé dès qu'il est écrit. On évite donc d'écrire de trop gros morceaux et on vérifie que tout fonctionne dès qu'une simple ligne de code est écrite, voire un simple mot. Pour le valider, on lance tout simplement les tests.

En fait, si on réfléchit bien, **on n'a pas inventé les baby-steps pour faire du TDD, on a inventé TDD pour faire des baby-steps**. Et j'ai mis au point ma technique pour me permettre de faire les pas les plus petits possible.

Mais pourquoi les baby-steps sont-ils aussi importants ? Tout simplement parce qu'ils permettent d'éviter l'effet tunnel, ce moment où on plonge la tête dans le code et qu'on se rend compte qu'il s'est passé un mois avant d'obtenr un code qui marche.

Je veux un code qui marche en moins de 30 secondes. Parce que si je me suis planté, je ne jette que 30 secondes de travail. Si mon code marche, je peux le publier au bout de 30 secondes.

Et cela marche parce que le code peut évoluer de manière incrémentale. C'est vraiment un concept de l'informatique que j'adore, cette possibilité de faire évoluer les choses au fur et à mesure et d'ajuster au bout de quelques secondes.

### La triche
Cette technique, très pratiquée lorsqu'on fait du TDD, est pour moi de la triche. Elle permet de terminer l'étape Green de TDD très rapidement. Elle est à la base de ma technique. Voyons voir en quoi elle consiste.

L'idée générale du développement consiste à effectuer un traitement sur des données en entrée pour obtenir des sorties. Lorsqu'on pratique TDD, le test met en évidence une entrée et la sortie attendue. Tricher consiste tout simplement à retourner directement la sortie, quitte à mettre une condition basée sur l'entrée.

Par exemple, dans le cas du fizzbuzz : 

```python
def fizzbuzz(number):
	# je triche ici
	if number == 15:
		return "fizzbuzz"

	# reste du code
```

C'est tout simple, mais ça fait gagner beaucoup de temps. Et si tu lis mes autres articles, cette technique permet d'aller beaucoup plus loin avec la clarification.

### La clarification
La clarification se réalise lors de l'étape du Refactoring. Elle consiste tout simplement à comprendre ce que représente une valeur et à indiquer d'où elle vient. Clarifier consiste souvent à appliquer une des TPP (jusqu'à ajouter une condition incluse).

La clarification va de pair avec la maîtrise du code, car il faut parfaitement comprendre le code pour savoir ce que représente quoi.

Par exemple, dans le cas du kata bowling où l'on calcule un score, on triche en renvoyant le score puis on clarifie le calcul du score.
```python
class Bowling:
	def score(self):
		if self.rolls == [1, 2] # La liste est beaucoup plus longue
			return 3
```

Que représente ce `3`  ?
```python
class Bowling:
	def score(self):
		if self.rolls == [1, 2] # La liste est beaucoup plus longue
			return 1 + 2
```

Que représentent ce `1`  et ce `2`?
```python
class Bowling:
	def score(self):
		if self.rolls == [1, 2] # La liste est beaucoup plus longue
			return self.rolls[0] + self.rolls[1]
```

### La mise en évidence de la duplication
Dans un morceau de code, il arrive souvent qu'il y ait de la duplication de code, mais il arrive parfois que cette duplication ne soit pas visible. Il faut alors la mettre en évidence.

Par exemple, dans le cas de fizzbuzz :

```python
def fizzbuzz(number):
	if number % 15 == 0:
		return "fizzbuzz"
	if number % 3 == 0
		return "fizz"
	if number % 5 == 0
		return "buzz"
	... 
```

Dans le code suivant, les termes fizz et buzz apparaissent plusieurs fois. Faire apparaître la duplication revient à décomposer : 

```python
def fizzbuzz(number):
	if number % 15 == 0:
		word = ""
		word += "fizz"
		word += "buzz"
		return word
	if number % 3 == 0
		word = ""
		word += "fizz"
		return word
	if number % 5 == 0
		word = ""
		word += "buzz"
		return word
	... 
```

Après décomposition, les blocs se ressemblent beaucoup plus. Le refactring *extract variable* et la *décomposition* sont deux techniques que j'utilise pour mettre en évidence la duplication.

### La suppression de la duplication
Après avoir mis en évidence qu'il y a du code dupliqué, on peut supprimer la duplication. Bien entendu, quand on parle de supprimer la duplication, il faut le prendre avec des pincettes. Il n'est pas nécessaire de partir à la chasse à la duplication.

Dans l'exemple précédent du *fizzbuzz*, la première version de l'algorithme est satisfaisante, mais la seconde version permet d'arriver sur un code plus générique qui aura vocation à évoluer plus facilement, à condition qu'on extrait les bonnes abstractions.

La mise en évidence comme la suppression de la duplication nécessitent de l'entraînement et de l'expérience pour être maîtrisés.

### Le copier-coller
Le copier-coller est une des merveilles apportées par l'ère de l'informatique. Je te conseille de l'utiliser sans arrêt. En l'utilisant, tu mets en évidence qu'il y a de la duplication.

## La technique du double dragon
Alors, en quoi consiste cette fameuse technique ? Tout simplement à appliquer tous les principes énoncés plus haut en même temps.

Je vous fait en parallèle un exemple avec le minesweeper.

On commence par appliquer *Red - Green - refactoring* afin de créer le walking skeleton du code. On utilise ensuite ce processus tout le reste du développement. Pour la résolution du premier test, on suit les TPP, et on commence par écrire un code qui ne fait rien puis un code qui renvoie une constante.

```python
def test_solve_an_empty_field():
    assert minesweeper("") == ""

def minesweeper(field):
    return ""
```
Une fois qu'on a fait passer notre premier test, notre deuxième test arrive. On peut commencer à appliquer la technique du double dragon (j'adore ce nom). Il suffit tout simplement de tricher.

```python
def test_solve_a_cell_with_mine():
    assert minesweeper("*") == "*"
    
def minesweeper(field):
	# La triche commence ici
	if field == "*":
        return "*"

	# Le code générique commence ici
	return ""
```

La triche est mise en place grâce à la condition `if field == "*":`. Ici, cette condition n'a rien à voir avec les TPP. Ce n'est pas une transformation du code, c'est de la triche. Mais cela nous permet de passer l'étape du *Green* très rapidement.

Suivant les cas, on peut parfois se retrouver avec plusieurs cas de triche afin de mieux comprendre le code.

On va maintenant appliquer la mise en *évidence de la duplication* avec un *extract variable* et la *décomposition*.

```python
def minesweeper(field):
	# La triche commence ici
    if field == "*":
        solved_field = ""
        solved_field += "*"
        return solved_field

	# Le code générique commence ici
    solved_field = ""
    return solved_field
```

La duplication est visible, mais pas assez, on va la mettre encore plus en évidence.

```python
def minesweeper(field):
    # La triche commence ici
    if field == "*":
        solved_field = ""
        if True:
            solved_field += "*"
        return solved_field
    
    # Le code générique commence ici
    solved_field = ""
    if False:
        solved_field += "*"

    return solved_field
```

On applique une TPP de priorité moins grande, l'ajout d'une condition car on n'a pas le choix. (Si quelqu'un sait faire passer cette étape sans le if, je suis preneur)

Le *True* et le *False* sont là pour nous permettre de faire des baby steps. J'ai donc un copier-coller de mon code de triche et de mon codé générique. Il faut chercher une condition (pas forcément la vraie condition) qui marche dans les deux cas.

Dans le cas présent, c'est une condition sur la taille de la chaîne qui fonctionne.

```python
def minesweeper(field):
    # La triche commence ici
    if field == "*":
        solved_field = ""
        if len(field) > 0:
            solved_field += "*"
        return solved_field
    
    # Le code générique commence ici
    solved_field = ""
    if len(field) > 0:
        solved_field += "*"

    return solved_field
```

La technique du double dragon est en place. Nous pouvons passer au test suivant.

```python
def test_solve_a_cell_without_mine():
    assert minesweeper(".") == "0"
```

Et le code qui fait passer ce test : 

```python
def minesweeper(field):
    # La triche commence ici
    if field == ".":
        solved_field = ""
        if len(field) > 0:
            solved_field += "0"
        return solved_field
    
    # Le code générique commence ici
    solved_field = ""
    if len(field) > 0:
        solved_field += "*"

    return solved_field
```
La première chose à comprendre, c'est que j'ai recyclé le bloc de triche. J'ai remplacé la condition pour être dans le cas du nouveau test.

La seconde chose, c'est que j'ai ajouté ce qu'il faut dans le code pour que ça passe automatiquement. Je n'ai pas cherché à écrire le bon code directement. J'ai cherché ce qu'il fallait changer dans le code existant pour que le test passe. 

Ce changement nous donne d'ailleurs des informations sur la TPP que l'on va choisir pour réaliser le code.

Je met en évidence la duplication en utlisant la technique du *True* et *False* : 
```python
def minesweeper(field):
    # La triche commence ici
    if field == ".":
        solved_field = ""
        if len(field) > 0:
            if True:
                solved_field += "0"
            else:
                solved_field += "*"
        return solved_field
    
    # Le code générique commence ici
    solved_field = ""
    if len(field) > 0:
        if False:
            solved_field += "0"
        else:
            solved_field += "*"

    return solved_field
```

Puis j'enlève la duplication en trouvant la bonne condition : 
```python
def minesweeper(field):
    # La triche commence ici
    if field == ".":
        solved_field = ""
        if len(field) > 0:
            if field[0] != "*" :
                solved_field += "0"
            else:
                solved_field += "*"
        return solved_field
    
    # Le code générique commence ici
    solved_field = ""
    if len(field) > 0:
        if field[0] != "*" :
            solved_field += "0"
        else:
            solved_field += "*"

    return solved_field
```
Les deux codes étant alignés, je peux maintenant passer au test suivant et ainsi de suite. Je recycle le code de triche à chaque fois, c'est lui qui évolue.

A noter que dans certains cas, il peut y avoir plusieurs étapes avant de pouvoir aligner le code de triche et le code générique.

## Exemple 

Voici un exemple de la technique du double dragon en python sur le kata prime factor : 

https://www.youtube.com/watch?v=iP7FW1PPdVo

Voici un second exemple. En fait, c'est durant cet exercice que j'ai finalisé la technique du double dragon.

Je l'effectue sur le kata minesweeper en rust : 

https://www.youtube.com/embed/-VSavtR5aCY

Le code de ce dernier kata est accessible sur cyberdojo avec chaque étape ici : [accéder au code]([https://cyber-dojo.org/kata/edit/aR5mXj](https://www.youtube.com/redirect?event=video_description&redir_token=QUFFLUhqbngzZTBOV2cxYVlwQ0xEcVgycG40X0RhQkZDZ3xBQ3Jtc0tubWlJWVFEdU1oYUVYdFVJRDNCVjg4WnFhOEQ3Sko2VlkwdlljWnRWdUhXX1ZSeXROYzNSRlVQeW5YUUFUemlweXhINnhPM3lzb283LXlXVVpvbEEtZ1ctZmEzSmg1NUlCaml0QUhpYThwM2pqWC14NA&q=https%3A%2F%2Fcyber-dojo.org%2Fkata%2Fedit%2FaR5mXj&v=-VSavtR5aCY))

