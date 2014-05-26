---
layout: post
title: Recréer une séquence
date: 2011-02-22 22:30:00
category: PostgreSQL
---

On peut faire ça dans 2 cas :

-   On a fait n'importe quoi, et la séquence a disparu :-(
-   On veut transformer une colonne en « SERIAL »

{% highlight sql %}
SET ROLE owner_de_la_table;
SELECT max(id)+1 FROM latable;
--  ?column?
-- ----------
--       155
-- (1 row)

-- On prend donc « max(id)+1 » comme valeur de départ de la séquence
BEGIN;
CREATE SEQUENCE latable_id_seq START 155 OWNED BY latable.id;
COMMIT;
{% endhighlight %}

Pour lier la séquence à la table (et ainsi l'utiliser lors d'INSERT par
exemple) :

{% highlight sql %}
BEGIN;
ALTER TABLE schema.latable ALTER COLUMN id SET DEFAULT NEXTVAL('latable_id_seq'::regclass);
COMMIT;
{% endhighlight %}

