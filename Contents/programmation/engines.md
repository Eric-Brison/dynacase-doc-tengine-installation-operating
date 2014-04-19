# Les moteurs {#te-manex-ref:89f916ab-453d-4cde-be2a-c711b0c5e12e}

## Création d'un engin {#te-manex-ref:e55dbdf7-c386-4565-894b-8a521f79f2f5}


Un moteur est un programme (script ou exécutable) qui prend en entrée deux arguments :

* le path du fichier à transformer,
* le path du répertoire où déposer le résultat de la transformation (fichier)

Le script doit retourné un status interprété de la manière suivante :

* *0*: pas d'erreur <span class="flag fixme">à confirmer</span>
* *<> 0*: le moteur est noté en échec  <span class="flag fixme">qu'est il fait des fichiers produits</span>

Les sorties `stdout` et `stderr` sont capturées par le process TE lançant le moteur. Elles sont consignées dans le journal d'exécution du moteur et sont consultables depuis l'interface de surveillance.


## Installation d'un moteur {#te-manex-ref:063945f8-9576-4e7a-94be-a18b7d80e48b}

Le script ou exécutable est déposé dans le répertoire `lib/engines/` du TE.  
Il doit être exécutable.

Il faut le déclarer auprès du TE via la requête sur la base TE :

    [sql]
    INSERT INTO engine (name, mime, command, comment) VALUES ('<mon_moteur>', '<le_type_mime_applicable>', '@TE_HOME@/lib/engines/<nom_du_script>', '<mon_commentaire>');

Avec :

* `<mon_moteur>`: nom présenté du moteur
* `<le_type_mime>` : le type mime sur lequel s'applique le moteur

Si un moteur s'applique sur plusieurs types mime, il est nécessaire de trouver autant de déclaration que de couple moteur/type mime.  
Pour plus d'information sur les types mimes, vous pouvez vous reporter au paragraphe [Configuration du serveur TE > Type mimes](#te-manex-ref:3e1d421e-9cb9-48f5-8c86-dd873a970eec)

