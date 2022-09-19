---
title: "github - changer le token d'identification"
date: "2022-09-19T10:21:06+02:00"
tags:
- git
- github
---

Lorsque j'utilise github, je dois indiquer un token provisoire en guise de mot de passe. Mais je ne me souviens jamais comment récupérer ce token et où je dois le mettre pour que ça passe.

Pour récupérer le token, il faut aller dans les paramètres du compte (clic sur mon avatar puis *settings*) ensuite, il faut aller dans *Developer settings* et enfin dans *Personnal access tokens*.

Je clique sur *Generate new token* et en règle général, j'indique une date d'expiration et envin, je choisis le *scope* : *repo*.

Je valide et je copie le token qui est donné, parce que je ne pourrai pas le récupérer de nouveau.

Ensuite, pour l'utiliser avec git, qui est paramétré sur les *credentials windows*, je vais dans le *panneau de configuration* puis dans *Comptes d'utilisateurs* puis dans *Gestionnaire d'identification / Gérer les informations d'identification Windows*.

Je choisis mon identification github concernée et je modifie le mot de passe.

Et voilà !