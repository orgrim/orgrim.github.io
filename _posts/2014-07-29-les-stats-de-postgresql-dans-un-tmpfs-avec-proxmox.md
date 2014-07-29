---
layout: post
title: Les stats de PostgreSQL dans un tmpfs avec Proxmox
date: 2014-07-29 20:30:14
category: Unix
---


Au ${BOULOT}, on utilise Proxmox pour virtualiser, et surtout des
containers OpenVZ (pour les hypeux qui ne jurent que par lxc, c'était
et c'est pas encore si sec que ça lxc, faut beaucoup enrober, et on
n'a pas le temps). Les machines PostgreSQL se trouvent donc être des
containers OpenVZ.

Pour soulager les disques de certains I/O, des fois ça aide de placer
le répertoire de travail du stats collector, par défaut
`$PGDATA/pg_stat_tmp` et configurable par le GUC `stats_temp_directory` sur un ramdisk.

Sauf que dans un container, le guest ne peut pas manipuler ses
filesystems, on doit passer par l'hyperviseur. Dans le cas d'OpenVZ,
on peut ajouter un programme sous le nom `CTID.mount` qui permet de
monter, justement, un tmpfs. Ce fichier est à placer à coté du fichier de conf, dans `/etc/vz/conf`.

On decide alors de placer nos stats dans `/var/lib/postgresql/tmpfs`,
un tmpfs en mémoire. On ajoute ces répertoires pour garder la
hiérarchie classique des instances PostgreSQL sur Debian : `9.3/main/pg_stat_tmp`.

On écrit donc le script shell suivant dans `/etc/vz/conf/CTID.mount`, ou CTID est le numéro du container :

{% highlight bash %}
#!/bin/sh
# Script de montage d'un tmpfs pour les stats de PostgreSQL

# If one of these files does not exist then something
# is really broken
[ -f /etc/vz/vz.conf ] || exit 1
[ -f $VE_CONFFILE ] || exit 1
# Source both files. Note the order is important.
. /etc/vz/vz.conf
. $VE_CONFFILE

mountpoint=/var/lib/postgresql/tmpfs
subdir=9.3/main/pg_stat_tmp
postgres_uid=104
postgres_gid=108
size=104857600 # 100 Mo

mkdir -p ${VE_ROOT}${mountpoint}
mount -t tmpfs -o size=${size},uid=${postgres_uid},gid=${postgres_gid},mode=0700 tmpfs ${VE_ROOT}${mountpoint}
mkdir -p ${VE_ROOT}${mountpoint}/${subdir}
chown -R ${postgres_uid}.${postgres_gid} ${VE_ROOT}${mountpoint}

{% endhighlight %}

Dans le script tout ce fait dans le context de l'hyperviseur, c'est
pourquoi on utilise les uid/gid numériques de `postgres` *dans le
container* pour que les permissions soient correctes dès le boot.

Enfin, il ne reste qu'à modifier le fichier `postgresql.conf` pour configurer `stats_temp_directory` :

    stats_temp_directory = '/var/lib/postgresql/tmpfs/9.3/main/pg_stat_tmp'

On peut alors redémarrer le container sur l'hyperviseur :

    # vzctl restart CTID

Et voir depuis le container le nouveau FS :

    # df -h
    Filesystem      Size  Used Avail Use% Mounted on
    /dev/simfs      100G   28G   73G  28% /
    tmpfs           100M   12K  100M   1% /var/lib/postgresql/tmpfs
    tmpfs           410M   32K  410M   1% /run
    tmpfs           5.0M     0  5.0M   0% /run/lock
    tmpfs           820M     0  820M   0% /run/shm

