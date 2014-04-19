# Introduction {#te-manex-ref:bdd0cab6-f8bc-4044-82cf-3f40a3376678}

## Présentation {#te-manex-ref:84947824-16de-4086-96fe-8044027b4002}

Le serveur de transformation *Dynacase TEngine* ou *TE* est un composant autonome
d'une architecture Dynacase.

Il a pour rôle d'exécuter sur demande de Dynacase Platform ou de ses applications des
travaux de transformation. Il pilote pour cela des moteurs de tranformation ou *Engine*.

Chaque moteur traite un ou plusieurs fichiers, de formats donnés, en entrée et produit en sortie
un ou plusieurs fichiers. Le traitement réalisé et les formats de fichiers font la
spécificité du moteur.

Lors de l'installation du serveur *TE*, un ensemble de moteurs est livré en standard <span class="flag inline fixme">link</span>.

Le développeur peut ajouter ses propres moteurs [.......]() <span class="flag inline fixme">link</span>.

Le serveur *TE* est consistué de deux composants distints :

* le serveur de communication gérant la communication avec ses clients
* le serveur de transformation ayant pour rôle le lancement des travaux et la gestion des produits.

Ce manuel aborde le fonctionnement du *TE* selon trois axes

* d'un point de vue installation et mise à jour, thème abordé au chapitre  <span class="flag inline fixme">link</span>;
* l'exploitation et la supervision du *TE* sont traitées au chapitre <span class="flag inline fixme">link</span>;
* les mécanismes supportés permettant de développer des clients ou moteur *TE* sont décrits au chapitre <span class="flag inline fixme">link</span>.

## Notes de version {#te-manex-ref:da01abb1-163e-434f-b56c-eb918c479cb9}

Les paragraphes suivant détaillent pour chacune des versions les nouveautés
et précisent si nécessaire les instructions de mise à jour.
