---
title: "etude du kata gilded rose - switch"
date: "2022-05-11T00:44:16+02:00"
tags:
- étude-de-kata
- refactoring
---

Parfois, il suffit d'une simple astuce pour faire en quelques minutes un travail qui peut prendre plusieurs heures. C'est pourquoi il est primordial de s'exercer en permanence pour découvrir ses astuces et pouvoir les réutiliser le moment opportun.

Ici, nous allons rusher le kata [[wiki/gilded rose|gilded rose]]. La technique que je vais utiliser est très intéressante, mais fait perdre tout son intérêt à ce kata. Je t'invite donc à exécuter ce kata de deux manières, avec celle que tu vas découvrir ici et la manière plus traditionnelle.

Cette étude est la suite de l'article [[blog/etude du kata gilded rose - golden master|etude du kata gilded rose - golden master]]. Si tu ne l'as pas déjà lu, je te recommande de commencer par là. En effet, il faut pouvoir lancer des tests automatiques pour commencer le refactoring.

## tl;dr
Le `switch` et le code coverage permette de faire ce kata en un temps record. Le switch permet de dupliquer le code pour chaque item en fonction de son nom puis le code coverage permet de supprimer le code que l'on n'utilise pas en fonction de l'item.

## Le code initial 

Voici le code initial du [[wiki/gilded rose|gilded rose]] que l'on doit retravailler :

```java
package com.gildedrose;  
  
class GildedRose {  
    Item[] items;  
  
    public GildedRose(Item[] items) {  
        this.items = items;  
    }  
  
    public void updateQuality() {  
        for (int i = 0; i < items.length; i++) {  
            if (!items[i].name.equals("Aged Brie") && !items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {  
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

## Rushons le kata
La technique que je vais employer fonctionne pour une bonne raison. Il n'y a pas d'effet de bord dans le code actuel, si ce n'est modifier les attributs `quality` ou `sellIn`. 

> - Novice : Je ne vois pas comment tu vas faire pour nettoyer ce code en quelques minutes  
> - Expert : Pour cela, on va utiliser un mot clé du langage. Il n'existe pas dans tous les langages, mais il est facile à remplacer si besoin  
> - Novice : Et c'est quoi ce mot clé ???  
> - Expert : Le switch, et s'il n'existe pas, plusieurs if imbriqué  
> - Novice : ???
> - Expert : Tu vas voir !

Oui, nous allons utiliser un `switch`  pour régler ce kata. Cela est possible parce qu'il y a un élément qui intervient énormément dans les règles métiers du gilded rose, mais qui ne change jamais pour un item donné. C'est l'attribut `name`.

Voyons la mise en place du switch : 

```diff
public void updateQuality() {
        for (int i = 0; i < items.length; i++) {
+           switch (items[i].name) {
+               default:
                    if (!items[i].name.equals("Aged Brie") && !items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
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
+                   }
            }
        }
    }
```

### case "Aged Brie"

Puis nous dupliquons le code de cette manière.

```diff
public void updateQuality() {
        for (int i = 0; i < items.length; i++) {
            switch (items[i].name) {
+               case "Aged Brie":
+                   if (!items[i].name.equals("Aged Brie") && !items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
+                       if (items[i].quality > 0) {
+                           if (!items[i].name.equals("Sulfuras, Hand of Ragnaros")) {
+                               items[i].quality = items[i].quality - 1;
+                           }
+                       }
+                   } else {
+                       if (items[i].quality < 50) {
+                           items[i].quality = items[i].quality + 1;
+
+                           if (items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
+                               if (items[i].sellIn < 11) {
+                                   if (items[i].quality < 50) {
+                                       items[i].quality = items[i].quality + 1;
+                                   }
+                               }
+
+                               if (items[i].sellIn < 6) {
+                                   if (items[i].quality < 50) {
+                                       items[i].quality = items[i].quality + 1;
+                                   }
+                               }
+                           }
+                       }
+                   }
+
+                   if (!items[i].name.equals("Sulfuras, Hand of Ragnaros")) {
+                       items[i].sellIn = items[i].sellIn - 1;
+                   }
+
+                   if (items[i].sellIn < 0) {
+                       if (!items[i].name.equals("Aged Brie")) {
+                           if (!items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
+                               if (items[i].quality > 0) {
+                                   if (!items[i].name.equals("Sulfuras, Hand of Ragnaros")) {
+                                       items[i].quality = items[i].quality - 1;
+                                   }
+                               }
+                           } else {
+                               items[i].quality = items[i].quality - items[i].quality;
+                           }
+                       } else {
+                           if (items[i].quality < 50) {
+                               items[i].quality = items[i].quality + 1;
+                           }
+                       }
+                   }
+
+                   break;
                default:
                    if (!items[i].name.equals("Aged Brie") && !items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
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

> - Novice : Bravo ! Tu as deux fois plus de code maintenant. Que comptes-tu faire ?  
> - Expert : Je vais tout simplement reexécuter mon test avec le code coverage  
> - Novice : Le code coverage ?  
> - Expert : Mais oui, souviens-toi, on en a parlé dans la partie précédente  
> - Novice : Et qu'est-ce que ça va faire ?  
> - Expert : De la magie !  

J'ai oublié de le préciser, nous allons utiliser le code coverage pour réaliser ce kata. Grâce à cela, plus besoin de réfléchir, c'est le test qui le fait pour nous.

Donc, nous lançons le test avec le code coverage et nous constatons que certaines lignes ne sont pas exécutées dans le premier cas, comme dans le cas par défaut. Nous les supprimons. Dans le diff suivant, toutes les lignes supprimées correspondent aux lignes qui n'ont pas été exécutées :

```diff
public void updateQuality() {
        for (int i = 0; i < items.length; i++) {
            switch (items[i].name) {
                case "Aged Brie":
                    if (!items[i].name.equals("Aged Brie") && !items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
-                       if (items[i].quality > 0) {
-                           if (!items[i].name.equals("Sulfuras, Hand of Ragnaros")) {
-                               items[i].quality = items[i].quality - 1;
-                           }
-                       }
                    } else {
                        if (items[i].quality < 50) {
                            items[i].quality = items[i].quality + 1;

                            if (items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
-                               if (items[i].sellIn < 11) {
-                                   if (items[i].quality < 50) {
-                                       items[i].quality = items[i].quality + 1;
-                                   }
-                               }
-
-                               if (items[i].sellIn < 6) {
-                                   if (items[i].quality < 50) {
-                                       items[i].quality = items[i].quality + 1;
-                                   }
-                               }
                            }
                        }
                    }

                    if (!items[i].name.equals("Sulfuras, Hand of Ragnaros")) {
                        items[i].sellIn = items[i].sellIn - 1;
                    }

                    if (items[i].sellIn < 0) {
                        if (!items[i].name.equals("Aged Brie")) {
-                           if (!items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
-                               if (items[i].quality > 0) {
-                                   if (!items[i].name.equals("Sulfuras, Hand of Ragnaros")) {
-                                       items[i].quality = items[i].quality - 1;
-                                   }
-                               }
-                           } else {
-                               items[i].quality = items[i].quality - items[i].quality;
-                           }
                        } else {
                            if (items[i].quality < 50) {
                                items[i].quality = items[i].quality + 1;
                            }
                        }
                    }

                    break;
                default:
                    if (!items[i].name.equals("Aged Brie") && !items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
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
-                           if (items[i].quality < 50) {
-                               items[i].quality = items[i].quality + 1;
-                           }
                        }
                    }
            }
        }
    }
```

Suite à cela, nous nous retrouvons avec des blocs `if / else` vides. Si malgré le test, nous ne rentrons jamais dans un bloc `if` ou dans un bloc `else`, cela signifie que la condition est respectivement soit toujours fause, soit toujours vrai. Nous pouvons donc supprimer le bloc correspondant. C'est simple parce que c'est le bloc vide qu'il faut supprimer.

Petit exemple avec le premier bloc : 

```diff
public void updateQuality() {
        for (int i = 0; i < items.length; i++) {
            switch (items[i].name) {
                case "Aged Brie":
-                   if (!items[i].name.equals("Aged Brie") && !items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
-                   } else {
                        if (items[i].quality < 50) {
                            items[i].quality = items[i].quality + 1;

                            if (items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
-                           }
                        }
                    }

                    if (!items[i].name.equals("Sulfuras, Hand of Ragnaros")) {
                        items[i].sellIn = items[i].sellIn - 1;
                    }

                    if (items[i].sellIn < 0) {
                        if (!items[i].name.equals("Aged Brie")) {
                        } else {
                            if (items[i].quality < 50) {
                                items[i].quality = items[i].quality + 1;
                            }
                        }
                    }

                    break;
                default:
                    if (!items[i].name.equals("Aged Brie") && !items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
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
                        }
                    }
            }
        }
    }
```

Je vais un peu plus vite en supprimant tous les blocs vides : 

```diff
public void updateQuality() {
        for (int i = 0; i < items.length; i++) {
            switch (items[i].name) {
                case "Aged Brie":
                    if (items[i].quality < 50) {
                        items[i].quality = items[i].quality + 1;
-
-                       if (items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
-                       }
                    }

                    if (!items[i].name.equals("Sulfuras, Hand of Ragnaros")) {
                        items[i].sellIn = items[i].sellIn - 1;
                    }

                    if (items[i].sellIn < 0) {
-                       if (!items[i].name.equals("Aged Brie")) {
-                       } else {
                            if (items[i].quality < 50) {
                                items[i].quality = items[i].quality + 1;
                            }
                        }
-                   }

                    break;
                default:
                    if (!items[i].name.equals("Aged Brie") && !items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
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
-                       } else {
                        }
                    }
            }
        }
    }
```

Enfin, il nous reste un bloc dans lequel on vérifie si l'attribut `name` est différent de `"Sulfuras, Hand of Ragnaros"`. Nous sommes sûrs de travailler avec un `"Aged Brie"`. La condition est toujours vrai dans ce bloc `case`, nous pouvons donc la supprimer (mais on garde le code à l'intérieur) : 

```diff
public void updateQuality() {
        for (int i = 0; i < items.length; i++) {
            switch (items[i].name) {
                case "Aged Brie":
                    if (items[i].quality < 50) {
                        items[i].quality = items[i].quality + 1;
                    }

-                   if (!items[i].name.equals("Sulfuras, Hand of Ragnaros")) {
                        items[i].sellIn = items[i].sellIn - 1;
-                   }

                    if (items[i].sellIn < 0) {
                        if (items[i].quality < 50) {
                            items[i].quality = items[i].quality + 1;
                        }
                    }

                    break;
                default:
                    if (!items[i].name.equals("Aged Brie") && !items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
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
                        }
                    }
            }
        }
    }
```

### case "Backstage passes to a TAFKAL80ETC concert"
```diff
public void updateQuality() {
        for (int i = 0; i < items.length; i++) {
            switch (items[i].name) {
                case "Aged Brie":
                    if (items[i].quality < 50) {
                        items[i].quality = items[i].quality + 1;
                    }

                    items[i].sellIn = items[i].sellIn - 1;

                    if (items[i].sellIn < 0) {
                        if (items[i].quality < 50) {
                            items[i].quality = items[i].quality + 1;
                        }
                    }

                    break;
+
+               case "Backstage passes to a TAFKAL80ETC concert":
+                   if (!items[i].name.equals("Aged Brie") && !items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
+                       if (items[i].quality > 0) {
+                           if (!items[i].name.equals("Sulfuras, Hand of Ragnaros")) {
+                               items[i].quality = items[i].quality - 1;
+                           }
+                       }
+                   } else {
+                       if (items[i].quality < 50) {
+                           items[i].quality = items[i].quality + 1;
+
+                           if (items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
+                               if (items[i].sellIn < 11) {
+                                   if (items[i].quality < 50) {
+                                       items[i].quality = items[i].quality + 1;
+                                   }
+                               }
+
+                               if (items[i].sellIn < 6) {
+                                   if (items[i].quality < 50) {
+                                       items[i].quality = items[i].quality + 1;
+                                   }
+                               }
+                           }
+                       }
+                   }
+
+                   if (!items[i].name.equals("Sulfuras, Hand of Ragnaros")) {
+                       items[i].sellIn = items[i].sellIn - 1;
+                   }
+
+                   if (items[i].sellIn < 0) {
+                       if (!items[i].name.equals("Aged Brie")) {
+                           if (!items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
+                               if (items[i].quality > 0) {
+                                   if (!items[i].name.equals("Sulfuras, Hand of Ragnaros")) {
+                                       items[i].quality = items[i].quality - 1;
+                                   }
+                               }
+                           } else {
+                               items[i].quality = items[i].quality - items[i].quality;
+                           }
+                       }
+                   }
+
+                   break;
+
                default:
                    if (!items[i].name.equals("Aged Brie") && !items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
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
                        }
                    }
            }
        }
    }
```

Puis nous relançons les tests avec le code coverage, nous supprimons le code mort, nous supprimons les blocs de code `if / else` qui sont forcément faux (ou vrai).

Ce qui donne : 

```diff
public void updateQuality() {
        for (int i = 0; i < items.length; i++) {
            switch (items[i].name) {
                case "Aged Brie":
                    if (items[i].quality < 50) {
                        items[i].quality = items[i].quality + 1;
                    }

                    items[i].sellIn = items[i].sellIn - 1;

                    if (items[i].sellIn < 0) {
                        if (items[i].quality < 50) {
                            items[i].quality = items[i].quality + 1;
                        }
                    }

                    break;

                case "Backstage passes to a TAFKAL80ETC concert":
-                   if (!items[i].name.equals("Aged Brie") && !items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
-                       if (items[i].quality > 0) {
-                           if (!items[i].name.equals("Sulfuras, Hand of Ragnaros")) {
-                               items[i].quality = items[i].quality - 1;
-                           }
-                       }
-                   } else {
                        if (items[i].quality < 50) {
                            items[i].quality = items[i].quality + 1;

-                           if (items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
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
-                       }
-                   }

-                   if (!items[i].name.equals("Sulfuras, Hand of Ragnaros")) {
                        items[i].sellIn = items[i].sellIn - 1;
-                   }

                    if (items[i].sellIn < 0) {
-                       if (!items[i].name.equals("Aged Brie")) {
-                           if (!items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
-                               if (items[i].quality > 0) {
-                                   if (!items[i].name.equals("Sulfuras, Hand of Ragnaros")) {
-                                       items[i].quality = items[i].quality - 1;
-                                   }
-                               }
-                           } else {
                                items[i].quality = items[i].quality - items[i].quality;
                            }
-                       }
-                   }

                    break;

                default:
                    if (!items[i].name.equals("Aged Brie") && !items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
                        if (items[i].quality > 0) {
                            if (!items[i].name.equals("Sulfuras, Hand of Ragnaros")) {
                                items[i].quality = items[i].quality - 1;
                            }
                        }
-                   } else {
-                       if (items[i].quality < 50) {
-                           items[i].quality = items[i].quality + 1;
-
-                           if (items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
-                               if (items[i].sellIn < 11) {
-                                   if (items[i].quality < 50) {
-                                       items[i].quality = items[i].quality + 1;
-                                   }
-                               }
-
-                               if (items[i].sellIn < 6) {
-                                   if (items[i].quality < 50) {
-                                       items[i].quality = items[i].quality + 1;
-                                   }
-                               }
-                           }
-                       }
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
-                           } else {
-                               items[i].quality = items[i].quality - items[i].quality;
                            }
                        }
                    }
            }
        }
    }
```

### case "Sulfuras, Hand of Ragnaros"

Nous faisons la même chose pour l'item `Sulfuras, Hand of Ragnaros"` .

Duplication dans un nouveau case : 

```diff
public void updateQuality() {
        for (int i = 0; i < items.length; i++) {
            switch (items[i].name) {
                case "Aged Brie":
                    if (items[i].quality < 50) {
                        items[i].quality = items[i].quality + 1;
                    }

                    items[i].sellIn = items[i].sellIn - 1;

                    if (items[i].sellIn < 0) {
                        if (items[i].quality < 50) {
                            items[i].quality = items[i].quality + 1;
                        }
                    }

                    break;

                case "Backstage passes to a TAFKAL80ETC concert":

                    if (items[i].quality < 50) {
                        items[i].quality = items[i].quality + 1;


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

                    items[i].sellIn = items[i].sellIn - 1;

                    if (items[i].sellIn < 0) {
                        items[i].quality = items[i].quality - items[i].quality;
                    }

                    break;
+               case "Sulfuras, Hand of Ragnaros":
+                   if (!items[i].name.equals("Aged Brie") && !items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
+                       if (items[i].quality > 0) {
+                           if (!items[i].name.equals("Sulfuras, Hand of Ragnaros")) {
+                               items[i].quality = items[i].quality - 1;
+                           }
+                       }
+                   }

+                   if (!items[i].name.equals("Sulfuras, Hand of Ragnaros")) {
+                       items[i].sellIn = items[i].sellIn - 1;
+                   }
+
+                   if (items[i].sellIn < 0) {
+                       if (!items[i].name.equals("Aged Brie")) {
+                           if (!items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
+                               if (items[i].quality > 0) {
+                                   if (!items[i].name.equals("Sulfuras, Hand of Ragnaros")) {
+                                       items[i].quality = items[i].quality - 1;
+                                   }
+                               }
+                           }
+                       }
+                   }
+
+                   break;
                default:
                    if (!items[i].name.equals("Aged Brie") && !items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
                        if (items[i].quality > 0) {
                            if (!items[i].name.equals("Sulfuras, Hand of Ragnaros")) {
                                items[i].quality = items[i].quality - 1;
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
                            }
                        }
                    }
            }
        }
    }
```

Puis nous supprimons le code inutile : 

```diff
public void updateQuality() {
        for (int i = 0; i < items.length; i++) {
            switch (items[i].name) {
                case "Aged Brie":
                    if (items[i].quality < 50) {
                        items[i].quality = items[i].quality + 1;
                    }

                    items[i].sellIn = items[i].sellIn - 1;

                    if (items[i].sellIn < 0) {
                        if (items[i].quality < 50) {
                            items[i].quality = items[i].quality + 1;
                        }
                    }

                    break;

                case "Backstage passes to a TAFKAL80ETC concert":

                    if (items[i].quality < 50) {
                        items[i].quality = items[i].quality + 1;


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

                    items[i].sellIn = items[i].sellIn - 1;

                    if (items[i].sellIn < 0) {
                        items[i].quality = items[i].quality - items[i].quality;
                    }

                    break;
                case "Sulfuras, Hand of Ragnaros":
-                   if (!items[i].name.equals("Aged Brie") && !items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
-                       if (items[i].quality > 0) {
-                           if (!items[i].name.equals("Sulfuras, Hand of Ragnaros")) {
-                               items[i].quality = items[i].quality - 1;
-                           }
-                       }
-                   }
-
-                   if (!items[i].name.equals("Sulfuras, Hand of Ragnaros")) {
-                       items[i].sellIn = items[i].sellIn - 1;
-                   }
-
-                   if (items[i].sellIn < 0) {
-                       if (!items[i].name.equals("Aged Brie")) {
-                           if (!items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
-                               if (items[i].quality > 0) {
-                                   if (!items[i].name.equals("Sulfuras, Hand of Ragnaros")) {
-                                       items[i].quality = items[i].quality - 1;
-                                   }
-                               }
-                           }
-                       }
-                   }

                    break;
                default:
                    if (!items[i].name.equals("Aged Brie") && !items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
                        if (items[i].quality > 0) {
                            if (!items[i].name.equals("Sulfuras, Hand of Ragnaros")) {
                                items[i].quality = items[i].quality - 1;
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
                            }
                        }
                    }
            }
        }
    }
```

> - Novice : Il n'y a plus de code  
> - Expert : Effectivement, si on lit le cahier des charges, cet item ne doit jamais changer. Il n'a donc pas de règle métier

### default case : 
Nous allons maintenant traiter le cas par défaut.

Nous avons géré tous les éléments ayant un nom spécifique dans le switch. Nous n'avons plus que des éléments pour lequel le nom n'a plus d'importance métier. Nous allons donc supprimer toutes les conditions faisant référence au nom, tout en conservant le code des blocs `if` forcément exécutés : 

```diff
public void updateQuality() {
        for (int i = 0; i < items.length; i++) {
            switch (items[i].name) {
                case "Aged Brie":
                    if (items[i].quality < 50) {
                        items[i].quality = items[i].quality + 1;
                    }

                    items[i].sellIn = items[i].sellIn - 1;

                    if (items[i].sellIn < 0) {
                        if (items[i].quality < 50) {
                            items[i].quality = items[i].quality + 1;
                        }
                    }

                    break;

                case "Backstage passes to a TAFKAL80ETC concert":

                    if (items[i].quality < 50) {
                        items[i].quality = items[i].quality + 1;


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

                    items[i].sellIn = items[i].sellIn - 1;

                    if (items[i].sellIn < 0) {
                        items[i].quality = items[i].quality - items[i].quality;
                    }

                    break;
                case "Sulfuras, Hand of Ragnaros":

                    break;
                default:
-                   if (!items[i].name.equals("Aged Brie") && !items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
                        if (items[i].quality > 0) {
-                           if (!items[i].name.equals("Sulfuras, Hand of Ragnaros")) {
                                items[i].quality = items[i].quality - 1;
                            }
-                       }
-                   }

-                   if (!items[i].name.equals("Sulfuras, Hand of Ragnaros")) {
                        items[i].sellIn = items[i].sellIn - 1;
-                   }

                    if (items[i].sellIn < 0) {
-                       if (!items[i].name.equals("Aged Brie")) {
-                           if (!items[i].name.equals("Backstage passes to a TAFKAL80ETC concert")) {
                                if (items[i].quality > 0) {
-                                   if (!items[i].name.equals("Sulfuras, Hand of Ragnaros")) {
                                        items[i].quality = items[i].quality - 1;
-                                   }
-                               }
-                           }
                        }
                    }
            }
        }
    }
```

## Conclusion
Voici le code final de la méthode `updateQuality` après ce refactoring : 

```java
public void updateQuality() {  
    for (int i = 0; i < items.length; i++) {  
        switch (items[i].name) {  
            case "Aged Brie":  
                if (items[i].quality < 50) {  
                    items[i].quality = items[i].quality + 1;  
                }  
  
                items[i].sellIn = items[i].sellIn - 1;  
  
                if (items[i].sellIn < 0) {  
                    if (items[i].quality < 50) {  
                        items[i].quality = items[i].quality + 1;  
                    }  
                }  
  
                break;  
  
            case "Backstage passes to a TAFKAL80ETC concert":  
  
                if (items[i].quality < 50) {  
                    items[i].quality = items[i].quality + 1;  
  
  
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
  
                items[i].sellIn = items[i].sellIn - 1;  
  
                if (items[i].sellIn < 0) {  
                    items[i].quality = items[i].quality - items[i].quality;  
                }  
  
                break;  
            case "Sulfuras, Hand of Ragnaros":  
  
                break;  
            default:  
                if (items[i].quality > 0) {  
                    items[i].quality = items[i].quality - 1;  
                }  
  
                items[i].sellIn = items[i].sellIn - 1;  
  
                if (items[i].sellIn < 0) {  
                    if (items[i].quality > 0) {  
                        items[i].quality = items[i].quality - 1;  
                    }  
                }  
        }  
    }  
}
```

Bien qu'il y ait encore du refactoring à faire, on peut commencer à comparer les règles métiers avec le cahier des charges qui est fourni avec le projet. 

Il ne faut jamais hésiter à dupliquer le code pour pouvoir le réorganiser.

Et en utilisant astucieusement le code coverage, nous pouvons gagner beaucoup de temps lors du refactoring et nettoyage de code. Pour moi, c'est la principale utilité de cet outil.

Nous verrons dans une troisième (et dernière j'espère) partie la suite de ce refactoring.

## Reference
J'ai retrouvé d'autres utilisateurs de cette technique dans cet article : http://www.arolla.fr/blog/2019/11/man-vs-legacy-gilded-rose-2/