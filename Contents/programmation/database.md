# La base de données {#te-manex-ref:bcd3079b-a552-42d0-9bcb-a28d164e2893}

## Liste des tables {#te-manex-ref:9cc63505-d570-4688-aae0-3f4397e3d572}

### Table `task`

La table `task` contient les demandes de transformation.

(text) `tid`
:   Identifiant de la transformation.

(text) `infile`
:   Chemin d'accès au fichier envoyé par le client pour conversion.

(text) `inmime`
:   Type MIME du fichier envoyé par le client pour conversion.

(text) `outfile`
:   Chemin d'accès du fichier produit par la transformation.

(text) `engine`
:   Nom du moteur de transformation a appliquer au fichier `infile`.

(char) `status`
:   Code de statut de la transformation (voir
    [Cycle de traitement des demandes][task_status]).

(text) `fkey`
:   Clef étrangère envoyé par le client pour la transformation
    (voir [Client::sendTransformation()][Client::sendTransformation]).

(text) `callback`
:   URL de callback envoyé par le client pour la transformation.

(text) `callreturn`
:   Contenu de la réponse à l'exécution de la requête de callback.

(timestamp) `cdate`
:   Timestamp de la création de la demande de transformation.

(integer) `pid`
:   Identifiant du processus du moteur lorsque la transformation est en cours
    de traitement.

(text) `comment`
:   Dernier commentaire de la transformation (mis à jour durant le déroulement
    de la transformation).

### Table `histo`

La table `histo` contient les messages d'information émis durant le traitement
de la transformation.

(text) `tid`
:   Identifiant de la transformation.

(timestamp) `date`
:   Timestamp du message.

(text) `comment`
:   Message/commentaire.

### Table `engine`

La table `engine` contient la liste des moteurs disponibles, les types MIME
supportés en entré ces moteurs et le chemin du moteur a exécuter.

(text) `name`
:   Nom du moteur de transformation.

(text) `mime`
:   Type MIME supporté par le moteur. Le type MIME peut être complet et dans ce
    cas le moteur est exécuté lorsque le type MIME du fichier correspond
    strictement (e.g. `application/x-foo` acceptera des fichiers de type
    `application/x-foo`), ou partiel (e.g. `application`) auquel cas le moteur
    sera utilisable pour tous les type MIME ayant la même base (e.g.
    `application` acceptera des fichiers de type MIME `application/x-foo` ou
    `application/x-bar`).

(text) `command`
:   Chemin d'accès au moteur a exécuter pour cette transformation.  (voir
    [Enregistrement d'un moteur][engine_creation]).

(text) `comment`
:   Commentaire/description à la description de l'auteur du moteur de
    transformation.

<!-- link -->
[task_status]: #te-manex-ref:099d71d4-76dc-41c9-aa19-9126fe7cb555
[Client::sendTransformation]: #te-manex-ref:adab03f2-0526-4d12-a28c-232a70a0fd8c
[engine_creation]: #te-manex-ref:3649a6a1-03eb-42d4-aa4c-1d473f081354
