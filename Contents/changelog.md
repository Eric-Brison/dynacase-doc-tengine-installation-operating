*[TE]: Transformation Engine

# ChangeLog

## Version 1.3.4

La version `1.3.4` de TE introduit l'utilisation de Apache Tika en version
`1.4` et d'OpenOffice `4.0`.

Si vous mettez à jour une installation existante, il vous faudra re-installer
Apache Tika 1.4 (voir [Installation des éléments additionnels > tika-app](#tika-app))
et modifier la variable `TIKA_APP_JAR` dans le fichier de configuration
`$TE_HOME/etc/te.conf` pour référencer cette nouvelle version de Apache Tika
([Configuration du serveur TE > Paramétrage tika-app](#conf-tika-app)).

Si vous mettez à jour OpenOffice de la version 3.x à la version 4.0, il vous
faudra aussi modifier la variable `TE_OOO_CLASSPATH` dans le fichier de
configuration `$TE_HOME/etc/te.conf` pour référencer correctement
l'emplacement des fichiers `.jar` requis
(voir [Configuration du serveur TE > Paramétrage serveur OpenOffice](#conf-ooo)).
