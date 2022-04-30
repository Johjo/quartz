---
title: "Rust - je n'arrive pas à accéder à mon crate dans mes tests"
date: "2022-04-29T21:59:36+02:00"
draft: true
tags:
- rust
---

## Le problème

J'ai l'habitude d'écrire mon code de production dans les fichiers de tests au départ. Mais lorsque j'ai voulu déplacer ce code dans le répertoire `src`, les différents modules n'étaient plus accessible dans les fichiers de test.

## Que s'est-il passé ?

Tout simplement, j'ai créé mon projet pour que ce soit un binaire, et j'ai créé le fichier `lib.rs` un peu plus tard pour y mettre mon code.

Les derniers projets sur lesquels j'ai travaillé ne contenaient pas de fichier `main.rs` et tout fonctionnait. Mais lorsque les deux fichiers sont présents, rust ne sait pas ce qu'il faut faire.

## La solution
J'ai modifié le fichier `cargo.toml` pour y ajouter ce bout de code (sachant que je souhaite que mon binaire et mon crate s'appelle todolist) : 

```toml
[bin]  
name = "todolist"  
path = "src/main.rs"  
  
[lib]  
name = "todolist"  
path = "src/lib.rs"
```

Si jamais le module lib est un répertoire, on peut indiquer `src/lib/mod.rs` dans le `path`.

Grâce à cela, j'ai pu de nouveau exécuter mes tests.

## Reference
https://stackoverflow.com/questions/57756927/rust-modules-confusion-when-there-is-main-rs-and-lib-rs
