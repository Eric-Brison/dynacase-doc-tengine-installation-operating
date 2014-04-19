# Configuration du serveur TE {#te-manex-ref:0dc32061-9f16-4941-b1f0-8d66d8f8f8bd}

## Initialisation {#te-manex-ref:5929b4d2-f7e3-4445-b4f9-fa34c211ed4e}

Les paramètres du serveur TE sont définis dans le fichier `$TE_HOME/etc/te.conf`.

Lors de la première utilisation il vous faudra copier le fichier d'exemple `te.conf.sample` dans `te.conf` :

    # cp $TE_HOME/etc/te.conf.sample $TE_HOME/etc/te.conf


## Paramètres {#te-manex-ref:0260dfe8-d1c9-40c8-b2ba-666988ae4c09}

Vous trouverez ci-dessous le contenu du fichier de configuration (configuration par défaut) présenté par rubrique. Chacun des paramètres explicité.

Base de données 
:  

    [bash]
    # Transformation Engine Configuration
    # - - - - - - - - - - - - - - - - - -
    TE_PG_SERVICE="te"                # Postgresql database service name

* `TE_PG_SERVICE` permet d'indiquer le [service d'accès à la base de données](#te-manex-ref:f6506413-f567-4b5f-b964-510570653886) *TE*

Serveur de communication 
:  

    [bash]
    #---- REQUEST PART ----
    PORT=51968                        # port number where listen client
    LISTEN_ADDRESS=0.0.0.0            # address mask to listen : listen everybody by default
    REQUEST_MAX_CLIENT=15             # max request in parallel 
    REQUEST_DIRECTORY=/var/tmp        # directory where source files to transform are saved

* `PORT` : port d'écoute sur serveur
* `LISTEN_ADDRESS` : plage d'adresse (mask) d'écoute du serveur
* `REQUEST_MAX_CLIENT` : nombre maximun de connexions client simultanées
* `REQUEST_DIRECTORY` : répertoire de stockage des fichiers reçus

Mécanisme de purge 
:  

    [bash]
    PURGE_DAYS=7                      # remove tasks older than 7 days
    PURGE_INTERVAL=100                # trigger tasks purge every 100 requests (set to 0 to disable purge)

Ces paramètres permettent de définir le fonctionnement de la purge (suppression des travaux terminés).

* `PURGE_DAYS` : les travaux antérieurs à ce nombre de jours sont purgés
* `PURGE_INTERVAL` : précise la fréquence de la purge, la purge est lancée toute les _n_ requêtes reçues par le serveur

Serveur de transformation 
:  

    [bash]
    #---- RENDERING PART ----
    RENDERING_MAX_CLIENT=10           # max conversion in parallel 
    RENDERING_DIRECTORY=/var/tmp      # directory where converted files are saved

* `RENDERING_MAX_CLIENT` Nombre de moteurs de transformation activé en parallèle 
* `RENDERING_DIRECTORY`  Répertoire de stockage des fichiers produits

Identité pour les serveurs 
:  

    [bash]
    # -- Run TE servers as a user --
    TE_SERVER_USER=root

* `TE_SERVER_USER` : permet de spécifier l'identité (unix user) sous laquelle les serveurs sont exécutés.

Serveur OpenOffice.org 
:   

    [bash]
    # -- Server-mode OpenOffice.org parameters
    TE_OOO_SERVER_ENABLED=yes
    TE_OOO_BASE_DIR=/replace/me/with/path/to/openoffice.org/directory
    TE_OOO_SERVER_PYTHON=${TE_OOO_BASE_DIR}/program/python
    TE_OOO_SERVER_SOFFICE=${TE_OOO_BASE_DIR}/program/soffice
    TE_OOO_SERVER_UNOPKG=${TE_OOO_BASE_DIR}/program/unopkg
    TE_OOO_CLASSPATH="${TE_OOO_BASE_DIR}/program/classes/unoil.jar:${TE_OOO_BASE_DIR}/program/classes/juh.jar:${TE_OOO_BASE_DIR}/program/classes/jurt.jar:${TE_OOO_BASE_DIR}/program/classes/ridl.jar"
    TE_OOO_JVM_OPTS=""
    TE_OOO_SERVER_HOST=127.0.0.1
    TE_OOO_SERVER_PORT=8123

Les chemin d'accès aux fichiers OpenOffice.org sont relatifs.  
Normalement vous ne devez que vous assurer que `TE_OOO_BASE_DIR` est correctement valué.  

* `TE_OOO_BASE_DIR` : chemin d'accès au répertoire racine d'installation de OpenOffice
* `TE_OOO_SERVER_PYTHON` : chemin d'accès à l'interpréteur Python de OpenOffice
* `TE_OOO_SERVER_SOFFICE` : chemin d'accès au programme *soffice* de OpenOffice
* `TE_OOO_SERVER_UNOPKG` : chemin d'accès au programme *unopkg* de OpenOffice
* `TE_OOO_CLASSPATH` :  _classpath_ Java pour accéder aux librairies Java d'OpenOffice. Les classes nécessaires sont contenus dans `unoil.jar`, `juh.jar`, `jurt.jar` et `ridl.jar`
* `TE_OOO_JVM_OPTS` : variable pour positionner des paramètres spécifiques pour la JVM si besoin
* `TE_OOO_SERVER_HOST` : adresse IP d'écoute du serveur TE
* `TE_OOO_SERVER_PORT` : port d'écoute du serveur TE


Serveur Tika
:   

    [bash]
    # -- Tika-app Jar file
    TIKA_APP_JAR="/replace/me/with/path/to/tika-app-#version#.jar"

* `TIKA_APP_JAR` fournit le chemin d'accès au fichier JAR Tika


# Type mimes  {#te-manex-ref:3e1d421e-9cb9-48f5-8c86-dd873a970eec}
