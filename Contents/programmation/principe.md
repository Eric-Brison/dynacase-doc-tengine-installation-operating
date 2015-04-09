# Principes {#te-manex-ref:8ff30cd9-9630-4360-8fce-1f79b24ccaa1}

Le serveur TE s'intègre dans l'architecture Dynacase de manière autonome.

Il peut être installé sur une machine différente de celle supportant Dynacase
Platform et ses applications.[^1]

Il peut être mutualisé pour répondre aux demandes d'applications hébergés par
des contextes différents de Dynacase.

Le serveur TE reçoit des demandes de *travaux* par ses clients.  Un travail est
une demande de transformation de données (fichiers) par un processus
particulier.  Ces processus sont matérialisés par des moteurs de transformation
ou _transformation engines_.

## Architecture {#te-manex-ref:e3934c80-fa8e-4bd3-a455-7ae17fb010fe}

Le serveur TE comporte plusieurs services qui sont :

Request server
:   Ce serveur est celui qui est en écoute sur le réseau et qui gère la
    communication entre Dynacase et TE.
    
    On peut catégoriser les demandes en deux types :
    
    * Demandes de conversion
    * Demandes de récupération de résultat
    
    Dans le cas d'une demande de conversion, il stocke en local le fichier et
    les paramètres de la transformation demandée (nom du moteur à appliquer,
    URL de callback à appeler en fin de transformation) dans une tâche qui est
    alors en attente de traitement.
    
    Dans le cas d'une demande de récupération de résultat, il va inspecter la
    tâche correspondante pour voir si elle à été traitée et fournir le fichier
    résultat.

Rendering server
:   Ce serveur tourne en tâche de fond, et scrute continuellement pour voir
    s'il y a de nouvelles tâches en attente de traitement.
    
    Lorsqu'une nouvelle tâche en attente de traitement est trouvée, il exécute
    alors le moteur de transformation associé avec le fichier soumit par le
    client.
    
    Si la tâche s'est correctement déroulée, il enregistre le fichier produit
    et appelle l'éventuelle URL de callback de la tâche pour notifier Dynacase
    que le résultat de la conversion peut être récupéré.

OOO server
:   Ce serveur est une instance de OpenOffice lancée en mode serveur qui est
    utilisée par certains moteurs de transformations.

## Cinématique {#te-manex-ref:36ff6309-f0a4-4242-8829-f3116cb0f580}

### Échanges Client / Serveur {#te-manex-ref:06b2ecf1-33cc-4226-87e3-5ef64d8734c5}

La communication entre Dynacase et le Request server permet au client de :

* Demander la conversion d'un fichier avec un moteur donné.
* Demander le statut d'une tâche de conversion.
* Demander la liste des moteurs de transformation supporté par le serveur TE
  avec, pour chacun, la liste des type MIME des fichiers compatibles en entré.
* Demander la liste des tâches et de leurs journaux (management/monitoring).
* Demander les journaux d'un tâche en particulier.
* Demander les informations générale du serveur TE (version, charge, etc.).
* Demander l'exécution des "selftests".
* Demander le résultat d'une tâche réussie.
* Demander l'interruption d'une tâche.
* Demander la purge de l'historique des tâche et des journaux.

### Cycle de traitement des demandes {#te-manex-ref:099d71d4-76dc-41c9-aa19-9126fe7cb555}

![Workflow tâche](te-task-workflow.png "Workflow tâche")

<span style="color: blue">Request server</span>
:   
    
    * Lors de la réception d'une demande de conversion, le service `Request server`
      crée une tâche dans l'état `B` (Beginning).
    * La tâche passe ensuite dans l'état `T` (Transferring) durant le temps
      qu'il faut pour lire le fichier de la requête.
    * Une fois le fichier lu et enregistré en local, la tâche passe dans l'état `W`
      (Waiting) pour indiquer qu'elle est prête à être traitée.
    * Si la tâche est en erreur, son statut est positionné à `K` (Knockout).
    * Si la tâche est interrompue, son statut est positionné à `I` (Interrupted).

<span style="color: green">Rendering server</span>
:   
    
    * En parallèle, le service `Renderer server` scrute les nouvelles tâches qui
      sont en attente de traitement (`W`).
    * Lorsqu'une nouvelle tâche est prise pour exécution, la tâche passe en
      status `P`.
    * Si la conversion est réussie, la tâche passe à l'état `D` (Done) et
      Dynacase est notifié via l'URL de callback.
    * Lorsque Dynacase a récupéré le résultat, alors la tâche est supprimée.
    * Si la tâche est en erreur, son statut est positionné à `K` (Knockout).
    * Si la tâche est interrompue, son statut est positionné à `I` (Interrupted).

## Les types mimes {#te-manex-ref:b779cd57-dd9c-4212-b2b6-7697c007d02a}

Voir [Types MIME][MIME_type].




[^1]: Sur des architectures de production nous préconisons une installation
systématique sur un serveur autonome. Les ressources consommées (disque, cpu et
mémoire) par le serveur et ses moteurs peuvent fortement pénaliser le
fonctionnement d'autres applications.

<!-- links -->
[MIME_type]: #te-manex-ref:3e1d421e-9cb9-48f5-8c86-dd873a970eec
[workflow]: #te-manex-ref:099d71d4-76dc-41c9-aa19-9126fe7cb555
