# Fonctionnement {#te-manex-ref:674a6c70-eb4e-415d-8f03-5a2a7142578e}

## Principes {#te-manex-ref:8ff30cd9-9630-4360-8fce-1f79b24ccaa1}

Le serveur TE s'intègre dans l'architecture Dynacase de manière autonome.

Il peut être installé sur une machine différente de celle supportant Dynacase Platform et ses applications.[^1]

Il peut être mutualisé pour répondre aux demandes d'applications hébergés par des contextes différents de Dynacase.

Le serveur TE reçoit des demandes de *travaux* par ses clients.
Un travail est une demande de transformation de données (fichiers) par un processus particulier.
Ces processus sont matérialisés par des moteurs de transformation ou _transformation engines_.


### Architecture {#te-manex-ref:e3934c80-fa8e-4bd3-a455-7ae17fb010fe}

### Cinématique {#te-manex-ref:36ff6309-f0a4-4242-8829-f3116cb0f580}

#### Échanges Client / Serveur {#te-manex-ref:06b2ecf1-33cc-4226-87e3-5ef64d8734c5}

#### Cycle de traitement des demandes {#te-manex-ref:099d71d4-76dc-41c9-aa19-9126fe7cb555}

### Les types mimes {#te-manex-ref:b779cd57-dd9c-4212-b2b6-7697c007d02a}

## Création d'un moteur {#te-manex-ref:87061b8c-19ca-4a7b-b370-706ae3fe1160}


## Création d'un client {#te-manex-ref:ba394e73-7e00-4ed8-a0d3-f4f80db92e95}

### Synchrone / Asynchrone {#te-manex-ref:53b2e83c-31a4-4405-ba97-1575d2548ba3}



[^1]: Sur des architectures de production nous préconisons une installation systématique sur un serveur autonome. Les ressources consommées (disque, cpu et mémoire) par le serveur et ses moteurs peuvent fortement pénaliser le fonctionnement d'autres applications.
