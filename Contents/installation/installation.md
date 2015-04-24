# Installation {#te-manex-ref:99a9bb66-739f-4fa4-88f9-fe236a9a8e7c}

TE est disponible sous forme d'archive à installer sur un serveur dédié (ou non) qui assurera la fonction de serveur de transformation pour Dynacase.

## Télécharger et décompresser l'archive `dynacase-te-current.tar.gz` {#te-manex-ref:f4d41890-4764-4293-a159-e5f7a82f8851}

    # wget http://dynacase.anakeen.com/tengine/dynacase-te-current.tar.gz
    # tar zxvf dynacase-te-current.tar.gz

L'archive se décompresse et crée un sous-répertoire `dynacase-te-${VERSION}-${RELEASE}` avec le numéro de version et de release de dynacase-te.

Pour simplifier le nommage et l'accès à ce répertoire vous pouvez le renommer :

    # mv dynacase-te-${VERSION}-${RELEASE} dynacase-te

Par la suite, nous ferons référence à ce répertoire à l'aide de la variable `TE_HOME` :

    # TE_HOME=/opt/dynacase-te

<span class="flag inline nota-bene"></span> Si vous avez un compte EEC, il est recommandé de télécharger dynacase-te depuis votre dépôt EEC afin d'avoir les dernières corrections disponibles.

## Initialisation {#te-manex-ref:063a343a-7265-46ef-b63a-a80335750f79}

Une fois les éléments installés, il vous faut initialiser la base de données TE.

    # $TE_HOME/bin/ted init
     * Initializing ted service: OK

L'initialisation crée les tables `engine` et `task` dans la base TE.

## Montée en version et mise à jour {#te-manex-ref:a5e82934-975f-48de-8edc-6d2b7a35639a}

La mise à jour de TE consiste à :

* déployer le contenu de l'archive de la nouvelle version sur l'installation de
  l'ancienne version ;
    
    * télécharger la version courante (e.g. `http://eec.anakeen.com/public/tengine/dynacase-te-current.tar.gz` ;
    * décompresser le contenu de l'archive de la version courante (e.g. `dynacase-te-current.tar.gz`) dans le répertoire racine de votre installation de TE (e.g. `/opt/dynacase-te`) :
    
        # wget -O /tmp/dynacase-te-current.tar.gz http://eec.anakeen.com/public/tengine/dynacase-te-current.tar.gz
        # tar -C /opt/dynacase-te -zxvf /tmp/dynacase-te-current.tar.gz --strip-components 1

* suivre ensuite les éventuelles procédures de migration fournies dans les
  [notes de version][release_notes] ;

* pour finir, [redémarrer le daemon TE][ted_start_stop].

## Installation des éléments additionnels {#te-manex-ref:45e8dbf4-5110-451d-b6a9-53ed45893548}

Installer les composants logiciel requis par TE (voir [Pré-requis][pre-requis])
puis procéder à leur configuration (voir [Configuration du serveur TE][te_config]).

<span class="flag inline nota-bene"></span> Les versions à utiliser dépendent de la version du serveur TE. Elles sont identifiées
dans [les notes de version][release_notes].

### Installation de tika-server {#tika-server}

L'archive `tika-server-<version>.jar` peut être obtenue en téléchargement sur
le site officiel de [Apache Tika](http://tika.apache.org/download.html), ou
bien sur notre dépôt *third-party* :
[tika-server-1.7.jar](http://ftp.dynacase.org/third-party/tika-server-1.7.jar)

L'archive JAR de `tika-server` doit ensuite être déposée dans le
sous-répertoire `$TE_HOME/lib/engines` :

    [bash]
    # cp /tmp/tika-server-<version>.jar $TE_HOME/lib/engines/

<!-- links -->
[release_notes]: #te-manex-ref:da01abb1-163e-434f-b56c-eb918c479cb9
[ted_start_stop]: #te-manex-ref:560d0dfe-8b7e-4bba-8262-c847f38c1ef4
[pre-requis]: #te-manex-ref:a0146a31-9ab5-4dfe-978d-7a5d3322036d
[te_config]: #te-manex-ref:0dc32061-9f16-4941-b1f0-8d66d8f8f8bd
