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

<span class="flag inline fixme">À compléter...</span>

## Installation des éléments additionnels {#te-manex-ref:45e8dbf4-5110-451d-b6a9-53ed45893548}

Ces composants sont utilisés par les moteurs de transformation livrés en standard.

<span class="flag inline nota-bene"></span> Les versions à utiliser dépendent de la version du serveur TE. Elles sont identifiées
dans [les notes de version](#te-manex-ref:da01abb1-163e-434f-b56c-eb918c479cb9).

### OpenOffice {#openoffice}

Installer OpenOffice à partir des paquets officiels [OpenOffice](http://www.openoffice.org/).

### tika-app {#tika-app}

L'archive `tika-app-<version>.jar` peut être obtenue en compilant les sources de [Apache Tika](http://tika.apache.org/download.html), ou bien sous forme pré-compilée sur notre dépôt *third-party* : [Tika](http://ftp.dynacase.org/third-party/tika-app-<version>.jar)

La compilation de `tika-<version>-src.zip` avec maven (`mvn`) peut nécessiter l'augmentation des limites mémoire de la JVM :

    [bash]
    $ cd tika-<version>
    $ MAVEN_OPTS=-Xmx2048m mvn -e clean install

L'archive JAR de `tika-app` doit ensuite être déposée dans le sous-répertoire `$TE_HOME/lib/engines` :

    [bash]
    # cp tika-app/target/tika-app-<version>.jar $TE_HOME/lib/engines/

