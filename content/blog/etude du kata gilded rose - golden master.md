---
title: "etude du kata gilded rose - golden master"
date: "2022-05-06T16:32:50+02:00"
tags:
- étude-de-kata
- refactoring
- golden-master
- approval-testing
---

Dans cette étude de kata, nous allons étudier [[wiki/gilded rose|gilded rose]]. Je vais te montrer comment mettre en place très rapidement un test de caractérisation avc un golden master et le résoudre encore plus rapidement grâce à une astuce de refactoring.

## Le code initial
Voici le code sur lequel on va travailler : 

```java
package com.gildedrose;

class GildedRose {
    Item[] items;

    public GildedRose(Item[] items) {
        this.items = items;
    }

    public void updateQuality() {
        for (int i = 0; i < items.length; i++) {
            if (!items[i].name.equals("Aged Brie")
                    && !items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
                if (items[i].quality > 0) {
                    if (!items[i].name.equals("Sulfuras, Hand of Ragnaros")) {
                        items[i].quality = items[i].quality - 1;
                    }
                }
            } else {
                if (items[i].quality < 50) {
                    items[i].quality = items[i].quality + 1;

                    if (items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
                        if (items[i].sellIn < 11) {
                            if (items[i].quality < 50) {
                                items[i].quality = items[i].quality + 1;
                            }
                        }

                        if (items[i].sellIn < 6) {
                            if (items[i].quality < 50) {
                                items[i].quality = items[i].quality + 1;
                            }
                        }
                    }
                }
            }

            if (!items[i].name.equals("Sulfuras, Hand of Ragnaros")) {
                items[i].sellIn = items[i].sellIn - 1;
            }

            if (items[i].sellIn < 0) {
                if (!items[i].name.equals("Aged Brie")) {
                    if (!items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
                        if (items[i].quality > 0) {
                            if (!items[i].name.equals("Sulfuras, Hand of Ragnaros")) {
                                items[i].quality = items[i].quality - 1;
                            }
                        }
                    } else {
                        items[i].quality = items[i].quality - items[i].quality;
                    }
                } else {
                    if (items[i].quality < 50) {
                        items[i].quality = items[i].quality + 1;
                    }
                }
            }
        }
    }
}
```

Charmant, n'est-ce pas ? On peut trouver le projet complet sur [le github d'Emily Bache](https://github.com/emilybache/GildedRose-Refactoring-Kata).

Après l'avoir récupéré, je t'invite à l'étudier. Il y a la classe `GildedRose` qui contient la méthode ci-dessus. Il y a aussi un test unitaire `GildedRoseTest.java` qui ne sert pas à grand chose, si ce n'est avoir un point de départ.

Il y a aussi un fichier `TexttestFixture.java` que j'utilisais pour mes précédents *golden master*.

## Mise en place du golden master
Le [[wiki/golden master|golden master]] nous permet de mettre en place très simplement un test qui va nous permettre de refactorer le code tranquillement. Il s'agit tout simplement de stocker dans un fichier la sortie créée par une fonctionnalité existante et de vérifier qu'on génère toujours la même sortie.

Je vais recycler le test foo qui est fourni dans le projet pour mettre en place ce *golden master*. Pour être honnête, je n'ai pas pris le temps (j'ai oublié :p) de renommer le test automatique.

On crée notre golden master dans une simple chaîne de caractère et on utilise la librairie [[wiki/ApprovalTests|ApprovalTests]] pour le valider. On en profite pour supprimer le `assertEquals` qui ne servait à rien : 

```diff
     @Test
     void foo() {
+        StringBuilder golden_master = new StringBuilder();

         Item[] items = new Item[] { new Item("foo", 0, 0) };
         GildedRose app = new GildedRose(items);
         app.updateQuality();
-        assertEquals("fixme", app.items[0].name);
+
+        golden_master.append(items[0].toString() + "\n");
+
+        Approvals.verify(golden_master.toString());
+
     }

 }

```

Après avoir exécuté ce test, un outil de comparaison s'ouvre (Winmerge dans mon cas) pour indiquer que le golden master généré ne correspond pas au golden master de référence. C'est normal. Cette référence va évoluer au fil du temps jusqu'à ce qu'elle soit suffisante pour nous servir de test de non-regresssion.

### Le code coverage

> Novice : Est-ce que le test est terminé ?  
> Expert : Non, le test doit couvrir l'intégralité de la fonctionnalité  
> Novice : Comment savoir si c'est le cas ?  
> Expert : On ne peut jamais en être sûr, mais il y a un outil qui va nous aider.  
> Novice : Lequel ? 
> Expert : Le code coverage !

La couverture de code (ou code coverage) nous indique le pourcentage de code exécuté lors de la dernière exécution. Elle nous indique aussi les lignes qui ont (ou n'ont pas) été exécutées.

Je considère le code coverage (ou couverture de code) comme un outil. Je pense que ce n'est pas une métrique fiable, mais elle peut nous guider pour la mise en place de nos tests. 

Actuellement, ma couverture de code est de 42%. Je vais compléter mon test pour essayer de couvrir un peu plus.

Pour cela, j'analyse le code qui a été couvert et celui qui ne l'a pas été. Ce qui donne ça : 

```java
public void updateQuality() {   
    for (int i = 0; i < items.length; i++) {  
        if (!items[i].name.equals("Aged Brie")  
                && !items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {  
            if (items[i].quality > 0) {  
            
            // le code ci-dessous n'a pas été couvert
                if (!items[i].name.equals("Sulfuras, Hand of Ragnaros")) {  
                    items[i].quality = items[i].quality - 1;  
                }  
            }
		...        
```

Je ne suis pas rentré dans la condition à cause de l'attribut `quality` de mon item. Je constate que c'est un critère que je peux faire évoluer dans mon test. Je vais m'en servir pour augmenter la couverture de code.

J'analyse l'utilisation de cet attribut et je constate qu'on l'utilise souvent dans des conditions. Cet attribut semble être borné par les valeurs `0` et `50`. Je vais manipuler cet attribut comme une entrée de ma fonctionnalité (j'y ai accès) et le faire varier pour voir ce qu'il se passe. Pour être plus prudent, je vais augmenter les bornes et les passer de `-50` à `100`.

Je vais évaluer chacune de ces évolutions et mémoriser la sortie dans mon golden master. Ce qui donne le code suivant : 

```diff
    @Test
    void foo() {
        StringBuilder golden_master = new StringBuilder();

+       for (int quality = -50; quality < 100; quality++) {
+           Item[] items = new Item[] { new Item("foo", 0, quality) };
-           Item[] items = new Item[] { new Item("foo", 0, 0) };
            GildedRose app = new GildedRose(items);            
            app.updateQuality();
            golden_master.append(items[0].toString() + "\n");
+       }
        Approvals.verify(golden_master.toString()); 
    }
```

La couverture de code a augmenté à 63%

Si l'on étudie le code, on constate que l'on bloque sur le nom d'un des items. Nous ne passons pas dans le code de l'item `AgedBrie`.

On va donc faire la même chose que précédemment, nous allons étudier le champ des possibles pour l'attribut `name`. On constate que l'on vérifie 3 valeurs dans le code : 
- `"Aged Brie"`
- `"Backstage passes to a TAFKAL80ETC concert"`
- `"Sulfuras, Hand of Ragnaros"`

Et si l'on ajoute notre premier item `foo` (je sais, on aurait pu utiliser un nom plus orienté métier), cela nous fait au moins 4 valeurs à tester : 

```diff
    @Test
    void foo() {
        StringBuilder golden_master = new StringBuilder();
+       String names[] = new String[] { "Foo", "Aged Brie", "Sulfuras, Hand of Ragnaros", "Backstage passes to a TAFKAL80ETC concert" };

+       for (String name: names) {
            for (int quality = -50; quality < 100; quality++) {
+               Item[] items = new Item[] { new Item(name, 0, quality) };
-               Item[] items = new Item[] { new Item("foo", 0, quality) };
                GildedRose app = new GildedRose(items);
                app.updateQuality();
                golden_master.append(items[0].toString() + "\n");
            }
+       }
        Approvals.verify(golden_master.toString());
    }
```

> Novice : Et voilà, notre code coverage est passé à 100% ! Place au refactoring !  
> Expert : Oula, il y a encore des points à vérifier.  
> Novice : ???  
> Expert : Oui, un code coverage de 100% ne nous garantit pas que l'on passe partout.  
> Novice : ???   

Effectivement, le code coverage est passé à 100 %. Mais cela ne veut pas dire que notre test est terminé. Nous allons devoir utiliser l'expérience et l'intuition pour vérifier cela.

Lorsqu'on analyse le code, on constate qu'il y a un attribut `sellIn` qui est utilisé lui aussi dans des conditions. Si l'on vérifie, on constate que l'utilisation de cet attribut est bornée entre 0 et 11. Nous allons donc partir sur des valeurs entre -20 et 20.

Ce qui donne ça :

```diff
    @Test
    void foo() {
        StringBuilder golden_master = new StringBuilder();
        String names[] = new String[] { "Foo", "Aged Brie", "Sulfuras, Hand of Ragnaros", "Backstage passes to a TAFKAL80ETC concert" };

        for (String name: names) {
+           for (int sellIn = -20; sellIn < 20; sellIn++) {
                for (int quality = -50; quality < 100; quality++) {
+                   Item[] items = new Item[] { new Item(name, sellIn, quality) };
-                   Item[] items = new Item[] { new Item(name, 0, quality) };
                    GildedRose app = new GildedRose(items);
                    app.updateQuality();
                    golden_master.append(items[0].toString() + "\n");
                }
+           }
        }
        Approvals.verify(golden_master.toString());
    }
```

Et voilà, avec ce test, notre code est bien couvert. Nous allons pouvoir commencer le refactoring.

Je te propose de lire cela dans la seconde partie.

Merci d'avoir pris le temps de me lire.

## Reference
