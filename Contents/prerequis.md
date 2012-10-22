*[TE]: Transformation Engine

# Pré-requis

PHP 5.x (cli) et extension pgsql
:   TE est écrit en PHP et nécessite donc l'interpréteur [PHP](http://www.php.net/) (php-cli) et l'extension pgsql.

Java Runtime Environment 6
:   TE nécessite un environnement Java 6 pour utiliser l'API Java d'OpenOffice en mode serveur. Il est mis à disposition soit par [OpenJDK](http://openjdk.java.net/install/) ou par [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html).

OpenOffice.org 3.2.1
:   TE est préconfiguré pour fonctionner avec OpenOffice.org 3.2.1 tels que livré par les paquets du site officiel [OpenOffice.org](http://www.openoffice.org) (installé par défaut dans `/opt/openoffice.org3`).

tika-app-0.9.jar
:   TE utilise l'outils tika-app du projet [Apache Tika](http://tika.apache.org/) pour l'extraction de texte (voir [tika-app](#tika-app)).

Xvfb
:   TE nécessite le serveur [X virtual-frame-buffer](http://www.x.org/) (Xvfb) pour exécuter OpenOffice.org.

xauth
:   TE nécessite la commande `xauth` de X11/Xorg.

SimpleXML
:   TE nécessite [SimpleXML](http://www.php.net/manual/book.simplexml.php).

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