# Introduction {#te-manex-ref:bdd0cab6-f8bc-4044-82cf-3f40a3376678}

## Présentation {#te-manex-ref:84947824-16de-4086-96fe-8044027b4002}

Le serveur de transformation *Dynacase TEngine* ou *TE* est un composant autonome
d'une architecture Dynacase.

Il a pour rôle d'exécuter sur demande de Dynacase Platform ou de ses
applications des travaux de transformation. Il pilote pour cela des moteurs de
transformation ou *Engine*.

Chaque moteur traite un fichier en entrée dans formats donnés, et produit en
sortie un fichier résultat. Le traitement réalisé et les formats de fichiers
font la spécificité du moteur.

Lors de l'installation du serveur *TE*, un ensemble de [moteurs est livré en
standard][std_engines].

Le développeur peut [ajouter ses propres moteurs][add_engines].

Le serveur *TE* est constitué de deux composants distincts :

* le serveur de communication gérant la communication avec ses clients
* le serveur de transformation ayant pour rôle le lancement des travaux et la gestion des produits.

Ce manuel aborde le fonctionnement du *TE* selon trois axes

* d'un point de vue installation et mise à jour, thème abordé au chapitre [Installation][installation] ;
* l'exploitation et la supervision du *TE* sont traitées au chapitre [Exploitation][exploitation] ;
* les mécanismes supportés permettant de développer des clients ou moteur *TE* sont décrits au chapitre [Fonctionnement][fonctionnement].

## Notes de version {#te-manex-ref:da01abb1-163e-434f-b56c-eb918c479cb9}

Les paragraphes suivant détaillent pour chacune des versions les nouveautés
et précisent si nécessaire les instructions de mise à jour.

<!-- links -->
[std_engines]: #te-manex-ref:49dd054a-e596-4ebe-9e75-adca937f630b
[add_engines]: #te-manex-ref:87061b8c-19ca-4a7b-b370-706ae3fe1160
[installation]: #te-manex-ref:99a9bb66-739f-4fa4-88f9-fe236a9a8e7c
[exploitation]: #te-manex-ref:cdff4ee0-3ed7-4293-8de9-ab53dc8ad312
[fonctionnement]: #te-manex-ref:674a6c70-eb4e-415d-8f03-5a2a7142578e
