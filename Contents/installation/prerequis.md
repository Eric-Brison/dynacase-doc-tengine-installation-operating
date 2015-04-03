# Pré-requis  {#te-manex-ref:a0146a31-9ab5-4dfe-978d-7a5d3322036d}

## Logiciels {#te-manex-ref:657e8fad-93ff-4705-a32f-e9d038721082}

PostgreSQL &gt;= 9.x
:   Le TE utilise la base de donnée pour stocker les informations de gestion des travaux.
    Cette base de donnée peut être locale (sur le même serveur) ou distante.
	
PHP &gt;= 5.3 (cli)
:   TE est écrit en PHP et nécessite donc l'interpréteur [PHP](http://www.php.net/) (php-cli) avec les extensions suivantes :
    
        curl
        gettext
        json
        pcntl
        pgsql
        posix
        SimpleXML
        xml

Java Runtime Environment 6 (ou 7)
:   TE nécessite un environnement Java 6 (ou 7) pour utiliser l'API Java d'OpenOffice en mode serveur. Il est mis à disposition soit par [OpenJDK](http://openjdk.java.net/install/) ou par [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html).

OpenOffice (4.1.1) ou LibreOffice (4.4.0)
:   TE nécessite [Apache OpenOffice](http://www.openoffice.org) ou
    [LibreOffice](http://www.libreoffice.org). À la date de rédaction de ces
    pré-requis, les dernières versions disponibles de OpenOffice (version
    4.1.1) et LibreOffice (version 4.4.0) ont été testées et sont compatibles.
    
    Des différences peuvent exister dans le support et le rendu des documents
    entre OpenOffice et LibreOffice. Le choix de l'utilisation de l'un ou
    l'autre vous incombe donc en fonction de votre utilisation.

tika-server-1.7.jar
:   TE nécessite l'outil tika-server du projet
    [Apache Tika](http://tika.apache.org/) pour l'extraction de texte (voir
    [tika-server](#tika-server)). À la date de rédaction de ces pré-requis, la
    version actuellement disponible et préconisée est la `1.7`.

a2ps et ps2pdf14
:   TE utilise les outils `a2ps` et `ps2pdf14` pour les conversions texte vers PDF. Ils sont fournis par [a2ps](http://www.gnu.org/software/a2ps/) et [Ghostscript](http://pages.cs.wisc.edu/~ghost/).

recode
:   TE utilise la commande `recode` (fournie par [GNU recode](http://recode.progiciels-bpi.ca/index.html)).

perl
:   TE utilise la commande `perl` (fournie par [Perl](http://www.perl.org/)).

convert
:   TE utilise l'outil de conversion d'image `convert` (fourni par [ImageMagick](http://www.imagemagick.org/)).

zip et unzip
:   TE utilise les commandes `zip` et `unzip` (fournie par [Info-ZIP](http://www.info-zip.org/)).

Script `/lib/lsb/init-functions`
:   TE utilise le script `/lib/lsb/init-functions` présent sur les systèmes Linux compatibles "Linux Standard Base". Dans le cas où ce script ne serait pas présent, il est possible qu'un paquet fournisse cette fonctionnalité (`redhat-lsb` sur les distribution RedHat Enterprise Linux par exemple).


## Base de donnée TE {#te-manex-ref:f6506413-f567-4b5f-b964-510570653886}

Le serveur TE utilise une base de données pour stocker les travaux qu'il gère et les logs associés.  
Cette base peut être sur la machine hébergeant le serveur TE lui même ou sur un machine tierce.

### Créer une base `te` sur votre serveur de base de données {#te-manex-ref:517d6004-d7b9-4509-9396-e5e7ec5032a1}

    [bash]
    # su postgres -c psql
    postgres=# CREATE DATABASE "te" WITH OWNER "dynacaseowner";

### Créer/ajouter le service postgresql pour l'accès à cette base `te` {#te-manex-ref:04ea62d6-2ad8-4a84-b32e-6d66fb37bafc}

    [bash]
    # vi ${PGSYSCONFDIR}/pg_service.conf
    
    ...
    
    [te]
    host=127.0.0.1
    port=5432
    user=dynacaseowner
    password=password
    dbname=te

__Note__ : La valeur de `${PGSYSCONFDIR}` est dépendante de votre distribution, et peut être trouvée avec la commande : `pg_config --sysconfdir`.

### Valider l'accès à la base de donnée `te` {#te-manex-ref:476099d1-30f0-4eff-a057-539a10bdf009}

    [bash]
    # PGSERVICE=te psql
    te=# \q

### Paramétrage de la base de données `te` {#te-manex-ref:1b61dea4-8f6c-4185-a598-8f9dc8d3dc9c}

#### Nombre maximum de connections {#te-manex-ref:6f5b83ae-cecf-4b15-8b7b-4ace0960885b}

Le service TE comprend deux processus qui tournent en continue (*te_request_server* et *te_request_renderer*). Chacun de ces processus ouvre et maintient une connexion ouverte sur la base de donnée.

Ensuite, à chaque fork (pour le traitement d'un client pour *te_request_server*, ou le traitement d'une tâche pour *te_request_renderer*) une nouvelle connexion est faite sur la base de données.

Le nombre maximum de connections (`max_connections`) à la base de données est donc donné par la formule[^1] :

    Nombre max de connections Postgresql = 2 + REQUEST_MAX_CLIENT + RENDERING_MAX_CLIENT


[^1]: Les paramètres `REQUEST_MAX_CLIENT` et `RENDERING_MAX_CLIENT` sont modifiables lors de la [configuration du server](#te-manex-ref:0dc32061-9f16-4941-b1f0-8d66d8f8f8bd).

