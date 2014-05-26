---
layout: post
title: pitrery - des scripts pour faciliter la sauvegarde PITR
date: 2011-08-04 17:35
category: PostgreSQL
---

Gérer une sauvegarde PITR (Point-In-Time Recovery), permettant de
revenir à un point précis dans le temps sur un serveur PostgreSQL, n'est
pas forcément évident à mettre en place et ensuite à gérer au
quotidien : la restauration en cas de problème peut même prendre
beaucoup de temps à mettre en place, avec des possibilités d'erreurs non
négligeables. C'est d'autant plus stressant que la pression monte quand
on en a besoin...

C'est pourquoi, dans le cadre de mon travail, j'ai écrit un ensemble de
scripts shell en Bash (ce qui permet d'utiliser des commandes qui sont
déjà sur une installation classique de Linux) pour faciliter ce travail.
Le code est hébergé sur github : [https://github.com/dalibo/pitrery][].
La version 1.0 vient d'être taguée. C'est encore un peu brut de
décoffrage niveau documentation, mais les scripts ont été testés dans
tous les sens, donc ce jeu d'outils devrait être utilisable en
production sans trop de difficultés.

Il y a un Makefile GNU, à l'ancienne, les fichiers de configuration sont
plein de commentaires pour expliquer et il y des « usage » pour chaque
script à base de `-?` ou `-h` qui devraient parler à ceux qui savent
déjà gérer du PITR.

</p>

[https://github.com/dalibo/pitrery]: https://github.com/dalibo/pitrery "https://github.com/dalibo/pitrery"
