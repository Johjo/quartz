---
title: "Etude du kata bowling (en un seul test)"
date: "2022-05-02T15:46:23+02:00"
tags:
- étude-de-kata
- refactoring
- tricher-avec-tdd
- clarification
---

Aujourd'hui, nous allons étudier le [[kata/bowling|kata bowling]]. Mais nous allons le faire d'une manière un peu différente :  **avec un seul test**.

Nous sortons donc des sentiers battus du TDD pour mettre en évidence une technique de développement basée sur le refactoring, et en particulier la triche (utilisée dans l'étape *GREEN*) et la clarification (utilisée dans l'étape *REFACTORING*).

Chaque fois que vous voyez un nouveau morceau de code, les tests (bon, il n'y en aura qu'un seul) sont lancés à chaque fois. 

Le code est présenté en mode diff. Les `+` indiquent le code qui a été ajouté et les `-` le code qui a été enlevé.

## On démarre la partie

Tout commence donc par un test (le seul test de cette étude). 

```diff
+import bowling.Bowling;  
+import org.junit.Test;  
+  
+public class TestBowling {  
+  
+    @Test  
+    public void one_game() {  
+        Bowling game = new Bowling();  
+    }  
+  
+}
```

Dans l'état, nous sommes donc à l'étape RED, le test échoue car la classe Bowling n'existe pas. Créons donc la classe qui va avec :

```diff
+package bowling;  
+  
+public class Bowling {  
+}
```

Et nous passons au vert.

Nous allons maintenant jouer au bowling. La méthode `roll` doit permettre d'indiquer le nombre de quilles que l'on fait tomber à chaque lancer.

On rajoute donc au test tous les lancers nécessaires pour faire une partie. Il y a des strikes , des spares, des coups normaux... Bref, c'est le genre de partie que je joue quand je suis chanceux ;)

```diff
@Test  
public void one_game() {  
    Bowling game = new Bowling();  
  
+   game.roll(3);  
+   game.roll(4);  
+   game.roll(6);  
+   game.roll(3);  
+   game.roll(2);  
+   game.roll(8);  
+   game.roll(4);  
+   game.roll(3);  
+   game.roll(4);  
+   game.roll(4);  
+   game.roll(5);  
+   game.roll(2);  
+   game.roll(6);  
+   game.roll(4);  
+   game.roll(10);  
+   game.roll(8);  
+   game.roll(0);  
+   game.roll(10);  
+   game.roll(8);  
+   game.roll(1);  
  
}
```

Et on passe du *RED* au *GREEN* en ajoutant la méthode `roll` dans la classe `Bowling`.

```diff
public class Bowling {  
+    public void roll(int pin) {  
+  
+    }  
}
```

Enfin, on vérifie que le score calculé est le bon : 

```diff
@Test  
public void one_game() {  
    Bowling game = new Bowling();  
  
    game.roll(3);  
    game.roll(4);  
    game.roll(6);  
    game.roll(3);  
    game.roll(2);  
    game.roll(8);  
    game.roll(4);  
    game.roll(3);  
    game.roll(4);  
    game.roll(4);  
    game.roll(5);  
    game.roll(2);  
    game.roll(6);  
    game.roll(4);  
    game.roll(10);  
    game.roll(8);  
    game.roll(0);  
    game.roll(10);  
    game.roll(8);  
    game.roll(1);  
  
+   Assert.assertEquals(117, game.score());  
}
```
On tombe sur une erreur de compilation (étape *RED*), on crée la méthode `score` et on fait échouer l'assert (toujours *RED*): 

```diff
public class Bowling {  
    public void roll(int pin) {  
  
    }  
  
+   public int score() {  
+       return -1;  
+   }  
}
```

Et voilà, on peut rentrer dans le vif du sujet, on a fini de mettre en place nos tests. Nous allons pouvoir tricher allègrement.

## Je triche, tu triches, nous trichons
Le passage au vert se fait de la manière la plus simple, en indiquant le résultat attendu.

```diff
public int score() {  
-   return -1;  	
+   return 117;  
}  
```
On passe de l'étape *RED* à l'étape *GREEN* en 2 secondes. Voilà, nous venons de pratiquer ce que j'appelle la **triche**. Indiquer directement dans le code le résultat attendu.

À partir de maintenant, nos tests doivent toujours passer. Nous entrons dans la phase du *REFACTORING*. Et nous n'en ressortirons pas tant que notre algo ne sera pas terminé.

## Clarifions... Quoi ? Comment ?
> Dev : C'est bien beau ce 117, mais je ne vois pas d'algo là-dedans
> Moi : C'est vrai, mais pourtant, l'algo est là, mais tu ne le vois pas encore
> Dev : ???
> Moi : Petite question, c'est quoi ce 117 ?
> Dev : 117 ? C'est le score total de ma partie de bowling...

Bingo ! Nous venons d'attaquer la phase de **clarification** qui est une étape de la phase de *REFACTORING*. Nous sommes en train de parler du **QUOI**. Et cela nous permet d'introduire un nouveau concept : le score total de la partie (bon, je sais, la méthode s'appelle score, c'était facile...). 

Mettons en évidence ce nouveau concept, à l'aide d'un [[wiki/extract variable|extract variable ]]: 

```diff
public int score() {  
-   return 117;  
+   int score = 117;  
+   return score;  
}
```

> Dev : Tu as toujours ce 117...
> Moi : C'est vrai, mais comment arrive-t-on à ce résultat ?
> Dev : Il faut ajouter le résultat du premier lancer puis du second ....
> Moi : C'est vrai, mais ton niveau d'abstraction est trop haut, c'est trop tôt pour en parler
> Dev : ???
> Moi : Le résultat du premier lancer, c'est 3 et le second, c'est 4
> Dev ???????
> Moi : 117, c'est égal à 3 + 4 + 6 + ... + 10 + 8 + 1
> Dev : ?????????????????????
> Moi : Oui, pour calculer le score, il faut connaître les règles du bowling et savoir faire le calcul à la main

Si on traduit cela en code, ça nous donne. En guide d'exercice, tu peux repartir de notre jeu de test et voir comment on calcule cela : 

```diff
public int score() {  
-   int score = 117;  
+   int score = 3 + 4 + 6 + 3 + 2 + 8 + 4 + 4 + 3 + 4 + 4 + 5 + 2 + 6 + 4 + 10 + 10 + 8 + 0 + 8 + 0 + 10 + 8 + 1;  
    return score;  
}
```

Pour réussir cela, nous avons introduit la notion de **COMMENT**. Mais attention, il ne faut pas changer de niveau d'abstraction trop rapidement. Si c'est possible, on introduit l'un après l'autre.

Organisons un peu le code pour faire apparaître le **QUOI**.

```diff
public int score() {  
-   int score = 3 + 4 + 6 + 3 + 2 + 8 + 4 + 4 + 3 + 4 + 4 + 5 + 2 + 6 + 4 + 10 + 10 + 8 + 0 + 8 + 0 + 10 + 8 + 1;  

+   int score = 0;  
  
+   score += 3;  
+   score += 4; 
+   score += 6;  
+   score += 3;  
+   score += 2;  
+   score += 8;  
+   score += 4;  
+   score += 4;  
+   score += 3;  
+   score += 4;  
+   score += 4;  
+   score += 5;  
+   score += 2;  
+   score += 6;  
+   score += 4;  
+   score += 10;  
+   score += 10;  
+   score += 8;  
+   score += 0;  
+   score += 8;  
+   score += 0;  
+   score += 10;  
+   score += 8;  
+   score += 1;  
  
    return score;  
}
```

Et encore une étape (pour rappel, le test passe toujours) : 

```diff
public int score() {  
    int score = 0;  
  
+   // frame 1  
    score += 3; // roll 1  
    score += 4; // roll 2  
	  
+   // frame 2  
    score += 6; // roll 3  
    score += 3; // roll 4  
	  
+   // frame 3  
    score += 2; // roll 5  
    score += 8; // roll 6 (spare)  
    score += 4; // roll 7 (spare bonus)  
	  
+   // frame 4  
    score += 4; // roll 7  
    score += 3; // roll 8  
	  
+   // frame 5  
    score += 4; // roll 9  
    score += 4; // roll 10  
	  
+   // frame 6  
    score += 5; // roll 11  
    score += 2; // roll 12  
	  
+   // frame 7  
    score += 6; // roll 13  
    score += 4; // roll 14 (spare) 
    score += 10; // roll 15 (spare bonus)   
	  
+   // frame 8  
    score += 10; // roll 15 (strike)  
    score += 8; // roll 16 (strike bonus)  
    score += 0; // roll 17 (strike bonus)  
	  	  
+   // frame 9  
    score += 8; // roll 16  
    score += 0; // roll 17   
	  
+   // frame 10  
    score += 10; // roll 18 (strike)  
    score += 8; // roll 19 (strike bonus)  
    score += 1; // roll 20 (strike bonus)
  
    return score;  
}
```

En séparant les différents lancers, nous faisons apparaître la notion de *frame*, l'un des concepts du bowling. Nous introduisons aussi la notion de strike et de spare ainsi que les bonus respectif.

## Clarifions un peu plus
> Dev : tu veux encore clarifier ?
> Moi : oui, mais cette fois, c'est pour enlever de la duplication
> Dev : de la duplication ??
> Moi : oui, ces nombres : 3, 4, 6, etc... on les retrouve aussi dans le test 
> Dev : ah oui, c'est vrai... et je suppose que ce sont les mêmes concepts ?
> Moi : oui, chaque nombre représente le nombre de quilles tombées lors d'un lancer.

Allons chercher ces lancers. Ils sont définis dans le test et sont introduits dans notre classe via la méthode `roll`.

Pour les avoir à disposition dans notre méthode `score`, nous devons les stocker dans un attribut : 

```diff
public class Bowling {  
+   List<Integer> rolls = new ArrayList<>();  
  
+   public void roll(int pins) {  
+       rolls.add(pins);  
+   }  
  
    public int score() {  
        int score = 0;  
  
        // frame 1  
        score += 3;  
        score += 4;  
  
       // ...
  
        // frame 10
        score += 10;  
        score += 8;  
        score += 1;  
  
        return score;  
    }  
}
```

Nous allons maintenant pouvoir les utiliser : 

```diff
public int score() {  
    int score = 0;  
  
    // frame 1  
-   score += 3; // roll 1  
-   score += 4; // roll 2      
+   score += rolls.get(0); // roll 1  
+   score += rolls.get(1); // roll 2  
  
    // frame 2
-   score += 6; // roll 3  
-   score += 3; // roll 4    
+   score += rolls.get(2); // roll 3  
+   score += rolls.get(3); // roll 4  
  
    // frame 3
-   score += 2; // roll 5  
-   score += 8; // roll 6 (spare)  
-   score += 4; // roll 7 (spare bonus)      
+   score += rolls.get(4); // roll 5  
+   score += rolls.get(5); // roll 6 (spare)  
+   score += rolls.get(6); // roll 7 (spare bonus)  
  
    // frame 4
-   score += 4; // roll 7  
-   score += 3; // roll 8     
+   score += rolls.get(6); // roll 7  
+   score += rolls.get(7); // roll 8  
  
    // frame 5
-   score += 4; // roll 9  
-   score += 4; // roll 10    
+   score += rolls.get(8); // roll 9  
+   score += rolls.get(9); // roll 10  
  
    // frame 6
-   score += 5; // roll 11  
-   score += 2; // roll 12          
+     score += rolls.get(10); // roll 11  
+   score += rolls.get(11); // roll 12  
  
    // frame 7
-   score += 6; // roll 13  
-   score += 4; // roll 14 (spare) 
-   score += 10; // roll 15 (spare bonus)       
+     score += rolls.get(12); // roll 13  
+   score += rolls.get(13); // roll 14 (spare)  
+   score += rolls.get(14); // roll 15 (spare bonus)    
  
    // frame 8
-   score += 10; // roll 15 (strike)  
-   score += 8; // roll 16 (strike bonus)  
-   score += 0; // roll 17 (strike bonus)      
+   score += rolls.get(14); // roll 15 (strike)  
+   score += rolls.get(15); // roll 16 (strike bonus)  
+   score += rolls.get(16); // roll 17 (strike bonus)  
  
  
    // frame 9
-   score += 8; // roll 16  
-   score += 0; // roll 17      
+   score += rolls.get(15); // roll 16  
+   score += rolls.get(16); // roll 17  
  
  
    // frame 10
-   score += 10; // roll 18 (strike)  
-   score += 8; // roll 19 (strike bonus)  
-   score += 1; // roll 20 (strike bonus)-   
+   score += rolls.get(17); // roll 18 (strike)  
+   score += rolls.get(18); // roll 19 (strike bonus)  
+   score += rolls.get(19); // roll 20 (strike bonus)  
  
    return score;  
}
```

>Dev : Et maintenant ? On fait quoi ?
>Moi : On continue d'enlever la duplication ! 
>Dev : À part le rolls.get, je n'en vois pas...
>Moi : C'est pourquoi nous allons la mettre en évidence

Pour mettre en évidence la duplication, il faut se servir de son instinct. Il n'y a pas de règle permettant d'y arriver, mais je te propose ça : 

```diff
public int score() {  
    int score = 0;  
  
    // frame 1  
    score += rolls.get(0); // roll 1  
-   score += rolls.get(1); // roll 2  
+   score += rolls.get(0 + 1); // roll 2  
  
    // frame 2
    score += rolls.get(2); // roll 3  
-   score += rolls.get(3); // roll 4  
+   score += rolls.get(2 + 1); // roll 4  
  
    // frame 3
    score += rolls.get(4); // roll 5  
-   score += rolls.get(5); // roll 6 (spare)  
-   score += rolls.get(6); // roll 7 (spare bonus)  
+   score += rolls.get(4 + 1); // roll 6 (spare)  
+   score += rolls.get(4 + 2); // roll 7 (spare bonus)  
  
    // frame 4    
    score += rolls.get(6); // roll 7  
-   score += rolls.get(7); // roll 8  
+   score += rolls.get(6 + 1); // roll 8  
  
    // frame 5    
    score += rolls.get(8); // roll 9  
-   score += rolls.get(9); // roll 10  
+   score += rolls.get(8 + 1); // roll 10  
  
    // frame 6    
    score += rolls.get(10); // roll 11  
-   score += rolls.get(11); // roll 12  
+   score += rolls.get(10 + 1); // roll 12  
  
    // frame 7    
    score += rolls.get(12); // roll 13  
-   score += rolls.get(13); // roll 14  
-   score += rolls.get(14); // roll 15  
+   score += rolls.get(12 + 1); // roll 14  
+   score += rolls.get(12 + 2); // roll 15  
  
    // frame 8    
    score += rolls.get(14); // roll 15 (strike)  
-   score += rolls.get(15); // roll 16 (strike bonus)  
-   score += rolls.get(16); // roll 17 (strike bonus)  
+   score += rolls.get(14 + 1); // roll 16 (strike bonus)  
+   score += rolls.get(14 + 2); // roll 17 (strike bonus)  
  
  
    // frame 9    
    score += rolls.get(15); // roll 16
-   score += rolls.get(16); // roll 17        
+   score += rolls.get(15 + 1); // roll 17  
  
  
    // frame 10    
    score += rolls.get(17); // roll 18 (strike) 
-   score += rolls.get(18); // roll 19 (strike bonus)  
-   score += rolls.get(19); // roll 20 (strike bonus)  
+   score += rolls.get(17 + 1); // roll 19 (strike bonus)  
+   score += rolls.get(17 + 2); // roll 20 (strike bonus)  
  
    return score;  
}
```

J'exprime l'indice de chaque lancer en fonction du premier lancer du frame. Ce qui va nous permettre d'introduire la notion de *frame index*. Chaque *frame* commence à son premier lancer. Le *frame index* correspond à l'index de ce lancer. Voyons voir ce que ça donne : 

```diff
public int score() {  
    int score = 0;  
+   int frameIndex = 0;  
  
    // frame 1  
-   score += rolls.get(0); // roll 1
-   score += rolls.get(0 + 1); // roll 2
+   frameIndex = 0;  
+   score += rolls.get(frameIndex); // roll 1  
+   score += rolls.get(frameIndex + 1); // roll 2  
  
    // frame 2   
-   score += rolls.get(2); // roll 3
-   score += rolls.get(2 + 1); // roll 4
+   frameIndex = 2;  
+   score += rolls.get(frameIndex); // roll 3  
+   score += rolls.get(frameIndex + 1); // roll 4  
  
    // frame 3
-   score += rolls.get(4); // roll 5
-   score += rolls.get(4 + 1); // roll 6 (spare)
-   score += rolls.get(4 + 2); // roll 7 (spare bonus)    
+   frameIndex = 4;  
+   score += rolls.get(frameIndex); // roll 5  
+   score += rolls.get(frameIndex + 1); // roll 6 (spare)  
+   score += rolls.get(frameIndex + 2); // roll 7 (spare bonus)  
  
    // frame 4    
-   score += rolls.get(6); // roll 7
-   score += rolls.get(6 + 1); // roll 8    
+   frameIndex = 6;  
+   score += rolls.get(frameIndex); // roll 7  
+   score += rolls.get(frameIndex + 1); // roll 8  
  
    // frame 5    
-   score += rolls.get(8); // roll 9
-   score += rolls.get(8 + 1); // roll 10    
+   frameIndex = 8;  
+   score += rolls.get(frameIndex); // roll 9  
+   score += rolls.get(frameIndex + 1); // roll 10  
  
    // frame 6    
-   score += rolls.get(10); // roll 11
-   score += rolls.get(10 + 1); // roll 12
+   frameIndex = 10;  
+   score += rolls.get(frameIndex); // roll 11  
+   score += rolls.get(frameIndex + 1); // roll 12  
  
    // frame 7    
-   score += rolls.get(12); // roll 13
-   score += rolls.get(12 + 1); // roll 14
-   score += rolls.get(12 + 2); // roll 15    
+   frameIndex = 12;  
+   score += rolls.get(frameIndex); // roll 13  
+   score += rolls.get(frameIndex + 1); // roll 14  
+   score += rolls.get(frameIndex + 2); // roll 15  
  
    // frame 8   
-   score += rolls.get(14); // roll 15 (strike)
-   score += rolls.get(14 + 1); // roll 16 (strike bonus)
-   score += rolls.get(14 + 2); // roll 17 (strike bonus)
+   frameIndex = 14;  
+   score += rolls.get(frameIndex); // roll 15 (strike)  
+   score += rolls.get(frameIndex + 1); // roll 16 (strike bonus)  
+   score += rolls.get(frameIndex + 2); // roll 17 (strike bonus)  
  
  
    // frame 9    
-   score += rolls.get(15); // roll 16
-   score += rolls.get(15 + 1); // roll 17    
+   frameIndex = 15;  
+   score += rolls.get(frameIndex); // roll 16  
+   score += rolls.get(frameIndex + 1); // roll 17  
  
  
    // frame 10    
-   score += rolls.get(17); // roll 18 (strike)
-   score += rolls.get(17 + 1); // roll 19 (strike bonus)
-   score += rolls.get(17 + 2); // roll 20 (strike bonus)
+   frameIndex = 17;  
+   score += rolls.get(frameIndex); // roll 18 (strike)  
+   score += rolls.get(frameIndex + 1); // roll 19 (strike bonus)  
+   score += rolls.get(frameIndex + 2); // roll 20 (strike bonus)  
  
    return score;  
}
```

>Dev : Ah oui ! Je la vois maintenant. On fait une boucle ?
>Moi : Ton instinct te dit qu'il y a une boucle, mais c'est encore trop tôt.
>Dev : Pourquoi ? Les blocs se ressemblent !
>Moi : Ils se ressemblent, mais ils ne sont pas identiques. Il faut continuer à clarifier.
>Dev : Qu'est-ce qu'on fait alors ?
>Moi : Ce que l'on fait depuis le début, on clarifie puis on met en évidence la duplication.

Effectivement, il est trop tôt pour introduire une boucle. Clarifions le concept de `frameIndex` : 

```diff
public int score() {  
    int score = 0;  
    int frameIndex = 0;  
  
    // frame 1  
    frameIndex = 0;  
    score += rolls.get(frameIndex); // roll 1  
    score += rolls.get(frameIndex + 1); // roll 2  
  
    // frame 2   
-   frameIndex = 2;  
+   frameIndex = 0 + 2;  
    score += rolls.get(frameIndex); // roll 3  
    score += rolls.get(frameIndex + 1); // roll 4  
  
    // frame 3
-   frameIndex = 4;  
+   frameIndex = 2 + 2;  
    score += rolls.get(frameIndex); // roll 5  
    score += rolls.get(frameIndex + 1); // roll 6 (spare)  
    score += rolls.get(frameIndex + 2); // roll 7 (spare bonus)  
  
    // frame 4    
-   frameIndex = 6;  
+   frameIndex = 4 + 2;  
    score += rolls.get(frameIndex); // roll 7  
    score += rolls.get(frameIndex + 1); // roll 8  
  
    // frame 5    
-   frameIndex = 8;  
+   frameIndex = 6 + 2;    
    score += rolls.get(frameIndex); // roll 9  
    score += rolls.get(frameIndex + 1); // roll 10  
  
    // frame 6    
-   frameIndex = 10;  
+   frameIndex = 8 + 2;  
    score += rolls.get(frameIndex); // roll 11  
    score += rolls.get(frameIndex + 1); // roll 12  
  
    // frame 7    
-   frameIndex = 12;  
+   frameIndex = 10 + 2;  
    score += rolls.get(frameIndex); // roll 13  
    score += rolls.get(frameIndex + 1); // roll 14  
    score += rolls.get(frameIndex + 2); // roll 15  
  
    // frame 8   
-   frameIndex = 14;  
+   frameIndex = 12 + 2;  
    score += rolls.get(frameIndex); // roll 15 (strike)  
    score += rolls.get(frameIndex + 1); // roll 16 (strike bonus)  
    score += rolls.get(frameIndex + 2); // roll 17 (strike bonus)  
  
  
    // frame 9    
-   frameIndex = 15;
+   frameIndex = 14 + 1;  
    score += rolls.get(frameIndex); // roll 16  
    score += rolls.get(frameIndex + 1); // roll 17  
  
  
    // frame 10    
-   frameIndex = 17;  
+   frameIndex = 15 + 2;  
    score += rolls.get(frameIndex); // roll 18 (strike)  
    score += rolls.get(frameIndex + 1); // roll 19 (strike bonus)  
    score += rolls.get(frameIndex + 2); // roll 20 (strike bonus)  
  
    return score;  
}
```

Puis mettons en évidence la duplication : 

```diff
public int score() {  
    int score = 0;  
    int frameIndex = 0;  
  
    // frame 1  
    frameIndex = 0;  
    score += rolls.get(frameIndex); // roll 1  
    score += rolls.get(frameIndex + 1); // roll 2  
  
    // frame 2   
-   frameIndex = 0 + 2;  
+   frameIndex = frameIndex + 2;  
    score += rolls.get(frameIndex); // roll 3  
    score += rolls.get(frameIndex + 1); // roll 4  
  
    // frame 3
-   frameIndex = 2 + 2;  
+   frameIndex = frameIndex + 2;  
    score += rolls.get(frameIndex); // roll 5  
    score += rolls.get(frameIndex + 1); // roll 6 (spare)  
    score += rolls.get(frameIndex + 2); // roll 7 (spare bonus)  
  
    // frame 4    
-   frameIndex = 4 + 2;  
+   frameIndex = frameIndex + 2;  
    score += rolls.get(frameIndex); // roll 7  
    score += rolls.get(frameIndex + 1); // roll 8  
  
    // frame 5    
-   frameIndex = 6 + 2;    
+   frameIndex = frameIndex + 2;    
    score += rolls.get(frameIndex); // roll 9  
    score += rolls.get(frameIndex + 1); // roll 10  
  
    // frame 6    
-   frameIndex = 8 + 2;  
+   frameIndex = frameIndex + 2;  
    score += rolls.get(frameIndex); // roll 11  
    score += rolls.get(frameIndex + 1); // roll 12  
  
    // frame 7    
-   frameIndex = 10 + 2;  
+   frameIndex = frameIndex + 2;  
    score += rolls.get(frameIndex); // roll 13  
    score += rolls.get(frameIndex + 1); // roll 14  
    score += rolls.get(frameIndex + 2); // roll 15  
  
    // frame 8   
-   frameIndex = 12 + 2;  
+   frameIndex = frameIndex + 2;  
    score += rolls.get(frameIndex); // roll 15 (strike)  
    score += rolls.get(frameIndex + 1); // roll 16 (strike bonus)  
    score += rolls.get(frameIndex + 2); // roll 17 (strike bonus)  
  
  
    // frame 9    
-   frameIndex = 14 + 1;  
+   frameIndex = frameIndex + 1;  
    score += rolls.get(frameIndex); // roll 16  
    score += rolls.get(frameIndex + 1); // roll 17  
  
  
    // frame 10    
-   frameIndex = 15 + 2;  
+   frameIndex = frameIndex + 2;  
    score += rolls.get(frameIndex); // roll 18 (strike)  
    score += rolls.get(frameIndex + 1); // roll 19 (strike bonus)  
    score += rolls.get(frameIndex + 2); // roll 20 (strike bonus)  
  
    return score;  
}
```

> Dev : Et ensuite ?
> Moi : On va continuer de clarifier en introduisant la notion de bonus. On va de nouveau utiliser le refactoring Extract variable.

La notion de bonus apparaît lorsqu'on a un *strike* (les deux prochains lancers sont ajoutés en bonus) ou lors d'un *spare* (le prochain lancer est ajouté en bonus). On va donc faire un extract variable sur ce calcul pour faire apparaître le concept.

```diff
public int score() {
    int score = 0;
    int frameIndex = 0;
+   int bonus = 0;
    
    // frame 1
    frameIndex = 0;
    score += rolls.get(frameIndex); // roll 1
    score += rolls.get(frameIndex + 1); // roll 2

    // frame 2
    frameIndex = frameIndex + 2;
    score += rolls.get(frameIndex); // roll 3
    score += rolls.get(frameIndex + 1); // roll 4

    // frame 3
    frameIndex = frameIndex + 2;
    score += rolls.get(frameIndex); // roll 5
    score += rolls.get(frameIndex + 1); // roll 6 (spare)
    bonus = rolls.get(frameIndex + 2); // roll 7 (spare bonus)
    score += bonus;

    // frame 4
    frameIndex = frameIndex + 2;
    score += rolls.get(frameIndex); // roll 7
    score += rolls.get(frameIndex + 1); // roll 8

    // frame 5
    frameIndex = frameIndex + 2;
    score += rolls.get(frameIndex); // roll 9
    score += rolls.get(frameIndex + 1); // roll 10

    // frame 6
    frameIndex = frameIndex + 2;
    score += rolls.get(frameIndex); // roll 11
    score += rolls.get(frameIndex + 1); // roll 12

    // frame 7
    frameIndex = frameIndex + 2;
    score += rolls.get(frameIndex); // roll 13
    score += rolls.get(frameIndex + 1); // roll 14 (spare)
-   score += rolls.get(frameIndex + 2); // roll 15 (spare bonus)
+   bonus = rolls.get(frameIndex + 2); // roll 15 (spare bonus)
+   score += bonus;

    // frame 8
    frameIndex = frameIndex + 2;
    score += rolls.get(frameIndex); // roll 15 (strike)
-   score += rolls.get(frameIndex + 1); // roll 16 (strike bonus)
-   score += rolls.get(frameIndex + 2); // roll 17 (strike bonus)
+   bonus = rolls.get(frameIndex + 1); // roll 16 (strike bonus)
+   bonus += rolls.get(frameIndex + 2); // roll 17 (strike bonus)
+   score += bonus;

    // frame 9
    frameIndex = frameIndex + 1;
    score += rolls.get(frameIndex); // roll 16
     score += rolls.get(frameIndex + 1); // roll 17


    // frame 10
    frameIndex = frameIndex + 2;
    score += rolls.get(frameIndex); // roll 18 (strike)
-   score += rolls.get(frameIndex + 1); // roll 19 (strike bonus)
-   score += rolls.get(frameIndex + 2); // roll 20 (strike bonus)
+   bonus = rolls.get(frameIndex + 1); // roll 19 (strike bonus)
+   bonus += rolls.get(frameIndex + 2); // roll 20 (strike bonus)
+   score += bonus;

   return score;
}
```

> Dev : C'est bien beau ce concept de bonus, mais on ne les voit pas dans tous les blocs !
> Moi : Et donc ?
> Dev : Les blocs se ressemblent moins après ta modification !
> Moi : Tu as tout à fait raison, mais rien ne nous interdit d'introduire la notion de bonus pour les frames standard

En effet, nous avons un concept qui n'apparait que dans certains cas. Ce concept est assez simple et peut-être représenté dans notre algorithme. 

```diff
    public int score() {
    int score = 0;
    int frameIndex = 0;
    int bonus = 0;

    // frame 1
    frameIndex = 0;
    score += rolls.get(frameIndex); // roll 1
    score += rolls.get(frameIndex + 1); // roll 2
+   bonus = 0;
+   score += bonus;

    // frame 2
    frameIndex = frameIndex + 2;
    score += rolls.get(frameIndex); // roll 3
    score += rolls.get(frameIndex + 1); // roll 4
+   bonus = 0;
+   score += bonus;

    // frame 3
    frameIndex = frameIndex + 2;
    score += rolls.get(frameIndex); // roll 5
    score += rolls.get(frameIndex + 1); // roll 6 (spare)
    bonus = rolls.get(frameIndex + 2); // roll 7 (spare bonus)
    score += bonus;

    // frame 4
    frameIndex = frameIndex + 2;
    score += rolls.get(frameIndex); // roll 7
    score += rolls.get(frameIndex + 1); // roll 8
+   bonus = 0;
+   score += bonus;

    // frame 5
    frameIndex = frameIndex + 2;
    score += rolls.get(frameIndex); // roll 9
    score += rolls.get(frameIndex + 1); // roll 10
+   bonus = 0;
+   score += bonus;

    // frame 6
    frameIndex = frameIndex + 2;
    score += rolls.get(frameIndex); // roll 11
    score += rolls.get(frameIndex + 1); // roll 12
+   bonus = 0;
+   score += bonus;

    // frame 7
    frameIndex = frameIndex + 2;
    score += rolls.get(frameIndex); // roll 13
    score += rolls.get(frameIndex + 1); // roll 14 (spare)
    bonus = rolls.get(frameIndex + 2); // roll 15 (spare bonus)
    score += bonus;

    // frame 8
    frameIndex = frameIndex + 2;
    score += rolls.get(frameIndex); // roll 15 (strike)
    bonus = rolls.get(frameIndex + 1); // roll 16 (strike bonus)
    bonus += rolls.get(frameIndex + 2); // roll 17 (strike bonus)
    score += bonus;

    // frame 9
    frameIndex = frameIndex + 1;
    score += rolls.get(frameIndex); // roll 16
    score += rolls.get(frameIndex + 1); // roll 17
+   bonus = 0;
+   score += bonus;


    // frame 10
    frameIndex = frameIndex + 2;
    score += rolls.get(frameIndex); // roll 18 (strike)
    bonus = rolls.get(frameIndex + 1); // roll 19 (strike bonus)
    bonus += rolls.get(frameIndex + 2); // roll 20 (strike bonus)
    score += bonus;

    return score;
}
```

> Dev : Génial ! Dans chaque bloc, on a une ligne qui se ressemble. Mais le bonus se calcule toujours différement...
> Moi : C'est vrai, après la clarification, mettons en évidence la duplication
> Dev : Pas facile...
> Moi : Dans quel cas le bonus est équivalent au prochain lancer ?
> Dev : Lorsqu'on fait un spare
> Moi : Et dans quel cas le bonus vaut les deux prochains lancers ?
> Dev : Lors d'un strike !
> Moi : Et pour un bonus de zéro ?
> Dev : Dans les autres cas !

Pour détecter un *strike*, il faut vérifier la somme du premier lancer. S'il vaut `10`, c'en est un. Pour un *spare*, c'est la somme des deux lancers qui doit valoir `10`. Dans les autres cas, on n'a pas de bonus (ou un bonus qui vaut `0`).

Mettons en évidence la duplication : 

```diff
public int score() {
    int score = 0;
    int frameIndex = 0;
    int bonus = 0;

    // frame 1
    frameIndex = 0;
    score += rolls.get(frameIndex); // roll 1
    score += rolls.get(frameIndex + 1); // roll 2
-   bonus = 0;
+   if (rolls.get(frameIndex) == 10) {
+       bonus = rolls.get(frameIndex + 1);
+       bonus += rolls.get(frameIndex + 2);
+   } else if (rolls.get(frameIndex) + rolls.get(frameIndex + 1) == 10)
+       bonus = rolls.get(frameIndex + 2);
+   else
+       bonus = 0;
    score += bonus;

    // frame 2
    frameIndex = frameIndex + 2;
    score += rolls.get(frameIndex); // roll 3
    score += rolls.get(frameIndex + 1); // roll 4
-   bonus = 0;
+   if (rolls.get(frameIndex) == 10) {
+       bonus = rolls.get(frameIndex + 1);
+       bonus += rolls.get(frameIndex + 2);
+   } else if (rolls.get(frameIndex) + rolls.get(frameIndex + 1) == 10)
+       bonus = rolls.get(frameIndex + 2);
+   else
+       bonus = 0;
    score += bonus;

    // frame 3
    frameIndex = frameIndex + 2;
    score += rolls.get(frameIndex); // roll 5
    score += rolls.get(frameIndex + 1); // roll 6 (spare)
-   bonus = rolls.get(frameIndex + 2); // roll 7 (spare bonus)
+   if (rolls.get(frameIndex) == 10) {
+       bonus = rolls.get(frameIndex + 1);
+       bonus += rolls.get(frameIndex + 2);
+   } else if (rolls.get(frameIndex) + rolls.get(frameIndex + 1) == 10)
+       bonus = rolls.get(frameIndex + 2);
+   else
+       bonus = 0;
    score += bonus;

    // frame 4
    frameIndex = frameIndex + 2;
    score += rolls.get(frameIndex); // roll 7
    score += rolls.get(frameIndex + 1); // roll 8
-   bonus = 0;
+   if (rolls.get(frameIndex) == 10) {
+       bonus = rolls.get(frameIndex + 1);
+       bonus += rolls.get(frameIndex + 2);
+   } else if (rolls.get(frameIndex) + rolls.get(frameIndex + 1) == 10)
+       bonus = rolls.get(frameIndex + 2);
+   else
+       bonus = 0;
    score += bonus;

    // frame 5
    frameIndex = frameIndex + 2;
    score += rolls.get(frameIndex); // roll 9
    score += rolls.get(frameIndex + 1); // roll 10
-   bonus = 0;
+   if (rolls.get(frameIndex) == 10) {
+       bonus = rolls.get(frameIndex + 1);
+       bonus += rolls.get(frameIndex + 2);
+   } else if (rolls.get(frameIndex) + rolls.get(frameIndex + 1) == 10)
+       bonus = rolls.get(frameIndex + 2);
+   else
+       bonus = 0;
    score += bonus;

    // frame 6
    frameIndex = frameIndex + 2;
    score += rolls.get(frameIndex); // roll 11
    score += rolls.get(frameIndex + 1); // roll 12
-   bonus = 0;
+   if (rolls.get(frameIndex) == 10) {
+       bonus = rolls.get(frameIndex + 1);
+       bonus += rolls.get(frameIndex + 2);
+   } else if (rolls.get(frameIndex) + rolls.get(frameIndex + 1) == 10)
+       bonus = rolls.get(frameIndex + 2);
+   else
+       bonus = 0;
    score += bonus;

    // frame 7
    frameIndex = frameIndex + 2;
    score += rolls.get(frameIndex); // roll 13
    score += rolls.get(frameIndex + 1); // roll 14 (spare)
-   bonus = rolls.get(frameIndex + 2); // roll 15 (spare bonus)
+   if (rolls.get(frameIndex) == 10) {
+       bonus = rolls.get(frameIndex + 1);
+       bonus += rolls.get(frameIndex + 2);
+   } else if (rolls.get(frameIndex) + rolls.get(frameIndex + 1) == 10)
+       bonus = rolls.get(frameIndex + 2);
+   else
+       bonus = 0;
    score += bonus;

    // frame 8
    frameIndex = frameIndex + 2;
    score += rolls.get(frameIndex); // roll 15 (strike)
-   bonus = rolls.get(frameIndex + 1); // roll 16 (strike bonus)
-   bonus += rolls.get(frameIndex + 2); // roll 17 (strike bonus)
+   if (rolls.get(frameIndex) == 10) {
+       bonus = rolls.get(frameIndex + 1);
+       bonus += rolls.get(frameIndex + 2);
+   } else if (rolls.get(frameIndex) + rolls.get(frameIndex + 1) == 10)
+       bonus = rolls.get(frameIndex + 2);
+   else
+       bonus = 0;
    score += bonus;

    // frame 9
    frameIndex = frameIndex + 1;
    score += rolls.get(frameIndex); // roll 16
    score += rolls.get(frameIndex + 1); // roll 17
-   bonus = 0;
+   if (rolls.get(frameIndex) == 10) {
+       bonus = rolls.get(frameIndex + 1);
+       bonus += rolls.get(frameIndex + 2);
+   } else if (rolls.get(frameIndex) + rolls.get(frameIndex + 1) == 10)
+       bonus = rolls.get(frameIndex + 2);
+   else
+       bonus = 0;
    score += bonus;


    // frame 10
    frameIndex = frameIndex + 2;
    score += rolls.get(frameIndex); // roll 18 (strike)
-   bonus = rolls.get(frameIndex + 1); // roll 19 (strike bonus)
-   bonus += rolls.get(frameIndex + 2); // roll 20 (strike bonus)
+   if (rolls.get(frameIndex) == 10) {
+       bonus = rolls.get(frameIndex + 1);
+       bonus += rolls.get(frameIndex + 2);
+   } else if (rolls.get(frameIndex) + rolls.get(frameIndex + 1) == 10)
+       bonus = rolls.get(frameIndex + 2);
+   else
+       bonus = 0;
    score += bonus;

    return score;
}
```

> Dev : Le code est beaucoup plus lourd comme ça...
> Moi : C'est vrai. On peut le simplfiier en ajoutant une abstraction sur le calcul du bonus.
> Dev : Comment peut-on faire ça ?
> Moi : C'est simple, avec le refactoring extract method.

Nous sommes assez avancés pour extraire le concept de calcul de bonus. Pour cela, nous allons faire un [[wiki/extract method|extract method]]. Cela consiste à créer une fonction à partir d'un bloc de code.

```diff
public int score() {
    int score = 0;
    int frameIndex = 0;
    int bonus = 0;

    // frame 1
    frameIndex = 0;
    score += rolls.get(frameIndex); // roll 1
    score += rolls.get(frameIndex + 1); // roll 2
+   bonus = calculateFrameBonus(frameIndex);
-   if (rolls.get(frameIndex) == 10) {
-       bonus = rolls.get(frameIndex + 1);
-       bonus += rolls.get(frameIndex + 2);
-   } else if (rolls.get(frameIndex) + rolls.get(frameIndex + 1) == 10)
-       bonus = rolls.get(frameIndex + 2);
-   else
-       bonus = 0;
    score += bonus;

    // frame 2
    frameIndex = frameIndex + 2;
    score += rolls.get(frameIndex); // roll 3
    score += rolls.get(frameIndex + 1); // roll 4
+   bonus = calculateFrameBonus(frameIndex);
-   if (rolls.get(frameIndex) == 10) {
-       bonus = rolls.get(frameIndex + 1);
-       bonus += rolls.get(frameIndex + 2);
-   } else if (rolls.get(frameIndex) + rolls.get(frameIndex + 1) == 10)
-       bonus = rolls.get(frameIndex + 2);
-   else
-       bonus = 0;
    score += bonus;

    // frame 3
    frameIndex = frameIndex + 2;
    score += rolls.get(frameIndex); // roll 5
    score += rolls.get(frameIndex + 1); // roll 6 (spare)
+   bonus = calculateFrameBonus(frameIndex);
-   if (rolls.get(frameIndex) == 10) {
-       bonus = rolls.get(frameIndex + 1);
-       bonus += rolls.get(frameIndex + 2);
-   } else if (rolls.get(frameIndex) + rolls.get(frameIndex + 1) == 10)
-       bonus = rolls.get(frameIndex + 2);
-   else
-       bonus = 0;
    score += bonus;

    // frame 4
    frameIndex = frameIndex + 2;
    score += rolls.get(frameIndex); // roll 7
    score += rolls.get(frameIndex + 1); // roll 8
+   bonus = calculateFrameBonus(frameIndex);
-   if (rolls.get(frameIndex) == 10) {
-       bonus = rolls.get(frameIndex + 1);
-       bonus += rolls.get(frameIndex + 2);
-   } else if (rolls.get(frameIndex) + rolls.get(frameIndex + 1) == 10)
-       bonus = rolls.get(frameIndex + 2);
-   else
-       bonus = 0;
    score += bonus;

    // frame 5
    frameIndex = frameIndex + 2;
    score += rolls.get(frameIndex); // roll 9
    score += rolls.get(frameIndex + 1); // roll 10
+   bonus = calculateFrameBonus(frameIndex);
-   if (rolls.get(frameIndex) == 10) {
-       bonus = rolls.get(frameIndex + 1);
-       bonus += rolls.get(frameIndex + 2);
-   } else if (rolls.get(frameIndex) + rolls.get(frameIndex + 1) == 10)
-       bonus = rolls.get(frameIndex + 2);
-   else
-       bonus = 0;
    score += bonus;

    // frame 6
    frameIndex = frameIndex + 2;
    score += rolls.get(frameIndex); // roll 11
    score += rolls.get(frameIndex + 1); // roll 12
+   bonus = calculateFrameBonus(frameIndex);
-   if (rolls.get(frameIndex) == 10) {
-       bonus = rolls.get(frameIndex + 1);
-       bonus += rolls.get(frameIndex + 2);
-   } else if (rolls.get(frameIndex) + rolls.get(frameIndex + 1) == 10)
-       bonus = rolls.get(frameIndex + 2);
-   else
-       bonus = 0;
    score += bonus;

    // frame 7
    frameIndex = frameIndex + 2;
    score += rolls.get(frameIndex); // roll 13
    score += rolls.get(frameIndex + 1); // roll 14 (spare)
+   bonus = calculateFrameBonus(frameIndex);
-   if (rolls.get(frameIndex) == 10) {
-       bonus = rolls.get(frameIndex + 1);
-       bonus += rolls.get(frameIndex + 2);
-   } else if (rolls.get(frameIndex) + rolls.get(frameIndex + 1) == 10)
-       bonus = rolls.get(frameIndex + 2);
-   else
-       bonus = 0;
    score += bonus;

    // frame 8
    frameIndex = frameIndex + 2;
    score += rolls.get(frameIndex); // roll 15 (strike)
+   bonus = calculateFrameBonus(frameIndex);
-   if (rolls.get(frameIndex) == 10) {
-       bonus = rolls.get(frameIndex + 1);
-       bonus += rolls.get(frameIndex + 2);
-   } else if (rolls.get(frameIndex) + rolls.get(frameIndex + 1) == 10)
-       bonus = rolls.get(frameIndex + 2);
-   else
-       bonus = 0;
    score += bonus;

    // frame 9
    frameIndex = frameIndex + 1;
    score += rolls.get(frameIndex); // roll 16
    score += rolls.get(frameIndex + 1); // roll 17
+   bonus = calculateFrameBonus(frameIndex);
-   if (rolls.get(frameIndex) == 10) {
-       bonus = rolls.get(frameIndex + 1);
-       bonus += rolls.get(frameIndex + 2);
-   } else if (rolls.get(frameIndex) + rolls.get(frameIndex + 1) == 10)
-       bonus = rolls.get(frameIndex + 2);
-   else
-       bonus = 0;
    score += bonus;


    // frame 10
    frameIndex = frameIndex + 2;
    score += rolls.get(frameIndex); // roll 18 (strike)
+   bonus = calculateFrameBonus(frameIndex);
-   if (rolls.get(frameIndex) == 10) {
-       bonus = rolls.get(frameIndex + 1);
-       bonus += rolls.get(frameIndex + 2);
-   } else if (rolls.get(frameIndex) + rolls.get(frameIndex + 1) == 10)
-       bonus = rolls.get(frameIndex + 2);
-   else
-       bonus = 0;
    score += bonus;

    return score;
}

+public int calculateFrameBonus(int frameIndex) {
+   int bonus = 0;
+   if (rolls.get(frameIndex) == 10) {
+       bonus = rolls.get(frameIndex + 1);
+       bonus += rolls.get(frameIndex + 2);
+   } else if (rolls.get(frameIndex) + rolls.get(frameIndex + 1) == 10)
+       bonus = rolls.get(frameIndex + 2);
+   else
+       bonus = 0;
+   return bonus;
+}
```

> Dev : 70 lignes de moins dans la fonction `score` !
> Moi : Et ce n'est que le début.
> Dev : Je pense qu'on va faire la même chose pour le calcul du score sans le bonus.
> Moi : Bingo, on va encore perdre des lignes, mais que doit-on faire d'abord ?
> Dev : Mettre en évidence le concept puis la duplication.

Le calcul du score (sans le bonus) peut se faire de plusieurs manières différentes. J'ai choisi l'approche de faire le moins de modification possible dans le code actuel (pour les *strikes* et les *spares*, il aurait été possible de calculer directement 10).

Ici, nous allons calculer la somme d'un lancer dans le cas d'un strike et la somme de deux lancers dans les autres cas.

Mettons en évidence le concept de score lié à la frame : 

```diff

public int score() {
    int score = 0;
    int frameIndex = 0;
    int bonus = 0;
+   int frameScore = 0;

    // frame 1
    frameIndex = 0;

+   frameScore = rolls.get(frameIndex); // roll 1
+   frameScore += rolls.get(frameIndex + 1); // roll 2
-   score += rolls.get(frameIndex); // roll 1
-   score += rolls.get(frameIndex + 1); // roll 2
    bonus = calculateFrameBonus(frameIndex);
+   score += frameScore + bonus;
-   score += bonus;

    // frame 2
    frameIndex = frameIndex + 2;
+   frameScore = rolls.get(frameIndex); // roll 3
+   frameScore += rolls.get(frameIndex + 1); // roll 4
-   score += rolls.get(frameIndex); // roll 3
-   score += rolls.get(frameIndex + 1); // roll 4
    bonus = calculateFrameBonus(frameIndex);
+   score += frameScore + bonus;
-   score += bonus;

    // frame 3
    frameIndex = frameIndex + 2;
+   frameScore = rolls.get(frameIndex); // roll 5
+   frameScore += rolls.get(frameIndex + 1); // roll 6 (spare)
-   score += rolls.get(frameIndex); // roll 5
-   score += rolls.get(frameIndex + 1); // roll 6 (spare)
    bonus = calculateFrameBonus(frameIndex);
+   score += frameScore + bonus;
-   score += bonus;

    // frame 4
    frameIndex = frameIndex + 2;
+   frameScore = rolls.get(frameIndex); // roll 7
+   frameScore += rolls.get(frameIndex + 1); // roll 8
-   score += rolls.get(frameIndex); // roll 7
-   score += rolls.get(frameIndex + 1); // roll 8
    bonus = calculateFrameBonus(frameIndex);
+   score += frameScore + bonus;
-   score += bonus;

    // frame 5
    frameIndex = frameIndex + 2;
+   frameScore = rolls.get(frameIndex); // roll 9
+   frameScore += rolls.get(frameIndex + 1); // roll 10
-   score += rolls.get(frameIndex); // roll 9
-   score += rolls.get(frameIndex + 1); // roll 10
    bonus = calculateFrameBonus(frameIndex);
+   score += frameScore + bonus;
-   score += bonus;

    // frame 6
    frameIndex = frameIndex + 2;
+   frameScore = rolls.get(frameIndex); // roll 11
+   frameScore += rolls.get(frameIndex + 1); // roll 12
-   score += rolls.get(frameIndex); // roll 11
-   score += rolls.get(frameIndex + 1); // roll 12
    bonus = calculateFrameBonus(frameIndex);
+   score += frameScore + bonus;
-   score += bonus;

    // frame 7
    frameIndex = frameIndex + 2;
+   frameScore = rolls.get(frameIndex); // roll 13
+   frameScore += rolls.get(frameIndex + 1); // roll 14 (spare)
-   score += rolls.get(frameIndex); // roll 13
-   score += rolls.get(frameIndex + 1); // roll 14 (spare)
    bonus = calculateFrameBonus(frameIndex);
+   score += frameScore + bonus;
-   score += bonus;

    // frame 8
    frameIndex = frameIndex + 2;
+       frameScore = rolls.get(frameIndex); // roll 15 (strike)
-   score += rolls.get(frameIndex); // roll 15 (strike)
    bonus = calculateFrameBonus(frameIndex);
+   score += frameScore + bonus;
-   score += bonus;

    // frame 9
    frameIndex = frameIndex + 1;
+   frameScore = rolls.get(frameIndex); // roll 16
+   frameScore += rolls.get(frameIndex + 1); // roll 17
-   score += rolls.get(frameIndex); // roll 16
-   score += rolls.get(frameIndex + 1); // roll 17
    bonus = calculateFrameBonus(frameIndex);
+   score += frameScore + bonus;
-   score += bonus;


    // frame 10
    frameIndex = frameIndex + 2;
+   frameScore = rolls.get(frameIndex); // roll 18 (strike)
-   score += rolls.get(frameIndex); // roll 18 (strike)
    bonus = calculateFrameBonus(frameIndex);
+   score += frameScore + bonus;
-   score += bonus;

    return score;
}
```

Ensuite, on met en évidence la duplication : 

```diff
public int score() {
    int score = 0;
    int frameIndex = 0;
    int bonus = 0;
    int frameScore = 0;

    // frame 1
    frameIndex = 0;

+   if (rolls.get(frameIndex) == 10) {
+       frameScore = rolls.get(frameIndex);
+   } else {
+       frameScore = rolls.get(frameIndex);
+       frameScore += rolls.get(frameIndex + 1);
+   }
-   frameScore = rolls.get(frameIndex); // roll 1
-   frameScore += rolls.get(frameIndex + 1); // roll 2
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;

    // frame 2
    frameIndex = frameIndex + 2;
+   if (rolls.get(frameIndex) == 10) {
+       frameScore = rolls.get(frameIndex);
+   } else {
+       frameScore = rolls.get(frameIndex);
+       frameScore += rolls.get(frameIndex + 1);
+   }
-   frameScore = rolls.get(frameIndex); // roll 3
-   frameScore += rolls.get(frameIndex + 1); // roll 4
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;

    // frame 3
    frameIndex = frameIndex + 2;
+   if (rolls.get(frameIndex) == 10) {
+       frameScore = rolls.get(frameIndex);
+   } else {
+       frameScore = rolls.get(frameIndex);
+       frameScore += rolls.get(frameIndex + 1);
+   }
-   frameScore = rolls.get(frameIndex); // roll 5
-   frameScore += rolls.get(frameIndex + 1); // roll 6 (spare)
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;

    // frame 4
    frameIndex = frameIndex + 2;
+   if (rolls.get(frameIndex) == 10) {
+       frameScore = rolls.get(frameIndex);
+   } else {
+       frameScore = rolls.get(frameIndex);
+       frameScore += rolls.get(frameIndex + 1);
+   }
-   frameScore = rolls.get(frameIndex); // roll 7
-   frameScore += rolls.get(frameIndex + 1); // roll 8
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;

    // frame 5
    frameIndex = frameIndex + 2;
+   if (rolls.get(frameIndex) == 10) {
+       frameScore = rolls.get(frameIndex);
+   } else {
+       frameScore = rolls.get(frameIndex);
+       frameScore += rolls.get(frameIndex + 1);
+   }
-   frameScore = rolls.get(frameIndex); // roll 9
-   frameScore += rolls.get(frameIndex + 1); // roll 10
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;

    // frame 6
    frameIndex = frameIndex + 2;
+   if (rolls.get(frameIndex) == 10) {
+       frameScore = rolls.get(frameIndex);
+   } else {
+       frameScore = rolls.get(frameIndex);
+       frameScore += rolls.get(frameIndex + 1);
+   }
-   frameScore = rolls.get(frameIndex); // roll 11
-   frameScore += rolls.get(frameIndex + 1); // roll 12
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;

    // frame 7
    frameIndex = frameIndex + 2;
+   if (rolls.get(frameIndex) == 10) {
+       frameScore = rolls.get(frameIndex);
+   } else {
+       frameScore = rolls.get(frameIndex);
+       frameScore += rolls.get(frameIndex + 1);
+   }
-   frameScore = rolls.get(frameIndex); // roll 13
-   frameScore += rolls.get(frameIndex + 1); // roll 14 (spare)
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;

    // frame 8
    frameIndex = frameIndex + 2;
+   if (rolls.get(frameIndex) == 10) {
+       frameScore = rolls.get(frameIndex);
+   } else {
+       frameScore = rolls.get(frameIndex);
+       frameScore += rolls.get(frameIndex + 1);
+   }
-   frameScore = rolls.get(frameIndex); // roll 15 (strike)
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;

    // frame 9
    frameIndex = frameIndex + 1;
+   if (rolls.get(frameIndex) == 10) {
+       frameScore = rolls.get(frameIndex);
+   } else {
+       frameScore = rolls.get(frameIndex);
+       frameScore += rolls.get(frameIndex + 1);
+   }
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;


    // frame 10
    frameIndex = frameIndex + 2;
+   if (rolls.get(frameIndex) == 10) {
+       frameScore = rolls.get(frameIndex);
+   } else {
+       frameScore = rolls.get(frameIndex);
+       frameScore += rolls.get(frameIndex + 1);
+   }
-   frameScore = rolls.get(frameIndex); // roll 18 (strike)
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;

    return score;
}
```

Puis on la supprimons avec un [[wiki/extract method |extract method]] : 

```diff
public int score() {
    int score = 0;
    int frameIndex = 0;
    int bonus = 0;
    int frameScore = 0;

    // frame 1
    frameIndex = 0;

+   frameScore = calculateFrameScore(frameIndex);
-   if (rolls.get(frameIndex) == 10) {
-       frameScore = rolls.get(frameIndex);
-   } else {
-       frameScore = rolls.get(frameIndex);
-       frameScore += rolls.get(frameIndex + 1);
-   }
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;

    // frame 2
    frameIndex = frameIndex + 2;
+   frameScore = calculateFrameScore(frameIndex);
-   if (rolls.get(frameIndex) == 10) {
-       frameScore = rolls.get(frameIndex);
-   } else {
-       frameScore = rolls.get(frameIndex);
-       frameScore += rolls.get(frameIndex + 1);
-   }
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;

    // frame 3
    frameIndex = frameIndex + 2;
+   frameScore = calculateFrameScore(frameIndex);
-   if (rolls.get(frameIndex) == 10) {
-       frameScore = rolls.get(frameIndex);
-   } else {
-       frameScore = rolls.get(frameIndex);
-       frameScore += rolls.get(frameIndex + 1);
-   }
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;

    // frame 4
    frameIndex = frameIndex + 2;
+   frameScore = calculateFrameScore(frameIndex);
-   if (rolls.get(frameIndex) == 10) {
-       frameScore = rolls.get(frameIndex);
-   } else {
-       frameScore = rolls.get(frameIndex);
-       frameScore += rolls.get(frameIndex + 1);
-   }
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;

    // frame 5
    frameIndex = frameIndex + 2;
+   frameScore = calculateFrameScore(frameIndex);
-   if (rolls.get(frameIndex) == 10) {
-       frameScore = rolls.get(frameIndex);
-   } else {
-       frameScore = rolls.get(frameIndex);
-       frameScore += rolls.get(frameIndex + 1);
-   }
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;

    // frame 6
    frameIndex = frameIndex + 2;
+   frameScore = calculateFrameScore(frameIndex);
-   if (rolls.get(frameIndex) == 10) {
-       frameScore = rolls.get(frameIndex);
-   } else {
-       frameScore = rolls.get(frameIndex);
-       frameScore += rolls.get(frameIndex + 1);
-   }
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;

    // frame 7
    frameIndex = frameIndex + 2;
+   frameScore = calculateFrameScore(frameIndex);
-   if (rolls.get(frameIndex) == 10) {
-       frameScore = rolls.get(frameIndex);
-   } else {
-       frameScore = rolls.get(frameIndex);
-       frameScore += rolls.get(frameIndex + 1);
-   }
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;

    // frame 8
    frameIndex = frameIndex + 2;
+   frameScore = calculateFrameScore(frameIndex);
-   if (rolls.get(frameIndex) == 10) {
-       frameScore = rolls.get(frameIndex);
-   } else {
-       frameScore = rolls.get(frameIndex);
-       frameScore += rolls.get(frameIndex + 1);
-   }
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;

    // frame 9
    frameIndex = frameIndex + 1;
+   frameScore = calculateFrameScore(frameIndex);
-   if (rolls.get(frameIndex) == 10) {
-       frameScore = rolls.get(frameIndex);
-   } else {
-       frameScore = rolls.get(frameIndex);
-       frameScore += rolls.get(frameIndex + 1);
-   }
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;


    // frame 10
    frameIndex = frameIndex + 2;
+   frameScore = calculateFrameScore(frameIndex);
-   if (rolls.get(frameIndex) == 10) {
-       frameScore = rolls.get(frameIndex);
-   } else {
-       frameScore = rolls.get(frameIndex);
-       frameScore += rolls.get(frameIndex + 1);
-   }
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;

    return score;
}

+private int calculateFrameScore(int frameIndex) {  
+    int frameScore;  
+    if (rolls.get(frameIndex) == 10) {  
+        frameScore = rolls.get(frameIndex);  
+    } else {  
+        frameScore = rolls.get(frameIndex);  
+        frameScore += rolls.get(frameIndex + 1);  
+    }  
+    return frameScore;  
+}+

```

> Dev : Il reste encore un concept, l'indice de la prochaine frame. C'est ça ?
> Moi : Tout à fait. Tu commences à comprendre :)
> Dev : Alors, le concept existe déjà, on va donc mettre en évidence la duplication puis la supprimer avec un extract method.
> Moi : Bingo ! 

Voilà la mise en évidence : 

```diff
public int score() {
    int score = 0;
    int frameIndex = 0;
    int bonus = 0;
    int frameScore = 0;

    // frame 1
	frameIndex = 0;

    frameScore = calculateFrameScore(frameIndex);
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;

    // frame 2
+   if (rolls.get(frameIndex) == 10) {
+       frameIndex = frameIndex + 1;
+   } else {
+       frameIndex = frameIndex + 2;
+   }
-   frameIndex = frameIndex + 2;
    frameScore = calculateFrameScore(frameIndex);
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;

    // frame 3
+   if (rolls.get(frameIndex) == 10) {
+       frameIndex = frameIndex + 1;
+   } else {
+       frameIndex = frameIndex + 2;
+   }
-   frameIndex = frameIndex + 2;
    frameScore = calculateFrameScore(frameIndex);
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;

    // frame 4
+   if (rolls.get(frameIndex) == 10) {
+       frameIndex = frameIndex + 1;
+   } else {
+       frameIndex = frameIndex + 2;
+   }
-   frameIndex = frameIndex + 2;
    frameScore = calculateFrameScore(frameIndex);
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;

    // frame 5
+   if (rolls.get(frameIndex) == 10) {
+       frameIndex = frameIndex + 1;
+   } else {
+       frameIndex = frameIndex + 2;
+   }
-   frameIndex = frameIndex + 2;
    frameScore = calculateFrameScore(frameIndex);
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;

    // frame 6
+   if (rolls.get(frameIndex) == 10) {
+       frameIndex = frameIndex + 1;
+   } else {
+       frameIndex = frameIndex + 2;
+   }
-   frameIndex = frameIndex + 2;
    frameScore = calculateFrameScore(frameIndex);
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;

    // frame 7
+   if (rolls.get(frameIndex) == 10) {
+       frameIndex = frameIndex + 1;
+   } else {
+       frameIndex = frameIndex + 2;
+   }
-   frameIndex = frameIndex + 2;
    frameScore = calculateFrameScore(frameIndex);
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;

    // frame 8
+   if (rolls.get(frameIndex) == 10) {
+       frameIndex = frameIndex + 1;
+   } else {
+       frameIndex = frameIndex + 2;
+   }
-   frameIndex = frameIndex + 2;
    frameScore = calculateFrameScore(frameIndex);
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;

    // frame 9
+   if (rolls.get(frameIndex) == 10) {
+       frameIndex = frameIndex + 1;
+   } else {
+       frameIndex = frameIndex + 2;
+   }
-   frameIndex = frameIndex + 1;
    frameScore = calculateFrameScore(frameIndex);
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;


    // frame 10
+   if (rolls.get(frameIndex) == 10) {
+       frameIndex = frameIndex + 1;
+   } else {
+       frameIndex = frameIndex + 2;
+   }
-   frameIndex = frameIndex + 2;
    frameScore = calculateFrameScore(frameIndex);
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;

    return score;
}

```

Et la suppression avec un [[wiki/extract method|extract method]] : 

```diff
public int score() {
    int score = 0;
    int frameIndex = 0;
    int bonus = 0;
    int frameScore = 0;

    // frame 1
    frameIndex = 0;

    frameScore = calculateFrameScore(frameIndex);
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;

    // frame 2
+   frameIndex = nextFrame(frameIndex);
-   if (rolls.get(frameIndex) == 10) {
-       frameIndex = frameIndex + 1;
-   } else {
-       frameIndex = frameIndex + 2;
-   }
    frameScore = calculateFrameScore(frameIndex);
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;

    // frame 3
+   frameIndex = nextFrame(frameIndex);
-   if (rolls.get(frameIndex) == 10) {
-       frameIndex = frameIndex + 1;
-   } else {
-       frameIndex = frameIndex + 2;
-   }
    frameScore = calculateFrameScore(frameIndex);
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;

    // frame 4
+   frameIndex = nextFrame(frameIndex);
-   if (rolls.get(frameIndex) == 10) {
-       frameIndex = frameIndex + 1;
-   } else {
-       frameIndex = frameIndex + 2;
-   }
    frameScore = calculateFrameScore(frameIndex);
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;

    // frame 5
+   frameIndex = nextFrame(frameIndex);
-   if (rolls.get(frameIndex) == 10) {
-       frameIndex = frameIndex + 1;
-   } else {
-       frameIndex = frameIndex + 2;
-   }
    frameScore = calculateFrameScore(frameIndex);
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;

    // frame 6
+   frameIndex = nextFrame(frameIndex);
-   if (rolls.get(frameIndex) == 10) {
-       frameIndex = frameIndex + 1;
-   } else {
-       frameIndex = frameIndex + 2;
-   }
    frameScore = calculateFrameScore(frameIndex);
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;

    // frame 7
+   frameIndex = nextFrame(frameIndex);
-   if (rolls.get(frameIndex) == 10) {
-       frameIndex = frameIndex + 1;
-   } else {
-       frameIndex = frameIndex + 2;
-   }
    frameScore = calculateFrameScore(frameIndex);
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;

    // frame 8
+   frameIndex = nextFrame(frameIndex);
-   if (rolls.get(frameIndex) == 10) {
-       frameIndex = frameIndex + 1;
-   } else {
-       frameIndex = frameIndex + 2;
-   }
    frameScore = calculateFrameScore(frameIndex);
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;

    // frame 9
+   frameIndex = nextFrame(frameIndex);
-   if (rolls.get(frameIndex) == 10) {
-       frameIndex = frameIndex + 1;
-   } else {
-       frameIndex = frameIndex + 2;
-   }
    frameScore = calculateFrameScore(frameIndex);
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;


    // frame 10
+   frameIndex = nextFrame(frameIndex);
-   if (rolls.get(frameIndex) == 10) {
-       frameIndex = frameIndex + 1;
-   } else {
-       frameIndex = frameIndex + 2;
-   }
    frameScore = calculateFrameScore(frameIndex);
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;

    return score;
}

+private int nextFrame(int frameIndex) {
+    if (rolls.get(frameIndex) == 10) {
+        frameIndex = frameIndex + 1;
+    } else {
+        frameIndex = frameIndex + 2;
+    }
+    return frameIndex;
+}
```

> Dev : Et maintenant, la boucle !!!!!
> Moi : Presque, il y a encore une chose qui ne colle pas.
> Dev : ???
> Moi : Tous les blocs de codes sont identiques, sauf pour le frame 1
> Dev : Oui, c'est vrai, on n'a qu'à faire un cas particulier pour ce bloc.
> Moi : Ou alors, on l'aligne avec les autres 
> Dev : ???
> Moi : Tu vas voir

Pour rendre le code identique, on supprime tout simplement l'initialisation de `frameIndex` qui est déjà fait (on aurait pu la supprimer depuis longtemps).

Ensuite, sans cette initialisation, il n'y a pas de calcul de `frameIndex` dans le premier bloc, on déplace donc le calcul du deuxième bloc pour l'associer au premier bloc. On fait ainsi pour chaque bloc.

Enfin, on se rend compte que le dixième bloc est différent des autres. On lui ajoute un `nextFrame` et on lance les tests pour voir si cette ligne supplémentaire ne casse rien.

```diff
public int score() {
    int score = 0;
    int frameIndex = 0;
    int bonus = 0;
    int frameScore = 0;

    // frame 1
-   frameIndex = 0;
    frameScore = calculateFrameScore(frameIndex);
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;
+   frameIndex = nextFrame(frameIndex);

    // frame 2
-   frameIndex = nextFrame(frameIndex);
    frameScore = calculateFrameScore(frameIndex);
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;
+   frameIndex = nextFrame(frameIndex);

    // frame 3
-   frameIndex = nextFrame(frameIndex);
    frameScore = calculateFrameScore(frameIndex);
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;
+   frameIndex = nextFrame(frameIndex);

    // frame 4
-   frameIndex = nextFrame(frameIndex);
    frameScore = calculateFrameScore(frameIndex);
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;
+   frameIndex = nextFrame(frameIndex);

    // frame 5
-   frameIndex = nextFrame(frameIndex);
    frameScore = calculateFrameScore(frameIndex);
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;
+   frameIndex = nextFrame(frameIndex);

    // frame 6
-   frameIndex = nextFrame(frameIndex);
    frameScore = calculateFrameScore(frameIndex);
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;
+   frameIndex = nextFrame(frameIndex);

    // frame 7
-   frameIndex = nextFrame(frameIndex);
    frameScore = calculateFrameScore(frameIndex);
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;
+   frameIndex = nextFrame(frameIndex);

    // frame 8
-   frameIndex = nextFrame(frameIndex);
    frameScore = calculateFrameScore(frameIndex);
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;
+   frameIndex = nextFrame(frameIndex);

    // frame 9
-   frameIndex = nextFrame(frameIndex);
    frameScore = calculateFrameScore(frameIndex);
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;
+   frameIndex = nextFrame(frameIndex);

    // frame 10
-   frameIndex = nextFrame(frameIndex);
    frameScore = calculateFrameScore(frameIndex);
    bonus = calculateFrameBonus(frameIndex);
    score += frameScore + bonus;
+   frameIndex = nextFrame(frameIndex);
    
    return score;
}
```

> Dev : On peut enfin passer à la boucle ?
> Moi : Ouiiiii ! Tous nos blocs sont identiques.

Notre code traite 10 frames. On va donc utiliser un compteur qui va jusqu'à 10 : 

```diff
public int score() {
    int score = 0;
    int frameIndex = 0;
    int bonus = 0;
    int frameScore = 0;

-	// frame 1    
+   for (int frame = 0; frame < 10; frame++) {
        frameScore = calculateFrameScore(frameIndex);
        bonus = calculateFrameBonus(frameIndex);
        score += frameScore + bonus;
        frameIndex = nextFrame(frameIndex);
+   }
-   // frame 2
-   frameScore = calculateFrameScore(frameIndex);
-   bonus = calculateFrameBonus(frameIndex);
-   score += frameScore + bonus;
-   frameIndex = nextFrame(frameIndex);

-   // frame 3
-   frameScore = calculateFrameScore(frameIndex);
-   bonus = calculateFrameBonus(frameIndex);
-   score += frameScore + bonus;
-   frameIndex = nextFrame(frameIndex);

-   // frame 4
-   frameScore = calculateFrameScore(frameIndex);
-   bonus = calculateFrameBonus(frameIndex);
-   score += frameScore + bonus;
-   frameIndex = nextFrame(frameIndex);

-   // frame 5
-   frameScore = calculateFrameScore(frameIndex);
-   bonus = calculateFrameBonus(frameIndex);
-   score += frameScore + bonus;
-   frameIndex = nextFrame(frameIndex);

-   // frame 6
-   frameScore = calculateFrameScore(frameIndex);
-   bonus = calculateFrameBonus(frameIndex);
-   score += frameScore + bonus;
-   frameIndex = nextFrame(frameIndex);

-   // frame 7
-   frameScore = calculateFrameScore(frameIndex);
-   bonus = calculateFrameBonus(frameIndex);
-   score += frameScore + bonus;
-   frameIndex = nextFrame(frameIndex);

-   // frame 8
-   frameScore = calculateFrameScore(frameIndex);
-   bonus = calculateFrameBonus(frameIndex);
-   score += frameScore + bonus;
-   frameIndex = nextFrame(frameIndex);

-   // frame 9
-   frameScore = calculateFrameScore(frameIndex);
-   bonus = calculateFrameBonus(frameIndex);
-   score += frameScore + bonus;
-   frameIndex = nextFrame(frameIndex);

-   // frame 10
-   frameScore = calculateFrameScore(frameIndex);
-   bonus = calculateFrameBonus(frameIndex);
-   score += frameScore + bonus;
-   frameIndex = nextFrame(frameIndex);

    return score;
}
```

La portée de nos variables peut être diminuée : 

```diff
public int score() {
    int score = 0;
    int frameIndex = 0;
-   int bonus = 0;
-   int frameScore = 0;

    // frame 1
    for (int frame = 0; frame < 10; frame++) {
+       int frameScore = calculateFrameScore(frameIndex);
+       int bonus = calculateFrameBonus(frameIndex);
-       frameScore = calculateFrameScore(frameIndex);
-       bonus = calculateFrameBonus(frameIndex);
        score += frameScore + bonus;
        frameIndex = nextFrame(frameIndex);
    }

    return score;
}
```

Les méthodes sont aussi parlantes que nos variables, on peut donc les supprimer : 

```diff
public int score() {
    int score = 0;
    int frameIndex = 0;

    // frame 1
    for (int frame = 0; frame < 10; frame++) {
+       score += calculateFrameScore(frameIndex) + calculateFrameBonus(frameIndex);
-       int frameScore = calculateFrameScore(frameIndex);
-       int bonus = calculateFrameBonus(frameIndex);
-       score += frameScore + bonus;
        frameIndex = nextFrame(frameIndex);
    }

    return score;
}
```

On peut simplifier la lecture en introduisant les concepts de strike et de spare : 

```diff
private int nextFrame(int frameIndex) {
+   if (isStrike(frameIndex)) {
-   if (rolls.get(frameIndex) == 10) {
        frameIndex = frameIndex + 1;
    } else {
        frameIndex = frameIndex + 2;
    }
    return frameIndex;
}

private int calculateFrameScore(int frameIndex) {
    int frameScore;
+   if (isStrike(frameIndex)) {
-   if (rolls.get(frameIndex) == 10) {
        frameScore = rolls.get(frameIndex);
    } else {
        frameScore = rolls.get(frameIndex);
        frameScore += rolls.get(frameIndex + 1);
    }
    return frameScore;
}

public int calculateFrameBonus(int frameIndex) {
    int bonus = 0;
+   if (isStrike(frameIndex)) {
-   if (rolls.get(frameIndex) == 10) {
        bonus = rolls.get(frameIndex + 1);
        bonus += rolls.get(frameIndex + 2);
    } else if (isSpare(frameIndex))
-   } else if (rolls.get(frameIndex) + rolls.get(frameIndex + 1) == 10)
        bonus = rolls.get(frameIndex + 2);
    else
        bonus = 0;
    return bonus;
+}

+private boolean isSpare(int frameIndex) {
+    return rolls.get(frameIndex) + rolls.get(frameIndex + 1) == 10;
+}

+private boolean isStrike(int frameIndex) {
+   return rolls.get(frameIndex) == 10;
+}
```

Et voilà ! Nous pourrions encore faire du refactoring, mais ce code fonctionne et je le trouve satisfaisant pour l'exercice.

## La classe Bowling : 
```java
public class Bowling {  
    List<Integer> rolls = new ArrayList<>();  
  
    public void roll(int pins) {  
        rolls.add(pins);  
    }  
  
    public int score() {  
        int score = 0;  
        int frameIndex = 0;  
  
        // frame 1  
        for (int frame = 0; frame < 10; frame++) {  
            score += calculateFrameScore(frameIndex) + calculateFrameBonus(frameIndex);  
            frameIndex = nextFrame(frameIndex);  
        }  
  
        return score;  
    }  
  
    private int nextFrame(int frameIndex) {  
        if (isStrike(frameIndex)) {
            frameIndex = frameIndex + 1;  
        } else {  
            frameIndex = frameIndex + 2;  
        }  
        return frameIndex;  
    }  
  
    private int calculateFrameScore(int frameIndex) {  
        int frameScore;  
        if (isStrike(frameIndex)) {  
            frameScore = rolls.get(frameIndex);  
        } else {  
            frameScore = rolls.get(frameIndex);  
            frameScore += rolls.get(frameIndex + 1);  
        }  
        return frameScore;  
    }  
  
    public int calculateFrameBonus(int frameIndex) {  
        int bonus = 0;  
        if (isStrike(frameIndex)) {  
            bonus = rolls.get(frameIndex + 1);  
            bonus += rolls.get(frameIndex + 2);  
        } else if (isSpare(frameIndex))  
            bonus = rolls.get(frameIndex + 2);  
        else
            bonus = 0;  
        return bonus;  
    }  
  
    private boolean isSpare(int frameIndex) {  
        return rolls.get(frameIndex) + rolls.get(frameIndex + 1) == 10;  
    }  
  
    private boolean isStrike(int frameIndex) {  
        return rolls.get(frameIndex) == 10;  
    }  
}
```

