---
layout: post
title: Forcer le chiffrement de mots de passe
date: 2014-07-03 09:35:00
category: PostgreSQL
---

create extension pgcrypto;

create table users (
  id bigserial primary key,
  username text unique not null,
  password text not null
);

create function password_crypt() returns trigger
as $_$
DECLARE
BEGIN
  IF TG_OP = 'INSERT' OR TG_OP = 'UPDATE' THEN
    NEW.password := crypt(NEW.password, gen_salt('md5'));
  END IF;

  RETURN NEW;
END;
$_$ language plpgsql;

create trigger force_password_crypt before insert or update of password on users for each row execute procedure password_crypt();

crypto=# insert into users (username, password) values ('nico', 'test') returning *;
 id | username |              password              
----+----------+------------------------------------
  1 | nico     | $1$7b5/skvE$IKL7ALDhtM9sL9bAkwrKN0
(1 row)


create function authenticate(text, text) returns boolean
as $_$SELECT password = crypt($2, password) FROM users WHERE username = $1;$_$
language sql;

crypto=# select authenticate('nico', 'icule');
 authenticate 
--------------
 f
(1 row)

Time: 1.182 ms
crypto=# select authenticate('nico', 'test');
 authenticate 
--------------
 t
(1 row)

