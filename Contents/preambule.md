# Introduction {#te-manex-ref:bdd0cab6-f8bc-4044-82cf-3f40a3376678}

## Présentation {#te-manex-ref:84947824-16de-4086-96fe-8044027b4002}

Le serveur de transformation *Dynacase TEngine* ou *TE* est un composant
autonome d'une architecture Dynacase.

Il a pour rôle d'exécuter, sur demande de Dynacase Platform ou de ses
applications, des travaux de transformation. Il pilote pour cela des moteurs de
transformation ou *Engine*.

Chaque moteur traite un fichier en entrée dans un format donné, et produit en
sortie un fichier résultat. Le traitement réalisé et les formats de fichiers
font la spécificité du moteur.

Lors de l'installation du serveur *TE*, un ensemble de [moteurs est livré en
standard][std_engines].

Le développeur peut [ajouter ses propres moteurs][add_engines].

Le serveur *TE* est constitué de deux composants distincts :

* le serveur de communication gérant la communication avec ses clients ;
* le serveur de transformation ayant pour rôle le lancement des travaux et la
  gestion des produits.

Ce manuel aborde le fonctionnement du *TE* selon trois axes :

* d'un point de vue installation et mise à jour, thème abordé au chapitre
  [Installation][installation] ;
* l'exploitation et la supervision du *TE* sont traités au chapitre
  [Exploitation][exploitation] ;
* les mécanismes supportés permettant de développer des clients ou moteurs *TE*
  sont décrits au chapitre [Fonctionnement][fonctionnement].

## Notes de version {#te-manex-ref:da01abb1-163e-434f-b56c-eb918c479cb9}

Les paragraphes suivant détaillent pour chacune des versions les nouveautés
et précisent si nécessaire les instructions de mise à jour.

### Compatibilité {#te-manex-ref:669acad7-cad6-4b61-8dbf-2722033ef514}

Dynacase Platform
:  version 3.2

Apache Tika Server
:  version 1.7

OpenOffice.org / LibreOffice.org
:  4.0 &nbps;&nbps;&nbps;&nbps;&nbps;&nbps; /    4.0 - 5.0 - 5.1

### Releases {#te-manex-ref:a5f86548-3543-4c77-a240-227b1913b26a}

#### Release 1.4.1 {#te-manex-ref:3640ee47-16c1-418c-92cc-e09e8a711ed3}

Cette version permet d'utiliser les versions 5.0 et 5.1 de
[LibreOffice](http://www.libreoffice.org).

Le [paramètrage de LibreOffice/OpenOffice][ooo-config] a été simplifiée.

#### Release 1.4.0 {#te-manex-ref:5df59eae-25aa-486a-913e-93c09b9cc542}

Point majeur de cette release, des interfaces permettant de réaliser les
opérations de paramétrage de Platform et de surveillance de TE sont
disponibles.

Elles sont installées dans le centre d'administration Dynacase Platform grâce
aux modules Dynacase TEngine Configuration (disponible publiquement) ou
Dynacase TEngine Monitor (disponible sous licence Anakeen).

Cette version introduit aussi le support de Tika en mode serveur afin
d'accélérer les conversions texte et réduire la charge serveur.

* utilisation de Tika en mode serveur ;
* suppression des moteurs `html2txt`, `ooo2txt` et `pdf2txt` (remplacés par
  `tika2txt`) ;
* suppression des répertoires de travail `REQUEST_DIRECTORY` et
  `RENDERING_DIRECTORY` remplacés par une seul et même paramètre `TE_WORK_DIR`
  ;
* chaque transformation crée ses fichiers temporaires dans un répertoire
  temporaire de travail dédié créé dans le répertoire `TE_WORK_DIR` (préfixe
  `te-task-`);
* l'argument `cleantmpfiles` du script rc/init `ted` a été modifié pour
  supprimer les répertoires de travail des transformations (préfixe `te-task-`)
  ;
* suppression du moteur `txt2txt` (la transformation étant à présent gérée par
  le moteur `tika2txt`) ;
* l'URL de callback d'une transformation est appelée à la fin de la
  transformation quelque soit sont "status" final ({K, D, I}) ;

##### Mise à jour depuis la version 1.3.4 {#te-manex-ref:66bdaad9-9646-4ac2-b61e-ee225e2e17df}

Après avoir installé la nouvelle version de TE, les modifications suivantes
sont à faire :

* Stopper le serveur TE.
* Installer `tika-server-<version>.jar` : voir [Installation de
  tika-server][tika-server].
* Configurer les variables `TE_TIKA_SERVER_*` dans `etc/te.conf` : voir
  [Configuration serveur Tika](#te-manex-ref:0260dfe8-d1c9-40c8-b2ba-666988ae4c09).
* Supprimer les variables `REQUEST_DIRECTORY` et `RENDERING_DIRECTORY` dans
  `etc/te.conf` et renseigner la variable `TE_WORK_DIR` : voir
  [Paramètres](#te-manex-ref:0260dfe8-d1c9-40c8-b2ba-666988ae4c09).
* Supprimer toutes les transformations de la table `task` :

    # PGSERVICE=${TE_PG_SERVICE} psql
    te=# DELETE FROM task;

* Supprimer le fichier `$TE_HOME/lib/engines/te-xvfb-server`.
* Supprimer le fichier `$TE_HOME/lib/engines/tika-app-<version>.jar`.
* Supprimer le fichier `$TE_HOME/lib/engines/html2txt`.
* Supprimer le fichier `$TE_HOME/lib/engines/ooo2txt`.
* Supprimer le fichier `$TE_HOME/lib/engines/pdf2txt`.
* Supprimer le fichier `$TE_HOME/lib/engines/txt2txt`.
* Démarrer le serveur TE.

Lors du redémarrage, le nouveau serveur Tika doit être lancé :

    Starting OOO server... 16020
    Starting Tika server... 16065
    Starting te_request_server... 16104
    Starting te_rendering_server... 16140
     * Starting ted service:  OK

Pour la mise à jour de la liste des moteurs :

* Sauvegarder la définition de vos propres moteurs.
* Supprimer le contenu de la table `engine` :

    # PGSERVICE=${TE_PG_SERVICE} psql
    te=# DELETE FROM engine;

* Re-charger la nouvelle liste de moteurs :

    # PGSERVICE=${TE_PG_SERVICE} psql
    te=# \i $TE_HOME/lib/engines/engine_init.sql

* Re-importer la définition de vos propres moteurs.

<!-- links -->
[std_engines]:    #te-manex-ref:49dd054a-e596-4ebe-9e75-adca937f630b
[add_engines]:    #te-manex-ref:87061b8c-19ca-4a7b-b370-706ae3fe1160
[installation]:   #te-manex-ref:99a9bb66-739f-4fa4-88f9-fe236a9a8e7c
[exploitation]:   #te-manex-ref:cdff4ee0-3ed7-4293-8de9-ab53dc8ad312
[fonctionnement]: #te-manex-ref:674a6c70-eb4e-415d-8f03-5a2a7142578e
[tika-server]:    #te-manex-ref:862c633f-e53f-4f7e-8544-fc795eb03acd
[ooo-config]:     #te-manex-ref:600fb6d4-d9e3-4032-a9b7-ca60c276f728