# Client::getTransformation {#te-manex-ref:53d82303-761a-4160-8477-2a6bbbfef2c9}

<div class="short-description">
Récupérer le fichier produit par la transformation.
</div>

## Description {#te-manex-ref:49d548ac-8635-4a04-a923-1cf926f7eba8}

    [php]
    string getTransformation ( string $tid,
                               string & $outputFile )

Permet de récupérer dans le fichier local `$outputFile` le résultat de la
transformation d'identifiant de tâche `$tid`.

### Avertissements {#te-manex-ref:69460e66-c295-4dfe-a98f-f9fea6fb4b5a}

## Liste des paramètres {#te-manex-ref:81cc6ffc-90fc-4853-b1e9-7c686febae1e}

(string) `tid`
:   Identifiant de la tâche dont on souhaite obtenir le fichier résultant.

(string) `outputFile`
:   Chemin local du fichier dans lequel sera stocké le fichier résultant de la
    tâche.

## Valeur de retour {#te-manex-ref:96e8d739-6182-43e6-a5b1-0c8ea25db9af}

La méthode `getTransformation()` retourne une chaîne non vide, contenant le
message d'erreur, si l'opération n'a pu être réalisée, ou une chaîne vide si
l'opération est réussie.

## Erreurs / Exceptions {#te-manex-ref:ab45ba00-af22-4198-8d40-26a3b57d7580}

La méthode retourne une chaîne non vide contenant le message d'erreur si
l'opération n'a pu être réalisée.

## Historique {#te-manex-ref:b884f641-87c6-47f9-9517-10c362419e9e}

## Exemples {#te-manex-ref:8e28a818-5ac0-495a-8c6a-af381e1b9c17}

    [php]
    $tid = '54c8a79a4780d2.85897641';
    $tmpFile = tempnam(getTmpDir(), 'test');
    $err = $te->getTransformation(
        $tid,    /* Identifiant de la tâche dont on souahite récupérer le résultat */
        $tmpFile /* Fichier dans lequel inscrire le résultat */
    );
    if ($err != '') {
        throw new Exception(sprintf("getTransformation() returned with error: %s", $err));
    }
    printf("Fichier '%s' : %d octets\n", $tmpFile, filesize($tmpFile));

    Fichier '/var/www/dynacase/var/tmp/dcp/test1f3ic0' : 2729 octets.

## Notes {#te-manex-ref:aedb722a-2b34-4fd5-ae9f-303c64256a18}

## Voir aussi {#te-manex-ref:6a098ed1-728b-440e-833c-e8c4d6c908c1}

- [`sendTransformation()`][Client::sendTransformation]
- [`getInfo()`][Client::getInfo]

<!-- links -->
[Client::sendTransformation]: #te-manex-ref:adab03f2-0526-4d12-a28c-232a70a0fd8c
[Client::getInfo]: #te-manex-ref:b46f0d87-2f97-4455-8e63-5defa94d8857
