---
title: "etude du kata gilded rose - switch"
date: "2022-05-06T16:32:50+02:00"
draft: true
tags:
- étude-de-kata
- refactoring
- golden-master
---

Dans cette étude de kata, nous allons étudier [[wiki/gilded rose|gilded rose]].



```diff
    @Test
     void foo() {
+        StringBuilder golden_master = new StringBuilder();
+
         Item[] items = new Item[] { new Item("foo", 0, 0) };
         GildedRose app = new GildedRose(items);
         app.updateQuality();
-        assertEquals("fixme", app.items[0].name);
+
+        golden_master.append(items[0].toString());
+
+        Approvals.verify(golden_master.toString());
+
     }

 }

```


```diff
     void foo() {
         StringBuilder golden_master = new StringBuilder();


+        for (int quality = -50; quality < 100; quality++) {
+            Item[] items = new Item[] { new Item("foo", 0, quality) };
-            Item[] items = new Item[] { new Item("foo", 0, 0) };
             GildedRose app = new GildedRose(items);
             app.updateQuality();
 
			 golden_master.append(items[0].toString());
+        }

         Approvals.verify(golden_master.toString());
}
```

```diff

     void foo() {
         StringBuilder golden_master = new StringBuilder();


        for (int quality = -50; quality < 100; quality++) {
            Item[] items = new Item[] { new Item("foo", 0, quality) };
             GildedRose app = new GildedRose(items);
             app.updateQuality();
 
-            golden_master.append(items[0].toString());
+            golden_master.append(String.format("%s : sell_in : %d, quality : %d\n", items[0].name, items[0].sellIn, items[0].quality));
        }

         Approvals.verify(golden_master.toString());

}
```



57 % de coverage
```diff

     void foo() {
         StringBuilder golden_master = new StringBuilder();

+       for (int sellIn = -20; sellIn < 50; sellIn++) {
            for (int quality = -50; quality < 100; quality++) {
+               Item[] items = new Item[] { new Item("foo", sellIn, quality) };
-               Item[] items = new Item[] { new Item("foo", 0, quality) };
                GildedRose app = new GildedRose(items);
                app.updateQuality();
 
                golden_master.append(String.format("%s : sell_in : %d, quality : %d\n", items[0].name, items[0].sellIn, items[0].quality));
            }
+       }

         Approvals.verify(golden_master.toString());

}
```




100 %
```diff

    void foo() {
        StringBuilder golden_master = new StringBuilder();
+       String names[] = new String[] { "Foo", "Aged Brie", "Sulfuras, Hand of Ragnaros", "Backstage passes to a TAFKAL80ETC concert" };
+       for (String name: names) {

            for (int sellIn = -20; sellIn < 50; sellIn++) {
                for (int quality = -50; quality < 100; quality++) {
+                   Item[] items = new Item[] { new Item(name, sellIn, quality) };
-                   Item[] items = new Item[] { new Item("foo", sellIn, quality) };
                    GildedRose app = new GildedRose(items);
                    app.updateQuality();
 
                    golden_master.append(String.format("%s : sell_in : %d, quality : %d\n", items[0].name, items[0].sellIn, items[0].quality));
                }
            }
+       }
         Approvals.verify(golden_master.toString());
}
```


## Refactoring

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

```diff
public void updateQuality() {  
    for (int i = 0; i < items.length; i++) {  
+       switch (items[i].name) {  
+           default:  
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
+       }  
  
    }  
}
```

```diff
public void updateQuality() {  
    for (int i = 0; i < items.length; i++) {  
        switch (items[i].name) {  
            case "Aged Brie":  
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
                        if (items[i].quality < 50) {  
                            items[i].quality = items[i].quality + 1;  
                        }  
                    }  
                }  
        }  
  
    }  
}

```


## Reference
