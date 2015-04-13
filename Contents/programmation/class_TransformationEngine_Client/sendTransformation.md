# Client::sendTransformation {#te-manex-ref:adab03f2-0526-4d12-a28c-232a70a0fd8c}

<div class="short-description">
Envoi d'un fichier pour transformation.
</div>

## Description {#te-manex-ref:1e29df4a-13e7-427f-ad48-1e2b5284be5d}

    [php]
    string sendTransformation ( string $te_name,
                                string $fkey,
                                string $filename,
                                string $callback,
                                 array & $info )

Crée une tâche pour la transformation du fichier `$filename` avec le moteur de
transformation `$te_name`.

L'identifiant de la tâche (`tid`) crfée sur le serveur TE est retourné dans la
structure `$info`.

Si une URL de callback est fournie via l'argument $callback, celle-ci est alors
appelée lorsque la conversion est terminée avec un argument HTTP `tid`
contenant l'identifiant de la tâche : c'est alors à la charge du code déclenché
par le callback de venir récupérer le résultat à l'aide de l'identifiant de la
tâche `tid`.

Le code peut aussi interroger régulièrement le serveur TE pour voir le status
de la tâche à l'aide de l'identifiant `tid` retourné dans la structure `$info`
(voir [`getInfo()`][Client::getInfo]).

* Le client soumet une transformation avec l'URL de callback :
  `http://localhost/dynacase/?app=TEST&action=CALLBACK`
* Lorsque la tâche de transformation est terminée, le serveur TE appelle l'URL
  de callback en y ajoutant la variable HTTTP `tid` contenant la valeur de
  l'identifiant de la tâche : `http://localhost/dynacase/?app=TEST&action=CALLBACK&tid=54c8a79a4780d2.85897641`
* L'action `CALLBACK` de l'application `TEST` est alors exécutée.
* L'action peut alors récupérer le résultat de la tâche en faisant une requête
  de récupération de résultat avec l'identifiant de la tâche ainsi obtenu.

### Avertissements {#te-manex-ref:e83ac346-fa43-4856-b015-ed152214e68c}

## Liste des paramètres {#te-manex-ref:ffa86e27-2d6a-4235-abda-3ff3e0497578}

(string) `te_name`
:   Le nom du moteur de transformation à appliquer.

(string) `fkey`
:   Un identifiant à la discrétion de l'appelant.

(string) `filename`
:   Le chemin d'accès du fichier à convertir.

(string) `callback`
:   L'URL à appeler à la fin de la transformation.

[out] (array) `info`
:   Informations retournées de la tâche créée sur le serveur TE.

## Valeur de retour {#te-manex-ref:09712757-e5e3-4b4a-8634-f0335c03242e}

La méthode `sendTransformation()` retourne une chaîne non vide, contenant un
message d'erreur, si la création de la tâche de transformation a échoué, ou une
chaîne vide si la transformation a été correctement soumise au serveur.

Si la transformation a été correctement soumise au serveur, la variable `$info`
contient les informations de la tâche créée sur le serveur :

    [php]
    array(
        'status'  => (string) $taskStatus,
        'tid'     => (string) $taskId,
        'comment' => (string) $transformationComment
    )

Liste des constantes de statut de tâche : 

`\Dcp\TransformationEngine\Client::TASK_STATE_BEGINNING` (`B`)
:   Demande de conversion reçue.

`\Dcp\TransformationEngine\Client::TASK_STATE_TRANSFERRING` (`T`)
:   Lecture/transfert du fichier à convertir.

`\Dcp\TransformationEngine\Client::TASK_STATE_ERROR` (`K`)
:   Erreur.

`\Dcp\TransformationEngine\Client::TASK_STATE_SUCCESS` (`D`)
:   Transformation réussie.

`\Dcp\TransformationEngine\Client::TASK_STATE_PROCESSING` (`P`)
:   En cours de traitement.

`\Dcp\TransformationEngine\Client::TASK_STATE_WAITING` (`W`)
:   Attente de traitement par le moteur.

`\Dcp\TransformationEngine\Client::TASK_STATE_INTERRUPTED` (`I`)
:   Interrompue.

Si la transformation n'a pas pu être soumise au serveur (`$err` retourné !=
chaîne vide), la variable `$info` contient le code de l'erreur dans la
propriété `status` :

    [php]
    array(
        'status' => \Dcp\TransformationEngine\Client::error_connect |
                    \Dcp\TransformationEngine\Client::error_noengine |
                    \Dcp\TransformationEngine\Client::error_sendfile |
                    \Dcp\TransformationEngine\Client::error_emptyfile
    )

Les constantes de code d'erreur sont :

`\Dcp\TransformationEngine\Client::error_connect`
:   Le client n'a pu établir une connexion avec le serveur TE.
    
    Vérifier que les paramètres de connexion `TE_HOST` et `TE_PORT` sont
    corrects et que le client peut se connecter sur l'hôte et le port TCP du
    serveur TE).

`\Dcp\TransformationEngine\Client::error_noengine`
:   Le nom du moteur de transformation demandé n'est pas connu par le serveur TE.
    
    Vérifier que le moteur demandé est bien déclaré sur le sereur TE.

`\Dcp\TransformationEngine\Client::error_sendfile`
:   Le fichier n'a pu être envoyé correctement au serveur TE.
    
    Voir le message d'erreur retourné par `sendTransformation()` pour plus de
    détails sur l'erreur rencontrée.

`\Dcp\TransformationEngine\Client::error_emptyfile`
:   Le fichier soumis pour transformation n'existe pas ou est vide.
    
    Vérifier que le fichier `$filename` soumis pour transformation existe et
    n'est pas vide.

## Erreurs / Exceptions {#te-manex-ref:2cb14095-c8ca-47ed-a964-b2d8339baa61}

## Historique {#te-manex-ref:e06babd1-49b4-4f75-8434-b1a9789ddb95}

## Exemples {#te-manex-ref:f7e5d339-8900-4083-9665-c6cffa6f200d}

    [php]
    /* Un fichier texte */
    $filename = '/tmp/hello.txt';
    $textContent = 'Hello world.';
    file_put_content($filename, $textContent);
     
    /* On envoi une demande de conversion du fichier texte en PDF */
    $fkey = sprintf("test-conversion-%s", uniqid());
    $callback = '';
    $info = array();
    $err = $clientTE->sendTransformation(
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
    var_export($info);

    [php]
    array(3) {
      'tid'     => "54c8a79a4780d2.85897641",
      'status'  => "W",
      'comment' => "13 bytes read in 0.005 sec"
    }

## Notes {#te-manex-ref:d48d0769-3b39-48ff-880f-acaff8e34b4b}

## Voir aussi {#te-manex-ref:f8a477c3-d212-440d-89a3-1885782a2ff5}

- [`getInfo()`][Client::getInfo]

<!-- links -->
[Client::getInfo]: #te-manex-ref:b46f0d87-2f97-4455-8e63-5defa94d8857
