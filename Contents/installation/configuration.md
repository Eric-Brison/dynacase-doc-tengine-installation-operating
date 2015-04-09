# Configuration du serveur TE {#te-manex-ref:0dc32061-9f16-4941-b1f0-8d66d8f8f8bd}

## Initialisation {#te-manex-ref:5929b4d2-f7e3-4445-b4f9-fa34c211ed4e}

Les paramètres du serveur TE sont définis dans le fichier `$TE_HOME/etc/te.conf`.

Lors de la première utilisation, le fichier d'exemple `te.conf.sample` être
copié dans le fichier `te.conf` :

    # cp $TE_HOME/etc/te.conf.sample $TE_HOME/etc/te.conf


## Paramètres {#te-manex-ref:0260dfe8-d1c9-40c8-b2ba-666988ae4c09}

Le fichier de configuration est composé des paramètres suivants :


Base de données 
:  

    [bash]
    # Transformation Engine Configuration
    # - - - - - - - - - - - - - - - - - -
    TE_PG_SERVICE="te"                # Postgresql database service name

* `TE_PG_SERVICE` : permet d'indiquer le [service d'accès à la base de données](#te-manex-ref:f6506413-f567-4b5f-b964-510570653886) *TE*

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
* `REQUEST_MAX_CLIENT` : nombre maximum de connexions client simultanées
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
    TE_SERVER_DEBUG=no

* `TE_SERVER_USER` : permet de spécifier l'identité (unix user) sous laquelle les serveurs sont exécutés.
* `TE_SERVER_DEBUG` : permet de rediriger (`yes` ou `no`) la sortie standard
  (STDOUT) et d'erreur (STDERR) des serveurs te_request_server,
  te_rendering_server, OpenOffice et Tika dans syslog (pour analyser les
  éventuels problèmes de démarrage de ces services).

Serveur OpenOffice.org 
:   

    [bash]
    # -- Server-mode OpenOffice.org parameters
    TE_OOO_SERVER_ENABLED=yes
    TE_OOO_BASE_DIR=/replace/me/with/path/to/openoffice.org/directory
    TE_OOO_SERVER_SOFFICE=${TE_OOO_BASE_DIR}/program/soffice
    TE_OOO_CLASSPATH="${TE_OOO_BASE_DIR}/program/classes/unoil.jar:${TE_OOO_BASE_DIR}/program/classes/juh.jar:${TE_OOO_BASE_DIR}/program/classes/jurt.jar:${TE_OOO_BASE_DIR}/program/classes/ridl.jar"
    TE_OOO_JVM_OPTS=""
    TE_OOO_SERVER_HOST=127.0.0.1
    TE_OOO_SERVER_PORT=8123

Les chemin d'accès aux fichiers OpenOffice.org sont relatifs.  

Le paramètre principal `TE_OOO_BASE_DIR` est utilisé pour définir les paramètres
secondaires :

* `TE_OOO_SERVER_ENABLED` : permet d'activer (`yes`) ou désactiver (`no`) le
  lancement du serveur OpenOffice. Attention : cela ne désactive pas
  l'exécution des moteurs de conversion qui utilisent OpenOffice. Ces derniers
  seront alors mis en erreur.

* `TE_OOO_BASE_DIR` : chemin d'accès au répertoire racine d'installation de
  OpenOffice.

* `TE_OOO_SERVER_SOFFICE` : chemin d'accès au programme *soffice* de
  OpenOffice.

* `TE_OOO_CLASSPATH` :  _classpath_ Java pour accéder aux librairies Java
  d'OpenOffice. Les classes nécessaires sont contenus dans les fichiers java suivants :
  `unoil.jar`, `juh.jar`, `jurt.jar` et `ridl.jar`.
  
  La valeur de `TE_OOO_CLASSPATH` est différente suivant l'utilisation d'
  OpenOffice ou de LibreOffice :
  
  * Exemple de valeur pour OpenOffice :
    
        [bash]
        TE_OOO_CLASSPATH="${TE_OOO_BASE_DIR}/program/classes/unoil.jar:${TE_OOO_BASE_DIR}/program/classes/juh.jar:${TE_OOO_BASE_DIR}/program/classes/jurt.jar:${TE_OOO_BASE_DIR}/program/classes/ridl.jar"
  
  * Exemple de valeur pour LibreOffice :
    
        [bash]
        TE_OOO_CLASSPATH="${TE_OOO_BASE_DIR}/program/classes/unoil.jar:${TE_OOO_BASE_DIR}/ure/share/java/juh.jar:${TE_OOO_BASE_DIR}/ure/share/java/jurt.jar:${TE_OOO_BASE_DIR}/ure/share/java/ridl.jar"

  Sous debian (ou ubuntu), pour LibreOffice, les classes java sont fournies par 
  le paquet "`ure`" (LibreOffice UNO runtime environment).

* `TE_OOO_JVM_OPTS` : variable pour positionner des paramètres spécifiques pour
  la JVM si besoin.

* `TE_OOO_SERVER_HOST` : adresse IP d'écoute du serveur OpenOffice.

* `TE_OOO_SERVER_PORT` : port TCP d'écoute du serveur OpenOffice.

Serveur Tika
:   
  Ces variables dépendent de l'installation de [Tika server][tikaserver].

    [bash]
    # -- Tika-server Jar file
    TE_TIKA_SERVER_ENABLED=yes
    TE_TIKA_SERVER_JAR="/replace/me/with/path/to/tika-server-#version#.jar"
    TE_TIKA_SERVER_HOST=127.0.0.1
    TE_TIKA_SERVER_PORT=9998
    TE_TIKA_SERVER_LOGLEVEL="" # 'debug' or 'info'

* `TE_TIKA_SERVER_ENABLED` : permet d'activer (`yes`) ou désactiver (`no`) le
  lancement du serveur Tika. Attention : cela ne désactive pas l'exécution des
  moteurs de conversion qui utilisent Tika. Ces derniers seront alors mis en
  erreur.
* `TE_TIKA_SERVER_JAR` : chemin d'accès au fichier JAR de Tika Server.
* `TE_TIKA_SERVER_HOST` : adresse IP d'écoute du serveur Tika.
* `TE_TIKA_SERVER_PORT` : port TCP d'écoute du serveur Tika.
* `TE_TIKA_SERVER_LOGLEVEL` : loglevel spécifique du serveur Tika (à utiliser
  conjointement avec `TE_SERVER_DEBUG=yes` décrit ci-dessus).

## Type mimes  {#te-manex-ref:3e1d421e-9cb9-48f5-8c86-dd873a970eec}

La détection du type MIME textuel et du type MIME système des fichiers par TE
est paramétrable via des règles appliquées sur l'extension du nom du fichier.

Ces règles sont décrites au format XML dans le fichier
`$TE_HOME/etc/user-mime.conf`.

Un fichier d'exemple est fourni par défaut dans
`$TE_HOME/etc/user-mime.conf.sample`.

Exemple de définition des types MIME textuel et système pour les fichiers
d'extension `.foo` et `.bar` :

    [xml]
    <?xml version="1.0" encoding="utf-8"?>
    <mimes>
        <mime ext="foo" sys="application/foo" text="Foo file" />
        <mime ext="bar" sys="application/bar" text="Bar file" />
    </mimes>

Chaque règle est décrite à l'aide d'un élément `<mime/>` comportant l'extension
(sans le point de l'extension) sur laquelle elle s'applique (attribut `ext`) et
le type MIME textuel et système correspond qui est retourné (attribut `text` et
`sys`).

Les règles sont évalués dans l'ordre et s'arrête à la première règle qui
correspond à l'extension du fichier.

Ces règles `$TE_HOME/etc/user-mime.conf` viennent en complément et sont évalués
en priorité par rapport au jeu de règles fournit par défaut par TE (consultable
dans le fichier `$TE_HOME/etc/mime.conf`).

<!-- links -->

[tikaserver]:  #tika-server