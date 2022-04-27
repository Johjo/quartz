---
title: "Spectral - petit piège lorsqu'on vérifie qu'un vecteur contient un str au lieu d'un string"
date: "2022-04-27T14:34:00+02:00"
tags:
- rust
- spectral
---

Lorsqu'on utilise spectral et que l'on veut vérifier qu'un `Vec` contient bien une `String`, on peut parfois aller trop vite et écrire le code suivant : 

```rust
#[cfg(test)]  
mod test {  
    use spectral::prelude::*;  
  
    #[test]  
    fn test_spectral_with_vec() {  
        let v = vec!["abc".to_string(), "def".to_string()];  
        assert_that!(v).contains("abc");  
    }  
}
```

Lorsqu'on execute le test, on obtient cette erreur, qui est assez indigeste.

```
error[E0277]: the trait bound `&str: Borrow<String>` is not satisfied
  --> src\main.rs:8:34
   |
8  |         assert_that!(v).contains("abc");
   |                         -------- ^^^^^ the trait `Borrow<String>` is not implemented for `&str`
   |                         |
   |                         required by a bound introduced by this call
   |
note: required by a bound in `spectral::prelude::ContainingIntoIterAssertions::contains`
  --> C:\Users\pasde\.cargo\registry\src\github.com-1ecc6299db9ec823\spectral-0.6.0\src\iter.rs:22:1
   |
22 | generate_iter_spec_trait!(ContainingIntoIterAssertions);
   | ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ required by this bound in `spectral::prelude::ContainingIntoIterAssertions::contains`
   = note: this error originates in the macro `generate_iter_spec_trait` (in Nightly builds, run with -Z macro-backtrace for more info)

```

La raison est toute simple, on recherche dans un `Vec<String>` une valeur de type `&str` ce qui n'est pas très compatible. Il faut bien penser à convertir la valeur recherchée en `String`.

Exemple de code qui fonctionne : 

```rust
#[cfg(test)]  
mod test {  
    use spectral::prelude::*;  
  
    #[test]  
    fn test_spectral_with_vec() {  
        let v = vec!["abc".to_string(), "def".to_string()];  
        assert_that!(v).contains("abc".to_string());  // convertir en string ici
    }  
}
```



