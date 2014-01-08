# Installation {#te-manex-ref:f25a1271-aab0-4b4a-83b1-ce10fbd462f6}

## Installation {#te-manex-ref:99a9bb66-739f-4fa4-88f9-fe236a9a8e7c}

TE est disponible sous forme d'archive à installer sur un serveur dédié (ou non) qui assurera la fonction de serveur de transformation pour Dynacase.

### Télécharger et décompresser l'archive `dynacase-te-current.tar.gz` {#te-manex-ref:f4d41890-4764-4293-a159-e5f7a82f8851}

    # wget http://dynacase.anakeen.com/tengine/dynacase-te-current.tar.gz
    # tar zxvf dynacase-te-current.tar.gz

L'archive se décompresse et crée un sous-répertoire `dynacase-te-${VERSION}-${RELEASE}` avec le numéro de version et de release de dynacase-te.

Pour simplifier le nommage et l'accès à ce répertoire vous pouvez le renommer :

    # mv dynacase-te-${VERSION}-${RELEASE} dynacase-te

Par la suite, nous ferons référence à ce répertoire à l'aide de la variable `TE_HOME` :

    # TE_HOME=/opt/dynacase-te

__Note__ : Si vous avez un compte EEC, il est recommandé de télécharger dynacase-te depuis votre dépôt EEC afin d'avoir les dernières corrections disponibles.

### Création de la base de donnée TE {#te-manex-ref:f6506413-f567-4b5f-b964-510570653886}

#### Créer une base `te` sur votre serveur de base de données {#te-manex-ref:517d6004-d7b9-4509-9396-e5e7ec5032a1}

    # su postgres -c psql
    postgres=# CREATE DATABASE "te" WITH OWNER "dynacaseowner";

#### Créer/ajouter le service postgresql pour l'accès à cette base `te` {#te-manex-ref:04ea62d6-2ad8-4a84-b32e-6d66fb37bafc}

    # vi ${PGSYSCONFDIR}/pg_service.conf
    
    ...
    
    [te]
    host=127.0.0.1
    port=5432
    user=dynacaseowner
    password=password
    dbname=te

__Note__ : La valeur de `${PGSYSCONFDIR}` est dépendante de votre distribution, et peut être trouvée avec la commande : `pg_config --sysconfdir`.

#### Valider l'accès à la base de donnée `te` {#te-manex-ref:476099d1-30f0-4eff-a057-539a10bdf009}

    # PGSERVICE=te psql
    te=# \q

#### Nombre maximum de connections à la base de donnée {#te-manex-ref:6f5b83ae-cecf-4b15-8b7b-4ace0960885b}

Le service TE comprend deux processus qui tournent en continue (*te_request_server* et *te_request_renderer*). Chacun de ces processus ouvre et maintient une connexion ouverte sur la base de donnée.

Ensuite, à chaque fork (pour le traitement d'un client pour *te_request_server*, ou le traitement d'une tâche pour *te_request_renderer*) une nouvelle connexion est faite sur la base de données.

Le nombre maximum de connections à la base de données est donc donné par la formule :

    Nombre max de connections Postgresql = 2 + REQUEST_MAX_CLIENT + RENDERING_MAX_CLIENT

### Installation des éléments additionnels {#te-manex-ref:45e8dbf4-5110-451d-b6a9-53ed45893548}

#### OpenOffice {#openoffice}

Installer OpenOffice 4.0 à partir des paquets officiels [OpenOffice](http://www.openoffice.org/).

#### tika-app {#tika-app}

L'archive `tika-app-1.4.jar` peut être obtenue en compilant les sources de [Apache Tika 1.4](http://tika.apache.org/download.html), ou bien sous forme pré-compilée sur notre dépôt *third-party* : [Tika](http://ftp.dynacase.org/third-party/tika-app-1.4.jar)

La compilation de `tika-1.4-src.zip` avec maven (`mvn`) peut nécessiter l'augmentation des limites mémoire de la JVM :

    $ cd tika-1.4
    $ MAVEN_OPTS=-Xmx2048m mvn -e clean install

L'archive JAR de `tika-app` doit ensuite être déposée dans le sous-répertoire `$TE_HOME/lib/engines` :

    # cp tika-app/target/tika-app-1.4.jar $TE_HOME/lib/engines/

## Configuration du serveur TE {#te-manex-ref:0dc32061-9f16-4941-b1f0-8d66d8f8f8bd}

Les paramètres du serveur TE sont définis dans le fichier `$TE_HOME/etc/te.conf`.

Lors de la première utilisation il vous faudra copier le fichier d'exemple `te.conf.sample` dans `te.conf` :

    # cp $TE_HOME/etc/te.conf.sample $TE_HOME/etc/te.conf

Les paramètres à ajuster en fonction de votre environnement sont :

### Répertoire temporaire de travail {#te-manex-ref:d244193e-f971-49db-80c8-0bcaf7ea9ded}

`REQUEST_DIRECTORY=/var/tmp`
:   Répertoire temporaire de travail du serveur request et des scripts engines.

`RENDERING_DIRECTORY=/var/tmp`
:   Répertoire temporaire de travail du serveur rendering.

### Adresse et port d'écoute TCP {#te-manex-ref:ad887351-30fb-4e76-8ee0-c517e107e5b0}

`LISTEN_ADDRESS=0.0.0.0`
:   Adresse d'écoute du serveur TE

`PORT=51968`
:   Port d'écoute du serveur TE

### Accès base de donnée {#te-manex-ref:75750be0-44e8-43de-ae26-b1086e57d6e2}

`TE_PG_SERVICE=te`
:   Contient le le nom du service pour l'accès à la base "te"

### Lancer les serveurs sous une autre identité que root {#te-manex-ref:0260dfe8-d1c9-40c8-b2ba-666988ae4c09}

`TE_SERVER_USER=root`
:   L'utilisateur sous lequel lancer les serveurs de TE (*root* par défaut)

__Note__ : Il est recommandé de ne pas lancer les serveurs de TE sous le compte *root* mais d'utiliser plutôt un compte utilisateur dédié.

### Paramétrage serveur OpenOffice {#conf-ooo}

`TE_OOO_BASE_DIR=/opt/openoffice4`
:   Le chemin d'accès au répertoire racine d'installation de OpenOffice

`TE_OOO_SERVER_PYTHON=${TE_OOO_BASE_DIR}/program/python`
:   Le chemin d'accès à l'interpréteur Python de OpenOffice

`TE_OOO_SERVER_SOFFICE=${TE_OOO_BASE_DIR}/program/soffice`
:   Le chemin d'accès au programme *soffice* de OpenOffice

`TE_OOO_SERVER_UNOPKG=${TE_OOO_BASE_DIR}/program/unopkg`
:   Le chemin d'accès au programme *unopkg* de OpenOffice

`TE_OOO_CLASSPATH="${TE_OOO_BASE_DIR}/program/classes/unoil.jar:${TE_OOO_BASE_DIR}/program/classes/juh.jar:${TE_OOO_BASE_DIR}/program/classes/jurt.jar:${TE_OOO_BASE_DIR}/program/classes/ridl.jar"`
:   Le classpath Java pour accéder aux librairies Java d'OpenOffice. Les classes nécessaires sont contenus dans `unoil.jar`, `juh.jar`, `jurt.jar` et `ridl.jar`

`TE_OOO_JVM_OPTS=""`
:   Variable pour positionner des paramètres spécifiques pour la JVM si besoin

`TE_OOO_SERVER_HOST=127.0.0.1`
:   L'adresse IP d'écoute du serveur TE

`TE_OOO_SERVER_PORT=8123`
:   Le port d'écoute du serveur TE

### Paramétrage tika-app {#conf-tika-app}

`TIKA_APP_JAR="${TE_HOME}/lib/engines/tika-app-1.4.jar"`
:   Le chemin d'accès à l'archive `tika-app-1.4.jar`

## Initialisation {#te-manex-ref:063a343a-7265-46ef-b63a-a80335750f79}

Une fois les éléments installés, il vous faut initialiser la base de données TE.

    # $TE_HOME/bin/ted init
     * Initializing ted service: OK

L'initialisation crée les tables `engine` et `task` dans la base TE.

## Démarrage/arrêt/status de TE {#te-manex-ref:218e260b-7cfa-4d53-a6c1-cb4c50f36f84}

### Démarrage des éléments de TE (`ted start`) {#te-manex-ref:560d0dfe-8b7e-4bba-8262-c847f38c1ef4}

TE démarre trois services qui sont :

  * Le serveur OpenOffice
  * Le serveur te_request_server
  * Le serveur te_rendering_server

    # $TE_HOME/bin/ted start
    Starting OOO server... 27023
    Starting te_request_server... 27041
    Starting te_rendering_server... 27043
    Starting ted service:  OK


Le script ted s'occupe de lancer les trois composants, et affiche leur PID.

### Status des éléments de TE (`ted status`) {#te-manex-ref:8e25828d-711c-4162-ad5e-7b0fcc78ca4b}

    # $TE_HOME/bin/ted status
    Request server running (27041)
    Rendering server running (27043)
    OOO server running (27023)

Le script `ted` affiche pour chacun des trois composants s'ils tournent ou non, et leur PID.

### Vérification des moteurs de transformation (`ted check`) {#te-manex-ref:02b9cb27-aca7-4232-8ba5-0455fa43709d}

Le script `ted` permet de lancer une vérification des moteurs de transformations. Pour cela, il faut démarrer les composants (voir `ted start` ci-dessus), et ensuite exécuter la commande suivante :

    # $TE_HOME/bin/ted check
    
    * Checking conversion from ODT to PDF...
      Ok: '/tmp/test.odtn27155.pdf' (7957 bytes)
    
    * Checking conversion from ODT to PDF/A-1...
      Ok: '/tmp/test.odtQ27176.pdfa' (14430 bytes)
    
    * Checking conversion from ODT to TXT...
      Ok: '/tmp/test.odtu27199.txt' (22 bytes)
    
    [etc.]


__Note__ : Si le nom d'hôte/nom de domaine du système est mal configuré, les temps de conversion peuvent être long du fait de timeouts de résolution de noms lors de la connexion au serveur OOo.
Pour corriger cela, assurez vous que le nom d'hôte (tel que retourné par la commande `hostname`) et le nom de domaine (tel que retourné par la commande `dnsdomainname`) sont corrects, et que le fichier `/etc/hosts` est correctement renseigné.

### Arrêts des éléments de TE (`ted stop`) {#te-manex-ref:fe36c94a-5fbc-4cc0-8fca-ea3d7e4886a5}

    # $TE_HOME/bin/ted stop
    Stopping te_request_server... 27041
    Stopping te_rendrering_server... 27043
    Stopping OOO server... 27023
     * Stopping ted service:  OK

### Nettoyage des fichiers temporaires (`ted cleantmpfiles`) {#cleantmpfiles}

L'option `cleantmpfiles` permet de supprimer les fichiers temporaires nommés `tes-*` et `ter-*`, présents dans les répertoire `$REQUEST_DIRECTORY` et `$RENDERING_DIRECTORY` qui ont plus de 7 jours (valeur par défaut).

    # $TE_HOME/bin/ted cleantmpfiles

Si vous voulez spécifier votre propre durée, vous pouvez ajouter le nombre de jours après l'option `cleantmpfiles`.

Exemple pour supprimer les fichiers temporaires de plus de 15 jours :

    # $TE_HOME/bin/ted cleantmpfiles 15

Pour effectuer le nettoyage régulièrement, vous pouvez exécuter cette commande à partir d'une crontab.

## Démarrage/arrêt automatique de TE avec le système {#te-manex-ref:c0a71f5b-d7e6-466d-b109-3e78ab407ebc}

Pour que TE démarre, et s'arrête, lors du démarrage, et l'arrêt, du système, il faut enregistrer le script `ted` dans le système rc/init de votre système.

### Enregistrement de ted sur distribution de type RedHat {#te-manex-ref:0489268c-97cc-4d0d-a6a9-519bf739e9f1}

Les distribution de type RedHat utilisent la commande `chkconfig` pour administrer les scripts rc/init.

Faire un lien symbolique de `$TE_HOME/bin/ted` dans le répertoire `/etc/rc.d/init.d/` :

    # ln -sf $TE_HOME/bin/ted /etc/rc.d/init.d/ted

Enregistrer `ted` :

    # chkconfig --add ted
    # chkconfig ted on
    # chkconfig --list ted
    ted             0:arrêt 1:arrêt 2:marche        3:marche        4:marche        5:marche        6:arrêt

### Enregistrement de ted sur distribution de type Debian {#te-manex-ref:7cfbb5f8-2f04-4ac9-8f96-5c5e741b5f07}

Les distributions de type Debian utilisent la commande `update-rc.d` pour administrer les scripts rc/init.

Faire un lien symbolique de `$TE_HOME/bin/ted` dans le répertoire `/etc/init.d/` :

    # ln -sf $TE_HOME/bin/ted /etc/init.d/ted

Enregistrer `ted` :

    # update-rc.d ted defaults
    Adding system startup for /etc/init.d/ted ...
     /etc/rc0.d/K20ted -> ../init.d/ted
     /etc/rc1.d/K20ted -> ../init.d/ted
     /etc/rc6.d/K20ted -> ../init.d/ted
     /etc/rc2.d/S20ted -> ../init.d/ted
     /etc/rc3.d/S20ted -> ../init.d/ted
     /etc/rc4.d/S20ted -> ../init.d/ted
     /etc/rc5.d/S20ted -> ../init.d/ted
