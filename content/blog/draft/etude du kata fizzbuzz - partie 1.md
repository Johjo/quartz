---
title: "etude du kata fizzbuzz - partie 1"
date: "2022-05-25T15:07:45+02:00"
draft: true
tags:
- étude-de-kata
---

```diff
+import org.junit.jupiter.api.Test;
+
+import static org.junit.jupiter.api.Assertions.assertEquals;
+
+public class TestFizzbuzz {
+    @Test
+    public void should_return_number_when_number_is_not_multiple_of_3_or_5() {
+        assertEquals("1", fizzbuzz(1));
+    }
+}
```

```bash
Expected :1
Actual   :null
```

```diff
public class TestFizzbuzz {
    @Test
    public void should_return_number_when_number_is_not_multiple_of_3_or_5() {
        assertEquals("1", fizzbuzz(1));
    }
+
+   private String fizzbuzz(int i) {
+       return null;
+   }
}

```

```diff
    private String fizzbuzz(int i) {
+       return "1";
-       return null;
    }

```

```diff
    @Test
+   public void should_return_number_when_number_is_not_multiple_of_3_or_5(int number, String expected) {
-   public void should_return_number_when_number_is_not_multiple_of_3_or_5() {
+       assertEquals(expected, fizzbuzz(number));
-       assertEquals("1", fizzbuzz(1));
    }
```


## Reference
