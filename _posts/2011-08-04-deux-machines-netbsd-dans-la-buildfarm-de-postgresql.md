---
layout: post
title: Deux machines NetBSD dans la buildfarm de PostgreSQL
date: 2011-08-04 21:09
category: PostgreSQL
---

Il y a quelques temps, j'avais remarqué que seules deux machines NetBSD
étaient présentes dans la [buildfarm de PostgreSQL][] sur du powerpc et
du mips. Pas de machines i386 et amd64, qui sont pourtant les
architectures phares du [TIER 1][], et ce depuis plus d'un an.

L'affront est désormais réparé et deux nouveaux drapeaux oranges
flottent fièrement dans les prés de la buildfarm, fournies par votre
serviteur :

-   [panther][] : NetBSD 5.1 gcc 4.1.3 i386
-   [smilodon][] : NetBSD 5.1 gcc 4.1.3 amd64

Les machines tournent maintenant depuis quelques temps, sans aucun
soucis.

[buildfarm de PostgreSQL]: http://www.pgbuildfarm.org
[TIER 1]: http://www.netbsd.org/ports/
[panther]: http://www.pgbuildfarm.org/cgi-bin/show_status.pl?member=panther
[smilodon]: http://www.pgbuildfarm.org/cgi-bin/show_status.pl?member=smilodon
