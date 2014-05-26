---
layout: post
title: Installation de cgit
date: 2011-03-26 12:36
category: Unix
---

Cgit est un programme CGI écrit en C qui permet d'obtenir une interface
web de navigation pour un ensemble de dépôts git.

Cgit a besoin de OpenSSL, le paquet Debian est `libssl-dev`.

On récupère la version de stable de cgit :

    
    $ wget http://hjemli.net/git/cgit/snapshot/cgit-0.9.tar.bz2
    $ tar xjf cgit-0.9.tar.bz2
    

Il faut d'abord configurer l'installation en éditant le fichier
Makefile. L'interface a besoin d'un endroit accessible par un serveur
web, nous allons prendre `/var/www/orgrim.net/git`. On configure donc
les chemins dans le Makefile, de cette façon :

    
    CGIT_SCRIPT_PATH = /var/www/orgrim.net/git
    prefix = /usr/local/cgit
    

Les binaires iront donc dans `/var/www/orgrim.net/git/,` les
bibliothèques et autres fichiers dans une arborescence sous
`/usr/local/cgit`, le fichier de configuration, sera `/etc/cgitrc`, la
valeur par défaut.

Ensuite, il faut suivre les instructions données dans le README. On doit
récupérer le dépôt git de git :

    
    $ make get-git
    

Puis on compile et on installe :

    
    $ make
    $ sudo make install
    

On configure ensuite un virtual host Apache :

    
    $ cat /etc/apache2/sites-enabled/git.orgrim.net 
    <VirtualHost *:80>
      ServerAdmin webmaster@localhost
       ServerName git.orgrim.net
    
       DocumentRoot /var/www/orgrim.net/git
      <Directory /var/www/orgrim.net/git>
             Addhandler cgi-script .cgi
                DirectoryIndex cgit.cgi
           Options +FollowSymLinks +ExecCGI
          AllowOverride None
            Order allow,deny
          allow from all
    
              RewriteEngine On
              RewriteBase /
             RewriteCond %{REQUEST_FILENAME} !-f
               RewriteCond %{REQUEST_FILENAME} !-d
               RewriteRule (.*) cgit.cgi/$1
              RewriteRule ^cgit.cgi$  cgit.cgi/
     </Directory>
    
          ## Logs
       # Possible values include: debug, info, notice, warn, error, crit,
        # alert, emerg.
       LogLevel warn
    
       ErrorLog /var/log/apache2/orgrim.net/git_error.log
        CustomLog /var/log/apache2/orgrim.net/git_access.log combined
    
    </VirtualHost>
    

On peut aller voir le site, il doit afficher une page générée par cgit
montrant qu'aucun dépôt n'est configuré pour l'affichage.

Il faut maintenant créer le fichier de configuration, `/etc/cgitrc` en
recopiant la partie exemple du fichier source de la doc
`cgit-0.9/cgitrc.5.txt`.

Ici, l'important est de définir le paramètre de configuration pour
matcher avec la configuration des rewrite rules du virtual host :

    
    virtual-root=/
    

Enfin, les chemins vers la CSS et le logo sont à adapter, par rapport au
document root du virtual host :

    
    css=/cgit.css
    logo=/cgit.png
    

Le reste de la configuration est assez facile à adapter, pour pouvoir
ajouter ses dépôts.
