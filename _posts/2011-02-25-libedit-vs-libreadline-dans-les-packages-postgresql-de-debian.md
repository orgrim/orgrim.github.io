---
layout: post
title: libedit vs. libreadline dans les packages PostgreSQL de Debian
date: 2011-02-25 13:12
category: PostgreSQL
---

Pour éditer sa ligne de commande dans `psql`, on peut soit compiler
Postgres avec le support de readline, soit libedit. Pour Debian, les
questions de licences sont très importantes, et ils pensent qu'il y a
une incompatibilité entre la licence de libreadline (GPLv3) et celle de
Postgres (BSD), c'est pourquoi ils ont décidé de compiler Postres avec
la libedit. Malheureusement, la version de la libedit est vieille dans
Debian et surtout limitée : on ne peut taper que des caractères ASCII,
ce qui est une belle regression du point de vu du l'utilisation.

Il y avait deux solutions :

-   Ne taper que des caractères ASCII
-   Recompiler les packages Postgres avec le support de readline, c'est
    ce que j'ai fait.

Un workaround a été trouvé récemment, en ayant la libreadline installée,
on peut s'arranger pour l'utiliser grâce à un `LD_PRELOAD` bien placé.
C'est ce que fait `pg_wrapper` (le truc qui permet d'avoir plusieurs
versions de Postgres en même temps sous Debian) depuis la version 114
des packages `postgresql-common` et `postgresql-client-common`.

De plus, la priorité a été mise plus haute sur cette mise à jour, qui
corrige aussi d'autres problèmes, et cette version est déjà dans
testing.

Enfin, j'ai refais les backports que je maintiens pour fournir diverses
versions de Postgres sur Debian, non disponibles dans les dépôts
officiels : c'est sur le dépôt [http://debian.dalibo.org](http://debian.dalibo.org).

