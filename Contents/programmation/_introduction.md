# Fonctionnement {#te-manex-ref:674a6c70-eb4e-415d-8f03-5a2a7142578e}

## Principes {#te-manex-ref:8ff30cd9-9630-4360-8fce-1f79b24ccaa1}

Le serveur TE s'intègre dans l'architecture Dynacase de manière autonome.

Il peut être installé sur une machine différente de celle supportant Dynacase
Platform et ses applications.[^1]

Il peut être mutualisé pour répondre aux demandes d'applications hébergés par
des contextes différents de Dynacase.

Le serveur TE reçoit des demandes de *travaux* par ses clients.  Un travail est
une demande de transformation de données (fichiers) par un processus
particulier.  Ces processus sont matérialisés par des moteurs de transformation
ou _transformation engines_.

### Architecture {#te-manex-ref:e3934c80-fa8e-4bd3-a455-7ae17fb010fe}

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

### Cinématique {#te-manex-ref:36ff6309-f0a4-4242-8829-f3116cb0f580}

#### Échanges Client / Serveur {#te-manex-ref:06b2ecf1-33cc-4226-87e3-5ef64d8734c5}

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

#### Cycle de traitement des demandes {#te-manex-ref:099d71d4-76dc-41c9-aa19-9126fe7cb555}

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
    * Lorsque Dynacase a récupéré le résultat, alors la tâche passe dans l'état
      `S` (Sent).
    * Si la tâche est en erreur, son statut est positionné à `K` (Knockout).
    * Si la tâche est interrompue, son statut est positionné à `I` (Interrupted).

### Les types mimes {#te-manex-ref:b779cd57-dd9c-4212-b2b6-7697c007d02a}

Voir [Types MIME][MIME_type].

## Création d'un moteur {#te-manex-ref:87061b8c-19ca-4a7b-b370-706ae3fe1160}

### Protocole moteur {#te-manex-ref:fad5a3c6-0106-4363-b91f-f648d4c21d33}

Un moteur est un exécutable (ou un shell-script) qui répond au protocole
suivant :

Arguments
:   L'exécutable est appelé avec deux arguments qui sont :
    
    * le fichier à transformer (entrée) ;
    * un fichier temporaire (existant) dans lequel le résultat doit être
      inscrit (sortie).

Exit code
:   L'exécutable doit se terminer avec :
    
    * un exit code égal `0` si la conversion est réussie ;
    * un exit code différent de `0` si la conversion à échouée.

Messages d'erreurs
:   L'exécutable émet ses messages d'erreurs sur STDERR.

Variables d'environnement
:   Les variables d'environnement suivantes sont positionnées lors de
    l'exécution du moteur :
    
    * `TE_HOME` : contient le chemin d'accès à la racine du répertoire
      d'installation de TE.

Exemple de script Bash conforme au protocole des moteurs décrit ci-dessus :

    [bash]
    #!/bin/bash
    
    # Le premier argument est le fichier à transformer.
    IN=$1
    # Le deuxième argument est un fichier temporaire dans lequel
    # sera inscrit le résultat.
    OUT=$2
    
    # Notre moteur compte simplement le nombre de lignes
    # dans le fichier d'entré, et inscrit le résultat dans 
    # le fichier de sortie.
    wc -l "$IN" > "$OUT"
    
    if [ $? -ne 0 ]; then
        # En cas d'erreur, on émet un message sur STDERR
        echo "Erreur lors de l'exécution du moteur." 1>&2
        # Et on se termine avec un exit code <> 0
        exit 1
    fi
    
    # La conversion est réussi, on se termine avec
    # un exit code = 0
    exit 0

### Enregistrement d'un moteur {#te-manex-ref:3649a6a1-03eb-42d4-aa4c-1d473f081354}

Pour être utilisable par TE, le moteur doit être exécutable et être déposé dans
le répertoire `$TE_HOME/lib/engines`.

Ensuite, le moteur doit être enregistré auprès de TE afin de déclarer son non
de conversion et les types MIME des fichiers qu'il supporte en entrée.

L'enregistrement des moteurs est géré dans la table `engine` :

    [sql]
    INSERT INTO engine (name, mime, command, comment) VALUES (
        'linecount',
        'text/plain',
        '@TE_HOME@/lib/engines/linecount',
        'Compte le nombre de lignes dans un fichier texte'
    );

Dans l'exemple ci-dessus, on déclare que notre shell-script aura comme nom de
transformation `linecount` et qu'il est compatible avec les fichiers texte brut
de type MIME `text/plain`.

Notes :

* La chaîne `@TE_HOME@` sera automatiquement remplacée par la racine du
  répertoire d'installation de TE lors de l'exécution du moteur.

## Création d'un client {#te-manex-ref:ba394e73-7e00-4ed8-a0d3-f4f80db92e95}

L'API cliente est fournit par le module `dynacase-tengine-client`.

### Instanciation d'un client TE {#te-manex-ref:9cf0794c-3d0e-482c-84ed-e9d12b015c8a}

La construction d'un client TE prend en argument l'adresse (ou le nom DNS) et
le port du serveur TE avec lequel on souhaite communiquer.

L'adresse et le port renseignés dans les paramètres applicatifs de Dynacase
sont accessibles avec la fonction
`\ApplicationParameterManager::getScopedParameterValue()` et les paramètres
`TE_HOST` et `TE_PORT`.

    [php]
    $te = new \Dcp\TransformationEngine\Client(
        \ApplicationParameterManager::getScopedParameterValue("TE_HOST"),
        \ApplicationParameterManager::getScopedParameterValue("TE_PORT")
    );

### Envoi d'un fichier pour conversion {#te-manex-ref:37331baf-7615-46fc-a718-5b0227c9b832}

Dans l'exemple ci-dessous, nous allons construire un fichier texte
`/tmp/hello.txt` que nous allons ensuite convertir au format PDF.

    [php]
    
    /* Le fichier texte */
    $filename = '/tmp/hello.txt';
    $textContent = 'Hello world.';
    file_put_content($filename, $textContent);
    
    /* On envoi une demande de conversion */
    $fkey = uniqid();
    $callback = '';
    $info = array();
    $err = $te->sendTransformation(
        'pdf',     /* Nom de la conversion à appliquer : 'pdf' */
        $fkey,     /* Identifiant de la conversion côté Dynacase */
        $filename, /* Chemin du fichier à convertir */
        $callback, /* URL de callback pour la notification de fin de tâche */
        $info      /* Information en retour de la tâche de conversion créée */
    );
    if ($err != '') {
        throw new Exception(sprintf("sendTransformation() returned with error: %s", $err));
    }
    /* Si la demande est accceptée, l'identifiant de la conversion
     * est retourné dans la structure $info
     */
    var_dump($info);

    array(3) {
      'tid' =>
      string(23) "54c8a79a4780d2.85897641"
      'status' =>
      string(1) "W"
      'comment' =>
      string(26) "13 bytes read in 0.005 sec"
    }

L'identifiant de la tâche `tid` est retourné dans la structure `$info`.

Si une URL de callback a été fourni via l'argument `$callback`, celle-ci est
alors appelée lorsque la conversion est terminée avec un argument HTTP `tid`
contenant l'identifiant de la tâche : c'est alors à la charge du code déclenché
par le callback de venir récupérer le résultat à l'aide de l'identifiant de la
tâche `tid` retourné dans la structure `$info`.

* Le client soumet une transformation avec l'URL de callback :
  `http://localhost/dynacase/?app=TEST&action=CALLBACK`
* Lorsque la tâche de transformation est terminée, le serveur TE appelle l'URL
  de callback en y ajoutant la variable HTTTP `tid` contenant la valeur de
  l'identifiant de la tâche :
  `http://localhost/dynacase/?app=TEST&action=CALLBACK&tid=54c8a79a4780d2.85897641`
* L'action `CALLBACK` de l'application `TEST` est alors exécutée.
* L'action peut alors récupérer le résultat de la tâche en faisant une requête
  de récupération de résultat avec l'identifiant de la tâche ainsi obtenu.

### Interrogation du statut d'une tâche {#te-manex-ref:bb9416b7-5305-4bc4-9cc6-cc4b8d1c4c64}

Une fois la tâche soumise, le client peut interroger régulièrement (ou dans le
code exécuté par le callback du serveur TE) la tâche à partir de son `tid` pour
voir si elle est terminée (ou en attente, ou en échec, etc.).

    [php]
    $tid = '54c8a79a4780d2.85897641';
    $info = array();
    $err = $te->getInfo(
        $tid, /* Identifiant de la tâche qu'on souhaite interroger */
        $info
    );
    if ($err != '') {
        throw new Exception(sprintf("getInfo() returned with error: %s", $err));
    }
    var_dump($info);

    array(11) {
      'tid' =>
      string(23) "54c8a79a4780d2.85897641"
      'infile' =>
      string(23) "/var/tmp/tes-6FDTUK.txt"
      'inmime' =>
      string(11) "text/plain;"
      'outfile' =>
      string(23) "/var/tmp/ter-xbpYHM.pdf"
      'engine' =>
      string(3) "pdf"
      'status' =>
      string(1) "D"
      'callback' =>
      string(0) ""
      'callreturn' =>
      string(0) ""
      'fkey' =>
      string(0) ""
      'pid' =>
      string(5) "15678"
      'comment' =>
      string(52) "generated by [/opt/te/lib/engines/txt2pdf] command; "
    }

Le statut de la tâche est consultable avec `$info['status']` et peut prendre
les valeurs suivantes :

| statut | constante |
| ------ | --------- |
| `B` | \Dcp\TransformationEngine\Client::TASK_STATE_BEGINNING |
| `T` | \Dcp\TransformationEngine\Client::TASK_STATE_TRANSFERRING |
| `K` | \Dcp\TransformationEngine\Client::TASK_STATE_ERROR |
| `D` | \Dcp\TransformationEngine\Client::TASK_STATE_SUCCESS |
| `P` | \Dcp\TransformationEngine\Client::TASK_STATE_PROCESSING |
| `W` | \Dcp\TransformationEngine\Client::TASK_STATE_WAITING |
| `I` | \Dcp\TransformationEngine\Client::TASK_STATE_INTERRUPTED |
| `S` | \Dcp\TransformationEngine\Client::TASK_STATE_SENT |

Voir [Cycle de traitement des demandes][workflow] pour le détails des codes de
statut.

### Récupération du résultat d'une tâche {#te-manex-ref:a007e38b-02e6-4f23-899a-5ae7c9e2568b}

    [php]
    $tid = '54c8a79a4780d2.85897641';
    $file = '/tmp/hello.pdf';
    $err = $te->getTransformation(
        $tid, /* Identifiant de la tâche dont on souahite récupérer le résultat */
        $file /* Fichier dans lequel inscrire le résultat */
    );
    if ($err != '') {
        throw new Exception(sprintf("getTransformation() returned with error: %s", $err));
    }
    printf("Fichier %s : %d octets\n", 

    Fichier '/tmp/hello.pdf' : 2729 octets.

Si la tâche n'est pas terminée, alors une erreur est retournée.

### Interruption d'une tâche {#te-manex-ref:78ebe0d8-0857-4ad6-a5b0-0622930a6a1f}

    [php]
    $tid = '54c8a79a4780d2.85897641';
    $err = $te->eraseTransformation(
        $tid /* Identifiation de la tâche qu'on souhaite interrompre */
    );
    if ($err != '') {
        throw new Exception(sprintf("eraseTransformation() returned with error: %s", $err));
    }

Lorsqu'une tâche est interrompue, son statut est positionné à `I` et les
fichiers d'entré et de sortie sont supprimés.


[^1]: Sur des architectures de production nous préconisons une installation
systématique sur un serveur autonome. Les ressources consommées (disque, cpu et
mémoire) par le serveur et ses moteurs peuvent fortement pénaliser le
fonctionnement d'autres applications.

<!-- links -->
[MIME_type]: #te-manex-ref:3e1d421e-9cb9-48f5-8c86-dd873a970eec
[workflow]: #te-manex-ref:099d71d4-76dc-41c9-aa19-9126fe7cb555
