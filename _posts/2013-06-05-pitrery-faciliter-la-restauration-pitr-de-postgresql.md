---
layout: post
title: pitrery - Faciliter la restauration PITR de PostgreSQL
date: 2013-06-05 08:38
category: PostgreSQL
---

pitrery est un ensemble de scripts pour gérer plus facilement la
sauvegarde à chaud et la restauration PITR dans PostgreSQL.

Il s'agit de simples scripts bash, qui n'imposent pas autant de
dépendances qu'un pgbarman et se concentre uniquement sur le PITR, là où
des outils tels que OmniPITR, PITRTools semblent vouloir gérer également
la réplication. L'objectif de pitrery est de pouvoir facilement
restaurer.

La version 1.3 apporte des nouveauté intéressantes :

-   Support de PostgreSQL 9.2, les changements de définition du
    catalogue en 9.2 sont pris en compte pour les tablespaces
-   Simplification du script d'archivage
-   Possibilité de configurer un utilisateur de connexion différent pour
    les accès SSH
-   Amélioration de l'affichage de la liste des sauvegardes
-   Ajout d'un résumé des informations essentielles pour la restauration
-   Possibilité de modifier l'emplacement de chaque tablespace à la
    restauration
-   Mode sans exécution de la restauration avec affichage des
    informations de restauration seulement
-   Beaucoup de corrections de bug

La documentation est à jour sur [https://dalibo.github.io/pitrery/documentation.html]

Le code est disponible sur [https://github.com/dalibo/pitrery/]

Les versions sont disponibles sur [https://dl.dalibo.com/public/pitrery/]

Les idées pour la prochaine version sont, en vrac :

-   Faire le base backup avec rsync, pour pallier la production de
    tarball énormes
-   Paralléliser le tar des tablespaces
-   Ajouter l'exécution de commandes pré/post backup
-   Améliorer l'algo de rétention des backups pour gérer age et nombre
    de backup en même temps


[https://dalibo.github.io/pitrery/documentation.html]: https://dalibo.github.io/pitrery/documentation.html
[https://github.com/dalibo/pitrery/]: https://github.com/dalibo/pitrery/
[https://dl.dalibo.com/public/pitrery/]: https://dl.dalibo.com/public/pitrery/
