# Client::purgeTransformation {#te-manex-ref:2f1845d6-ebf9-4aa1-ab99-3343c72e6b38}

<div class="short-description">
Supprime la demande de transformation.
</div>

## Description {#te-manex-ref:401ac361-60c5-468f-90e7-8e713d9edc83}

    [php]
    string purgeTransformation ( string $tid, )

Permet de supprimer une demande de transformation.

### Avertissements {#te-manex-ref:dac2047a-7de3-417a-992f-fd0e36e87621}

La suppression ne vérifie pas si la tâche est en cours d'exécution. Par
conséquent, si la demande est en cours d'exécution il faut au préalable
l'interrompre (voir méthode
[`abortTransformation()`][Client::abortTransformation]).

## Liste des paramètres {#te-manex-ref:6dbb98ac-0249-43c1-a89e-e54c12d7f492}

(string) `tid`
:   Identifiant de la tâche qu'on souhaite supprimer.

## Valeur de retour {#te-manex-ref:5d0d8498-c62a-40d9-9511-a810b32e5cca}

La méthode `getTransformation()` retourne une chaîne non vide, contenant le
message d'erreur, si l'opération n'a pu être réalisée, ou une chaîne vide si
l'opération est réussie.

## Erreurs / Exceptions {#te-manex-ref:5257ffdf-6cd9-4817-baeb-c580dacea5af}

La méthode retourne une chaîne non vide contenant le message d'erreur si
l'opération n'a pu être réalisée.

## Historique {#te-manex-ref:7df8f27e-cb5c-4bd7-94a9-345653ef71cc}

## Exemples {#te-manex-ref:78d10f99-2dd6-4d65-b852-53ed3ad46007}

    [php]
    $tid = '54c8a79a4780d2.85897641';
    $err = $te->getInfo($tid, $info);
    if ($info['status'] == \Dcp\TransformationEngine\Client::TASK_STATE_PROCESSING) {
        throw new Exception("La transformation est en cours d'exécution. Veuillez d'abord l'interrompre.");
    }
    $err = $te->purgeTransformation($tid)
    if ($err != '') {
        throw new Exception(sprintf("Erreur lors de la suppression de la transformation '%s': %s", $tid, $err));
    }

## Notes {#te-manex-ref:3350f6fb-7bfc-40eb-8cba-4bb7267f406a}

## Voir aussi {#te-manex-ref:6a098ed1-728b-440e-833c-e8c4d6c908c1}

- [`getInfo()`][Client::getInfo]
- [`abortTransformation()`][Client::abortTransformation]

<!-- links -->
[Client::getInfo]: #te-manex-ref:b46f0d87-2f97-4455-8e63-5defa94d8857
[Client::abortTransformation]: #te-manex-ref:3786b240-ab1b-4f6f-9993-eafc95a9eed6a
