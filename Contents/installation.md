
# Installation

## Pré-requis

  * __PHP 5.x (cli) et extension `pgsql`__

`dynacase-te` est écrit en PHP et nécessite donc l'interpréteur [PHP](http://www.php.net/) (php-cli) et l'extension `pgsql`.

  * __Java Runtime Environment 6__

`dynacase-te` nécessite un environnement Java 6 pour utiliser l'API Java d'OpenOffice en mode serveur (http://openjdk.java.net/install/, http://www.oracle.com/technetwork/java/javase/downloads/index.html).

  * __OpenOffice.org 3.2.1__

`dynacase-te` est préconfiguré pour fonctionner avec OpenOffice.org 3.2.1 tels que livré par les paquets du site officiel [OpenOffice.org](http://www.openoffice.org) (installé par défaut dans `/opt/openoffice.org3`).

  * __`tika-app-0.9.jar`__

`dynacase-te` utilise l'outils `tika-app`, du projet [Apache Tika](http://tika.apache.org/), pour l'extraction de texte (voir [tika-app](#tika-app)).

  * __`Xvfb`__

`dynacase-te` nécessite le serveur [X virtual-frame-buffer](http://www.x.org/) (Xvfb) pour exécuter OpenOffice.org.

  * __`xauth`__

`dynacase-te` nécessite la commande `xauth` de X11/Xorg.

  * __SimpleXML__

`dynacase-te` nécessite [SimpleXML](http://www.php.net/manual/book.simplexml.php).

  * __`a2ps` et `ps2pdf14`__

`''dynacase-te''' utilise l'outils `''a2ps''' (fournit par [[http://www.gnu.org/software/a2ps/|a2ps]]) et `''ps2pdf14''' (fournit avec [[http://pages.cs.wisc.edu/~ghost/|Ghostscript]]), pour les conversions texte -> PDF. 

  * __`recode`__

`dynacase-te` utilise `recode` (fournit par [GNU recode](http://www.gnu.org/software/recode/)).

  * __`perl`__

`dynacase-te` utilise `perl` (fournit par [Perl](http://www.perl.org/)).

  * __`convert`__

`dynacase-te` utilise l'outil de conversion d'image `convert` (fournit par [ImageMagick](http://www.imagemagick.org/)).

  * __`zip` et `unzip`__

`dynacase-te` utilise les commandes `zip` et `unzip` (fournit par [Info-ZIP](http://www.info-zip.org/)).

  * __Script `/lib/lsb/init-functions`__

`dynacase-te` utilise le script `/lib/lsb/init-functions` présent sur les systèmes Linux compatibles "Linux Standard Base". Dans le cas ou ce script ne serait pas présent, voir si un paquet fournit cette fonctionnalité : paquet `redhat-lsb` sur les distribution RedHat Enterprise Linux par exemple.

## Installation

`dynacase-te` est disponible sous forme d'archive à installer sur un serveur dédié (ou non) qui assurera la fonction de serveur de transformation pour un serveur Dynacase.

### Télécharger et décompresser l'archive `dynacase-te-current.tar.gz`

<pre>
# wget http://eec.anakeen.com/public/tengine/dynacase-te-current.tar.gz
# tar zxvf dynacase-te-current.tar.gz
</pre>

L'archive se décompresse et crée un sous-répertoire `dynacase-te-${VERSION}-${RELEASE}` avec le numéro de version et de release de dynacase-te.

Pour simplifier le nommage et l'accès à ce répertoire vous pouvez le renommer :

<pre>
# mv dynacase-te-${VERSION}-${RELEASE} dynacase-te
</pre>

Par la suite, nous feront référence à ce répertoire au travers de la variable `TE_HOME` :

<pre>
# TE_HOME=/opt/dynacase-te
</pre>

__Note__ : Si vous avez un compte EEC, il est recommandé de télécharger dynacase-te depuis votre dépôt EEC afin d'avoir les dernières corrections disponibles.

### Création de la base de donnée TE

#### Créer une base `te` sur votre serveur de base de données

<pre>
# su postgres -c psql
postgres=# CREATE DATABASE "te" WITH OWNER "te";
</pre>

#### Créer/ajouter le service postgresql pour l'accès à cette base `te`

<pre>
# vi ${PGSYSCONFDIR}/pg_service.conf

   ...

[te]
host=127.0.0.1
port=5432
user=dynacaseowner
password=password
dbname=te
</pre>

Note: La valeur de `${PGSYSCONFDIR}` est dépendante de votre distribution, et peut être trouvée avec la commande : `pg_config --sysconfdir`.

#### Valider l'accès à la base de donnée `te`

<pre>
# PGSERVICE=te psql
te=# \q
</pre>

#### Nombre max de connections à la base de donnée

Le service TE comprend deux processus qui tournent en continue (`te_request_server` et `te_request_renderer`). Chacun de ces processus ouvre et maintient une connexion ouverte sur la base de donnée.

Ensuite, à chaque fork (pour le traitement d'un client pour `te_request_server`, ou le traitement d'une tâche pour `te_request_renderer`) une nouvelle connexion est faite sur la base de données.

Le nombre maximum de connections à la base de données est donc donné par la formule :

<pre>
Nombre max de connections Postgresql = 2 + REQUEST_MAX_CLIENT + RENDERING_MAX_CLIENT
</pre>

### Installation des éléments additionnels

#### tika-app {#tika-app}

L'archive `tika-app-0.9.jar` peut être obtenue en compilant les sources de [Apache Tika 0.9](http://tika.apache.org/download.html), ou bien sous forme pré-compilée sur notre dépôt `third-party` : http://ftp.dynacase.org/third-party/tika-app-0.9.jar

La compilation de `apache-tika-0.9-src.zip` avec maven (`mvn`) peut nécessiter l'augmentation des limites mémoire de la JVM :
<pre>
$ cd apache-tika-0.9
$ MAVEN_OPTS=-Xmx2048m mvn -e clean install
</pre>

L'archive JAR de `tika-app` doit ensuite être déposée dans le sous-répertoire `$TE_HOME/lib/engines` :

<pre>
# cp tika-app/target/tika-app-0.9.jar $TE_HOME/lib/engines/
</pre>


## Configuration du serveur TE

Les paramètres du serveur TE sont définis dans le fichier `$TE_HOME/etc/te.conf`.

Lors de la première utilisation il vous faudra copier le fichier d'exemple `te.conf.sample` dans `te.conf` :

<pre>
# cp $TE_HOME/etc/te.conf.sample $TE_HOME/etc/te.conf
</pre>

Les paramètres à ajuster en fonction de votre environnement sont :

  * __Répertoire temporaire de travail__

<table>
<tr><td><tt>REQUEST_DIRECTORY=/var/tmp</tt></td><td>Répertoire temporaire de travail du serveur request et des scripts engines.</td></tr>
<tr><td><tt>RENDERING_DIRECTORY=/var/tmp</tt></td><td>Répertoire temporaire de travail du serveur rendering.</td></tr>
</table>

  * __Adresse et port d'écoute TCP__

<table>
<tr><td><tt>LISTEN_ADDRESS=0.0.0.0</tt></td><td>Adresse d'écoute du serveur TE.</td></tr>
<tr><td><tt>PORT=51968</tt></td><td>Port d'écoute du serveur TE.</td></tr>
</table>

  * __Accès base de donnée__

<table>
<tr><td><tt>TE_PG_SERVICE=te</tt></td><td>Contient le le nom du service pour l'accès à la base <tt>te</tt></td></tr>
</table>

  * __Interaction avec Dynacase__

__Note__ : À partir de la version 0.8.2 la définition du login et du mot de passe ne sont plus obligatoires.

<table>
<tr><td><tt>URL_CALLBACK_LOGIN=te</tt></td><td>Login dans le cas ou le serveur Dynacase est derrière une authentification HTTP Basic non gérée par dynacase (frontal/reverse proxy HTTP avec authentification HTTP Basic par exemple).</td></tr>
<tr><td><tt>URL_CALLBACK_PASSWORD=secret</tt></td><td>Le mot de passe associé au login <tt>URL_CALLBACK_LOGIN</tt> pour l'authentification HTTP Basic.</td></tr>
</table>

  * __Lancer les serveurs sous une autre identité que root__

<table>
<tr><td><tt>TE_SERVER_USER=root</tt></td><td>L'utilisateur sous lequel lancer les serveurs de TE (''root'' par défaut).</td></tr>
</table>

__Note__ : Il est recommandé de ne pas lancer les serveurs de TE sous le compte ''root'' mais d'utiliser plutôt un compte utilisateur dédié.

  * __Paramétrage serveur OpenOffice.org__

<table>
<tr><td><tt>TE_OOO_BASE_DIR=/opt/openoffice.org3</tt></td><td>Le chemin d'accès au répertoire racine d'installation de OpenOffice.org.</td></tr>
<tr><td><tt>TE_OOO_SERVER_PYTHON=${TE_OOO_BASE_DIR}/program/python</tt></td><td>Le chemin d'accès à l'interpréteur Python de OpenOffice.org.</td></tr>
<tr><td><tt>TE_OOO_SERVER_SOFFICE=${TE_OOO_BASE_DIR}/program/soffice</tt></td><td>Le chemin d'accès au programme `''soffice''' de OpenOffice.org.</td></tr>
<tr><td><tt>TE_OOO_SERVER_UNOPKG=${TE_OOO_BASE_DIR}/program/unopkg</tt></td><td>Le chemin d'accès au programme `''unopkg''' de OpenOffice.org.</td></tr>
<tr><td><tt>TE_OOO_CLASSPATH="${TE_OOO_BASE_DIR}/basis-link/program/classes/unoil.jar:${TE_OOO_BASE_DIR}/basis-link/ure-link/share/java/juh.jar:${TE_OOO_BASE_DIR}/basis-link/ure-link/share/java/jurt.jar:${TE_OOO_BASE_DIR}/basis-link/ure-link/share/java/ridl.jar"</tt></td><td>Le classpath Java pour accéder aux librairies Java d'OpenOffice. Les classes nécessaires sont contenus dans `unoil.jar`, `juh.jar`, `jurt.jar` et `ridl.jar`.</td></tr>
<tr><td><tt>TE_OOO_JVM_OPTS=""</tt></td><td>Variable pour positionner des paramètres spécifiques pour la JVM si besoin.</td></tr>
<tr><td><tt>TE_OOO_SERVER_HOST=127.0.0.1</tt></td><td>L'adresse IP d'écoute du serveur TE.</td></tr>
<tr><td><tt>TE_OOO_SERVER_PORT=8123</tt></td><td>Le port d'écoute du serveur TE.</td></tr>
</table>

  * __Paramétrage tika-app__

<table>
<tr><td><tt>TIKA_APP_JAR="${TE_HOME}/lib/engines/tika-app-0.9.jar"</tt></td><td>Le chemin d'accès à l'archive `''tika-app-0.9.jar'''.</td></tr>
</table>

## Initialisation

Une fois les éléments installés, il vous faut initialiser la base de données TE.

<pre>
# $TE_HOME/bin/ted init
 * Initializing ted service: OK

</pre>

L'initialisation crée les tables `engine` et `task` dans la base TE.

## Démarrage/arrêt/status de TE

### Démarrage des éléments de TE (`ted start`)

TE démarre trois services qui sont :

  * Le serveur OpenOffice.org
  * Le serveur te_request_server
  * Le serveur te_rendering_server

<pre>
# $TE_HOME/bin/ted start
Starting OOO server... 27023
Starting te_request_server... 27041
Starting te_rendering_server... 27043
 * Starting ted service:  OK

</pre>

Le script `ted` s'occupe de lancer les trois composants, et affiche leur PID. 

### Status des éléments de TE (`ted status`)

<pre>
# $TE_HOME/bin/ted status
Request server running (27041)
Rendering server running (27043)
OOO server running (27023)
</pre>

Le script `ted` affiche pour chacun des trois composants s'ils tournent ou non, et leur PID.

### Vérification des moteurs de transformation (`ted check`)

Le script `ted` permet de lancer une vérification des moteurs de transformations. Pour cela, il faut démarrer les composants (voir `ted start` ci-dessus), et ensuite exécuter la commande suivante :

<pre>
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
</pre>

__Note__ : Si le nom d'hôte/nom de domaine du système est mal configuré, les temps de conversion peuvent être long du fait de timeouts de résolution de noms lors de la connexion au serveur OOo.
Pour corriger cela, assurez vous que le nom d'hôte (tel que retourné par la commande `hostname`) et le nom de domaine (tel que retourné par la commande `dnsdomainname`) sont corrects, et que le fichier `/etc/hosts` est correctement renseigné.

### Arrêts des éléments de TE (`ted stop`)

<pre>
# $TE_HOME/bin/ted stop
Stopping te_request_server... 27041
Stopping te_rendrering_server... 27043
Stopping OOO server... 27023
 * Stopping ted service:  OK

</pre>

### Nettoyage des fichiers temporaires (`ted cleantmpfiles`) {#cleantmpfiles}

L'option `cleantmpfiles` permet de supprimer les fichiers temporaires nommés `tes-*` et `ter-*`, présents dans les répertoire `$REQUEST_DIRECTORY` et `$RENDERING_DIRECTORY` qui ont plus de 7 jours (valeur par défaut).

<pre>
# $TE_HOME/bin/ted cleantmpfiles
</pre>

Si vous voulez spécifier votre propre durée, vous pouvez ajouter le nombre de jours après l'option `cleantmpfiles`.

Exemple pour supprimer les fichiers temporaires de plus de 15 jours :

<pre>
# $TE_HOME/bin/ted cleantmpfiles 15
</pre>

Pour effectuer le nettoyage régulièrement, vous pouvez exécuter cette commande à partir d'une crontab.

## Démarrage/arrêt automatique de TE avec le système

Pour que TE démarre, et s'arrête, lors du démarrage, et l'arrêt, du système, il faut enregistrer le script `ted` dans le système rc/init de votre système.

### Enregistrement de ted sur distribution de type RedHat

Les distribution de type RedHat utilisent la commande `chkconfig` pour administrer les scripts rc/init.

Faire un lien symbolique de `$TE_HOME/bin/ted` dans le répertoire `/etc/rc.d/init.d/` :

<pre>
# ln -sf $TE_HOME/bin/ted /etc/rc.d/init.d/ted
</pre>

Enregistrer `ted` :

<pre>
# chkconfig --add ted
# chkconfig ted on
# chkconfig --list ted
ted             0:arrêt 1:arrêt 2:marche        3:marche        4:marche        5:marche        6:arrêt
</pre>

### Enregistrement de ted sur distribution de type Debian

Les distributions de type Debian utilisent la commande `update-rc.d` pour administrer les scripts rc/init.

Faire un lien symbolique de `$TE_HOME/bin/ted` dans le répertoire `/etc/init.d/` :

<pre>
# ln -sf $TE_HOME/bin/ted /etc/init.d/ted
</pre>

Enregistrer `ted` :

<pre>
# update-rc.d ted defaults
 Adding system startup for /etc/init.d/ted ...
   /etc/rc0.d/K20ted -> ../init.d/ted
   /etc/rc1.d/K20ted -> ../init.d/ted
   /etc/rc6.d/K20ted -> ../init.d/ted
   /etc/rc2.d/S20ted -> ../init.d/ted
   /etc/rc3.d/S20ted -> ../init.d/ted
   /etc/rc4.d/S20ted -> ../init.d/ted
   /etc/rc5.d/S20ted -> ../init.d/ted
</pre>

## Configuration de Dynacase

Rendez vous ensuite à la page [[modules:dynacase-te:configclient]] pour configurer Dynacase

## Migration/Changelog

### Migration d'un TE 0.7.0

La partie cliente de dynacase-te est à présent intégré dans Dynacase-platform-3.X

  * Arrêter le service TE.
  * Désinstaller le paquet `dynacase-te` et `dynacase-te-client` à l'aide du gestionnaire de paquet.
  * Installer `dynacase-te-current.tar.gz` comme indiqué ci-dessus.
  * Instancier un fichier de configuration `$TE_HOME/etc/te.conf` à partir du fichier `$TE_HOME/etc/te.conf.sample`, et ajuster les paramètres en fonction de votre installation précédente.
  * Modifier les chemins des scripts de conversion de la table `engine` :

<pre>
# PGSERVICE=te psql
te=# UPDATE engine SET command = regexp_replace(command, '^/usr/share/(php|pear)/TE/engines/', '@TE_HOME@/lib/engines/');
</pre>

  * Démarrer le service TE.
  * Lancer un `ted check`.

### freedom-te-0.8.0-2

Le paramètre `TE_OOO_SERVER_USER` est renommé en `TE_SERVER_USER` et sert à lancer tous les serveurs de TE sous cette identité.

Vérifier votre configuration `te.conf` pour rétablir ce paramètre si vous utilisiez `TE_OOO_SERVER_SERVER`.

### freedom-te-0.8.0-3

Cette version introduit l'utilisation par défaut de Beagle pour l'extraction du texte des documents.

Vérifier votre configuration `te.conf` avec les paramètres `BEAGLE_*`, et ré-initialiser la table `engine` pour incorporer `beagle2txt` comme moteur de transformation pour l'extraction de texte :
  * Sauvegarder la définition de vos propres moteurs.
  * Supprimer le contenu de la table `engine` :

<pre>
# PGSERVICE=te psql
te=# DELETE FROM engine;
</pre>

  * Re-charger la nouvelle liste de moteurs :

<pre>
# PGSERVICE=te psql
te=# \i $TE_HOME/lib/engines/engine_init.sql
</pre>

  * Re-importer la définition de vos propres moteurs.

### freedom-te-0.8.1-1

Correction bug [#254](https://bug.freedom-ecm.org/issues/254) : la librairie `Lib.TEUtil.php` a été renommée en `Lib.TE.php` pour éviter les conflits avec la librairie `Lib.TEUtil.php` livrée par l'ancien paquet `freedom-te-client`.

### freedom-te-0.8.2-0

Cette version introduit trois nouveaux moteurs de transformation qui sont :
  * mergeodt
  * mergepdf
  * mergepdfa

Leur rôle est de prendre en entrée une archive ZIP contenant des fichiers texte  compréhensibles par OpenOffice.org (.odt, .doc, etc. ; sont donc exclus les .xls, .ods, .ppt, etc.), et de produire en sortie un fichier au format ODT, PFD ou PDF/A résultat de la concaténation (merge) des fichiers contenus dans l'archive.

Pour la mise à jour de la liste des moteurs :
  * Sauvegarder la définition de vos propres moteurs.
  * Supprimer le contenu de la table `engine` :

<pre>
# PGSERVICE=te psql
te=# DELETE FROM engine;
</pre>

  * Re-charger la nouvelle liste de moteurs :

<pre>
# PGSERVICE=te psql
te=# \i $TE_HOME/lib/engines/engine_init.sql
</pre>

  * Re-importer la définition de vos propres moteurs.

### freedom-te-0.8.3-1

Une petit mise-à-jour de TE afin de contourner le dysfonctionnement de `beagle2txt` pour l'extraction de texte depuis les fichiers MS/Word sur les systèmes x86-64.

La commande `beagle-extract-content` ne fonctionnant pas correctement sur ces systèmes x86-64, nous avons donc ajouté un moteur `beagledoc2txt` dédié pour l'extraction texte des .doc (en utilisant `beagle-doc-extractor`).

Si vous faites une mise-à-jour de TE, il faudra mettre à jour manuellement la base TE pour utiliser ce nouveau moteur :

<pre>
# PGSERVICE=te psql
te=# UPDATE engine SET command = '@TE_HOME@/lib/engines/beagledoc2txt' WHERE name = 'utf8' AND mime = 'application/msword';
</pre>

Vérifiez ensuite le fonctionnement de la conversion en lançant un `ted check` et en vérifiant le statut de la conversion "from MS-Word to TXT" :

<pre>
# /etc/init.d/ted check
[...]
* Checking conversion from MS-Word to TXT...
  Ok: '/tmp/test.docgFZZHm.txt' (28 bytes)

[...]
</pre>

### freedom-te-0.8.4-1

Cette version introduit un mécanisme pour définir le type MIME d'un fichier à partir de son extension, en définissant des règles d'associations dans un fichier XML.

Les règles personnelle, qui ne seront pas écrasés lors de la mise-à-jour de TE, sont a définir dans le fichier `$TE_HOME/etc/mime-user.conf` :

<pre>
  <?xml version="1.0" encoding="utf8"?>
  <mimes>
    <mime ext="eml" sys="text/x-mail" text="Mail text file" />
    <mime ext="foo" sys="application/foo" text="Foo file" />
  </mimes>
</pre>

Cela vous permet, par la suite, de pouvoir déclarer un moteur de transformation/indexation personnel pour traiter les `application/x-mail`.

### dynacase-te-1.1.0-0

Cette version remplace le script de conversion utilisant l'API Python d'OpenOffice par un script utilisant l'API Java.

Les scripts `lib/engines/ooo-server-convert.py` et `lib/engines/ooo-server-merge.py` sont donc supprimés et remplacés par le script `lib/engines/ooo-server` qui combine les fonctions de convert et merge.

Il faut donc un environnement de runtime Java 6 pour utiliser cette version de `dynacase-te`.

Cette version introduit aussi un nouveau moteur `ooo2doc` pour générer des fichiers au format MS-Word 97. Si vous faite une mise à jour il faudra ajouter la déclaration de ce moteur dans votre table `engine` :
<pre>
INSERT INTO engine (name, mime, command, "comment") VALUES ('doc', 'application/vnd.oasis.opendocument.text', '@TE_HOME@/lib/engines/ooo2doc', NULL);
</pre>

### dynacase-te-1.2.0-0

__IMPORTANT__

Le port d'écoute par défaut du serveur TE a été changé et est à présent 51968 (l'ancien port étant le 10000). Suite à la mise à jour il vous faudra soit modifier la configuration des serveurs Dynacase qui utilisent ce TE pour leur indiquer le nouveau port à utliser, ou bien modifier la configuration de TE `$TE_HOME/etc/te.conf` pour rétablir l'écoute sur le port 10000 en positionnant la variable `PORT=10000`.

Autres changements :

Cette version remplace les moteurs d'extraction de texte basés sur Beagle par un moteur (`tika2txt`) utilisant `tika-app` du projet [Apache Tika](http://tika.apache.org/).

Voir détails sur l'installation de ce composant dans la section [tika-app](#tika-app).

L'utilisation de `tika-app` apporte aussi le support de l'extraction de texte pour les documents au format MS-Office Open XML (`.docx`, `.xlsx` et `.pptx`), Apple iWork (`.pages`, `.numbers` et `.keynote`), Rich Text Format (`.rtf`), ePub (`.epub`) et Outlook Mail Message (`.msg`).

La liste des moteurs doit être mise à jour en base de données. Pour cela, il faudra re-initialiser le contenu de la table `engine' et rajouter par la suite vos éventuels moteurs spécifiques.

Pour la mise à jour de la liste des moteurs :
  * Sauvegarder la définition de vos propres moteurs.
  * Supprimer le contenu de la table `engine` :

<pre>
# PGSERVICE=te psql
te=# DELETE FROM engine;
</pre>

  * Re-charger la nouvelle liste de moteurs :

<pre>
# PGSERVICE=te psql
te=# \i $TE_HOME/lib/engines/engine_init.sql
</pre>

  * Re-importer la définition de vos propres moteurs.

Le fichier de configuration (`$TE_HOME/etc/te.conf`) doit être modifié afin de référencer le programme tika

<pre>
# -- Tika-app Jar file
TIKA_APP_JAR="${TE_HOME}/lib/engines/tika-app-0.9.jar"
</pre>

### dynacase-te-1.3.0-0

Cette version améliore la gestion des fichiers temporaires et ajoute une option au script rc-init ted pour nettoyer les fichiers temporaires de TE qui ont plus de X jours.

Voir [Nettoyage des fichiers temporaires](#cleantmpfiles)

### dynacase-te-1.3.1-0

Cette version corrige un problème de détection du type MIME des fichiers OpenOffice.org.
