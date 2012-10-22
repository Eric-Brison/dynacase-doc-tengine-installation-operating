*[TE]: Transformation Engine

# Installation

## Installation

TE est disponible sous forme d'archive à installer sur un serveur dédié (ou non) qui assurera la fonction de serveur de transformation pour Dynacase.

### Télécharger et décompresser l'archive `dynacase-te-current.tar.gz`

    # wget http://eec.anakeen.com/public/tengine/dynacase-te-current.tar.gz
    # tar zxvf dynacase-te-current.tar.gz

L'archive se décompresse et crée un sous-répertoire `dynacase-te-${VERSION}-${RELEASE}` avec le numéro de version et de release de dynacase-te.

Pour simplifier le nommage et l'accès à ce répertoire vous pouvez le renommer :

    # mv dynacase-te-${VERSION}-${RELEASE} dynacase-te

Par la suite, nous ferons référence à ce répertoire à l'aide de la variable `TE_HOME` :

    # TE_HOME=/opt/dynacase-te

__Note__ : Si vous avez un compte EEC, il est recommandé de télécharger dynacase-te depuis votre dépôt EEC afin d'avoir les dernières corrections disponibles.

### Création de la base de donnée TE

#### Créer une base `te` sur votre serveur de base de données

    # su postgres -c psql
    postgres=# CREATE DATABASE "te" WITH OWNER "te";

#### Créer/ajouter le service postgresql pour l'accès à cette base `te`

    # vi ${PGSYSCONFDIR}/pg_service.conf

     ...

    [te]
    host=127.0.0.1
    port=5432
    user=dynacaseowner
    password=password
    dbname=te

Note: La valeur de `${PGSYSCONFDIR}` est dépendante de votre distribution, et peut être trouvée avec la commande : `pg_config --sysconfdir`.

#### Valider l'accès à la base de donnée `te`

    # PGSERVICE=te psql
    te=# \q

#### Nombre maximum de connections à la base de donnée

Le service TE comprend deux processus qui tournent en continue (*te_request_server* et *te_request_renderer*). Chacun de ces processus ouvre et maintient une connexion ouverte sur la base de donnée.

Ensuite, à chaque fork (pour le traitement d'un client pour *te_request_server*, ou le traitement d'une tâche pour *te_request_renderer*) une nouvelle connexion est faite sur la base de données.

Le nombre maximum de connections à la base de données est donc donné par la formule :

    Nombre max de connections Postgresql = 2 + REQUEST_MAX_CLIENT + RENDERING_MAX_CLIENT

### Installation des éléments additionnels

#### tika-app {#tika-app}

L'archive `tika-app-0.9.jar` peut être obtenue en compilant les sources de [Apache Tika 0.9](http://tika.apache.org/download.html), ou bien sous forme pré-compilée sur notre dépôt *third-party* : [Tika](http://ftp.dynacase.org/third-party/tika-app-0.9.jar)

La compilation de `apache-tika-0.9-src.zip` avec maven (`mvn`) peut nécessiter l'augmentation des limites mémoire de la JVM :

    $ cd apache-tika-0.9
    $ MAVEN_OPTS=-Xmx2048m mvn -e clean install

L'archive JAR de `tika-app` doit ensuite être déposée dans le sous-répertoire `$TE_HOME/lib/engines` :

    # cp tika-app/target/tika-app-0.9.jar $TE_HOME/lib/engines/

## Configuration du serveur TE

Les paramètres du serveur TE sont définis dans le fichier `$TE_HOME/etc/te.conf`.

Lors de la première utilisation il vous faudra copier le fichier d'exemple `te.conf.sample` dans `te.conf` :

    # cp $TE_HOME/etc/te.conf.sample $TE_HOME/etc/te.conf

Les paramètres à ajuster en fonction de votre environnement sont :

  * __Répertoire temporaire de travail__

| Répertoire                   | Définition                                                                  |
| -----------                  | -----------                                                                 |
| REQUEST_DIRECTORY=/var/tmp   | Répertoire temporaire de travail du serveur request et des scripts engines. |
| RENDERING_DIRECTORY=/var/tmp | Répertoire temporaire de travail du serveur rendering.                      |

  * __Adresse et port d'écoute TCP__

| Config                 | Définition                     |
| ---                    | ---                            |
| LISTEN_ADDRESS=0.0.0.0 | Adresse d'écoute du serveur TE |
| PORT=51968             | Port d'écoute du serveur TE    |

  * __Accès base de donnée__

| Config           | Définition                                                |
| ---              | ---                                                       |
| TE_PG_SERVICE=te | Contient le le nom du service pour l'accès à la base "te" |

  * __Interaction avec Dynacase__

__Note__ : À partir de la version 0.8.2 la définition du login et du mot de passe ne sont plus obligatoires.

| Config                         | Définition                                                                                                                                                                             |
| ---                            | ---                                                                                                                                                                                    |
| URL_CALLBACK_LOGIN=te          | Login dans le cas ou le serveur Dynacase est derrière une authentification HTTP Basic non gérée par dynacase (frontal/reverse proxy HTTP avec authentification HTTP Basic par exemple) |
| URL_CALLBACK_PASSWORD=password | Le mot de passe associé au login URL_CALLBACK_LOGIN pour l'authentification HTTP Basic                                                                                                 |

  * __Lancer les serveurs sous une autre identité que root__

| Config              | Définition                                                              |
| ---                 | ---                                                                     |
| TE_SERVER_USER=root | L'utilisateur sous lequel lancer les serveurs de TE (*root* par défaut) |

__Note__ : Il est recommandé de ne pas lancer les serveurs de TE sous le compte *root* mais d'utiliser plutôt un compte utilisateur dédié.

  * __Paramétrage serveur OpenOffice.org__

| Config                                                                                                                                                                                                                                                     | Définition                                                                                                                                                   |
| ---                                                                                                                                                                                                                                                        | ---                                                                                                                                                          |
| TE_OOO_BASE_DIR=/opt/openoffice.org3                                                                                                                                                                                                                       | Le chemin d'accès au répertoire racine d'installation de OpenOffice.org                                                                                      |
| TE_OOO_SERVER_PYTHON=${TE_OOO_BASE_DIR}/program/python                                                                                                                                                                                                     | Le chemin d'accès à l'interpréteur Python de OpenOffice.org                                                                                                  |
| TE_OOO_SERVER_SOFFICE=${TE_OOO_BASE_DIR}/program/soffice                                                                                                                                                                                                   | Le chemin d'accès au programme *soffice* de OpenOffice.org                                                                                                   |
| TE_OOO_SERVER_UNOPKG=${TE_OOO_BASE_DIR}/program/unopkg                                                                                                                                                                                                     | Le chemin d'accès au programme *unopkg* de OpenOffice.org                                                                                                    |
| TE_OOO_CLASSPATH="${TE_OOO_BASE_DIR}/basis-link/program/classes/unoil.jar:${TE_OOO_BASE_DIR}/basis-link/ure-link/share/java/juh.jar:${TE_OOO_BASE_DIR}/basis-link/ure-link/share/java/jurt.jar:${TE_OOO_BASE_DIR}/basis-link/ure-link/share/java/ridl.jar" | Le classpath Java pour accéder aux librairies Java d'OpenOffice. Les classes nécessaires sont contenus dans `unoil.jar`, `juh.jar`, `jurt.jar` et `ridl.jar` |
| TE_OOO_JVM_OPTS=""                                                                                                                                                                                                                                         | Variable pour positionner des paramètres spécifiques pour la JVM si besoin                                                                                   |
| TE_OOO_SERVER_HOST=127.0.0.1                                                                                                                                                                                                                               | L'adresse IP d'écoute du serveur TE                                                                                                                          |
| TE_OOO_SERVER_PORT=8123                                                                                                                                                                                                                                    | Le port d'écoute du serveur TE                                                                                                                               |

  * __Paramétrage tika-app__

| Config                                                 | Définition                                           |
| ---                                                    | ---                                                  |
| TIKA_APP_JAR="${TE_HOME}/lib/engines/tika-app-0.9.jar" | Le chemin d'accès à l'archive `''tika-app-0.9.jar''' |

## Initialisation

Une fois les éléments installés, il vous faut initialiser la base de données TE.

    # $TE_HOME/bin/ted init
     * Initializing ted service: OK

L'initialisation crée les tables `engine` et `task` dans la base TE.

## Démarrage/arrêt/status de TE

### Démarrage des éléments de TE (`ted start`)

TE démarre trois services qui sont :

  * Le serveur OpenOffice.org
  * Le serveur te_request_server
  * Le serveur te_rendering_server

    # $TE_HOME/bin/ted start
    Starting OOO server... 27023
    Starting te_request_server... 27041
    Starting te_rendering_server... 27043
    Starting ted service:  OK


Le script ted s'occupe de lancer les trois composants, et affiche leur PID.

### Status des éléments de TE (`ted status`)

    # $TE_HOME/bin/ted status
    Request server running (27041)
    Rendering server running (27043)
    OOO server running (27023)

Le script `ted` affiche pour chacun des trois composants s'ils tournent ou non, et leur PID.

### Vérification des moteurs de transformation (`ted check`)

Le script `ted` permet de lancer une vérification des moteurs de transformations. Pour cela, il faut démarrer les composants (voir `ted start` ci-dessus), et ensuite exécuter la commande suivante :

    # $TE_HOME/bin/ted check

    * Checking conversion from ODT to PDF...
      Ok: '/tmp/test.odtn27155.pdf' (7957 bytes)

    * Checking conversion from ODT to PDF/A-1...
      Ok: '/tmp/test.odtQ27176.pdfa' (14430 bytes)

    * Checking conversion from ODT to TXT...
      Ok: '/tmp/test.odtu27199.txt' (22 bytes)

    * Checking conversion from ODT to MS-Word...
      Ok: '/tmp/test.odtSy4BuE.doc' (9216 bytes)

    * Checking conversion from HTML to ODT...
      Ok: '/tmp/test.htmlF27220.odt' (9067 bytes)

    * Checking conversion from HTML to PDF...
      Ok: '/tmp/test.htmlj27253.pdf' (8066 bytes)

    * Checking conversion from HTML to PDF/A-1...
      Ok: '/tmp/test.htmlW27300.pdfa' (14764 bytes)

    * Checking conversion from HTML to TXT...
      Ok: '/tmp/test.htmll27348.txt' (39 bytes)

    * Checking conversion from PDF to TXT...
      Ok: '/tmp/test.pdfq27356.txt' (22 bytes)

    * Checking conversion from TXT to PDF...
      Ok: '/tmp/test.txtK27363.pdf' (2559 bytes)


__Note__ : Si le nom d'hôte/nom de domaine du système est mal configuré, les temps de conversion peuvent être long du fait de timeouts de résolution de noms lors de la connexion au serveur OOo.
Pour corriger cela, assurez vous que le nom d'hôte (tel que retourné par la commande `hostname`) et le nom de domaine (tel que retourné par la commande `dnsdomainname`) sont corrects, et que le fichier `/etc/hosts` est correctement renseigné.

### Arrêts des éléments de TE (`ted stop`)

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

## Démarrage/arrêt automatique de TE avec le système

Pour que TE démarre, et s'arrête, lors du démarrage, et l'arrêt, du système, il faut enregistrer le script `ted` dans le système rc/init de votre système.

### Enregistrement de ted sur distribution de type RedHat

Les distribution de type RedHat utilisent la commande `chkconfig` pour administrer les scripts rc/init.

Faire un lien symbolique de `$TE_HOME/bin/ted` dans le répertoire `/etc/rc.d/init.d/` :

    # ln -sf $TE_HOME/bin/ted /etc/rc.d/init.d/ted

Enregistrer `ted` :

    # chkconfig --add ted
    # chkconfig ted on
    # chkconfig --list ted
    ted             0:arrêt 1:arrêt 2:marche        3:marche        4:marche        5:marche        6:arrêt

### Enregistrement de ted sur distribution de type Debian

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
