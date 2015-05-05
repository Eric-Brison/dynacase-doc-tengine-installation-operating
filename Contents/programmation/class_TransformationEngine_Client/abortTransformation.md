# Client::abortTransformation {#te-manex-ref:3786b240-ab1b-4f6f-9993-eafc95a9eed6a}

<div class="short-description">
Interrompre une tâche.
</div>

## Description {#te-manex-ref:f92c74c7-6bc5-42ef-9013-7b6e946fb84e}

    [php]
    string abortTransformation ( string $tid )

Permet d'interrompre le traitement d'une tâche.

### Avertissements {#te-manex-ref:6f858191-a57e-499a-8e65-fc4c5d190948}

## Liste des paramètres {#te-manex-ref:e3a86fb8-bb7d-4cb4-a976-c2cb5a2b2efe}

(string) `tid`
:   Identifiant de la tâche dont on souhaite interrompre le traitement.

## Valeur de retour {#te-manex-ref:2b7ec02a-1c39-42c3-97dd-e5e9161c29f3}

La méthode `abortTransformation()` retourne une chaîne non vide contenant le
message d'erreur si l'opération n'a pu être réalisée, ou une chaîne vide si
l'opération est réussie.

## Erreurs / Exceptions {#te-manex-ref:a2161dea-aacf-441f-8eb0-1cc8d745f8fa}

La méthode retourne une chaîne non vide contenant le message d'erreur si
l'opération n'a pu être réalisée.

## Historique {#te-manex-ref:6c6d154d-86c8-4580-aaa0-3154205da8a0}

## Exemples {#te-manex-ref:0b371375-bac2-4804-b39b-db829adbdc8c}

    [php]
    $tid = '54c8a79a4780d2.85897641';
    $err = $clientTE->abortTransformation(
        $tid /* Identifiation de la tâche qu'on souhaite interrompre */
    );
    if ($err != '') {
        throw new Exception(sprintf("abortTransformation() returned with error: %s", $err));
    }

## Notes {#te-manex-ref:ee242633-5757-4247-90cf-0fd98a61c115}

L'interruption de la tâche :

* tue le processus de rendering si ce dernier est actif (`pid` de la tâche !=
  0) ;
* les fichiers `infile` et `outfile` de la tâche sont conservés jusqu'à ce que
  la tâche soit supprimée.

## Voir aussi {#te-manex-ref:2d6aed21-e289-40c2-972c-68288fe892dc}

- [`getTransformation()`][Client::getTransformation]
- [`getInfo()`][Client::getInfo]

<!-- links -->
[Client::getTransformation]: #te-manex-ref:53d82303-761a-4160-8477-2a6bbbfef2c9
[Client::getInfo]: #te-manex-ref:b46f0d87-2f97-4455-8e63-5defa94d8857
