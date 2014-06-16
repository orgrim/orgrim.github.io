---
layout: post
title: pgbench et .pgpass
date: 2014-06-16 10:51:44
category: PostgreSQL
---


En faisant quelques tests sur le Foreign Data Wrapper, j'ai (re)fait un peu de pgbench. Pour ceux qui ne savent pas pgbench permet de simuler un benchmark TPC-B, en gros des lectures, des écritures par un certain nombre de sessions concurrentes.

Même si on peut le considérer simpliste par rapport à un Tsung, il est fourni dans les contribs de PostgreSQL et permet de facilement tester un aspect qu'on néglige souvent : les effets de la concurrence sur une base.

J'ai donc monté mon petit environnement de test avec un rôle pour pgbench et sa base dédiée. Pour aller plus vite, j'en ai profité pour configurer un fichier `.pgpass`, sauf que pgbench n'en voulait pas :


    postgres@fdw:~$ cat .pgpass 
    localhost:5432:*:bench:Clair!
    
    postgres@fdw:~$ pgbench -h localhost -n -c 10 -j 10 -U bench bench
    Password: 
    Connection to database "bench" failed:
    fe_sendauth: no password supplied

Poutant, la ligne pour l'utilisateur `bench` est correcte. Si on lui fournit *explicitement* le port 5432, il tient compte de l'entrée dans le `.pgpass` :

    postgres@fdw:~$ pgbench -h localhost -n -c 10 -j 10 -U bench -p 5432 bench
    transaction type: TPC-B (sort of)
    scaling factor: 500
    query mode: simple
    number of clients: 10
    number of threads: 10
    number of transactions per client: 10
    number of transactions actually processed: 100/100
    tps = 77.993447 (including connections establishing)
    tps = 88.977249 (excluding connections establishing)

La lecture de ce [thread](http://www.postgresql.org/message-id/CA+Tgmob+q=6QRPXt8mYqiwb90SaHy+SMbsW7LrTZvoXuvKgvmg@mail.gmail.com), qui se conclut sur ce [patch](http://git.postgresql.org/gitweb/?p=postgresql.git;a=commitdiff;h=13ecb822e8da5668133b706474c25bc908ae370a), nous éclaire sur ce comportement.

Selon le message de commit, la modification change le comportement de la libpq. Elle n'a pas été backportée sur les autres branches et ne sera donc disponible que pour la libpq de la version 9.4 (ou Debian/Ubuntu qui prend toujours la dernière version de la libpq).
