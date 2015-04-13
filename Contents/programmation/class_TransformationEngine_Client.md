# Classe \Dcp\TransformationEngine\Client {#te-manex-ref:175f53d2-3322-464d-bb81-38479a776145}

La classe `\Dcp\TransformationEngine\Client` implémente l'API cliente pour
l'utilisation d'un serveur TE.

Cette API cliente est fourni par le module `dynacase-tengine-client`.

Elle permet de soumettre des fichiers pour transformation, récupérer le status
des tâches ainsi créés, récupérer le fichier produit par ces tâches et
interrompre des tâches.

## Constructeur {#te-manex-ref:e139f9a1-279a-4bad-9945-30bc6a70c268}

    [php]
    new \Dcp\TransformationEngine\Client( string $host, int $port )

### Liste des paramètres {#te-manex-ref:08ebc549-e31a-4e69-9ac6-586225288322}

(string) `host`
:   Le nom d'hôte (ou l'adresse IP) du serveur TE auquel doit se connecter le
    client.

(int) `port`
:   Le port TCP du serveur TE auquel doit se connecter le client.

### Exemples {#te-manex-ref:c3ffa584-65fb-4146-b4cc-972aa1b2fd4e}

L'adresse et le port renseignés dans les paramètres applicatifs de Dynacase
sont accessibles avec la fonction
`\ApplicationParameterManager::getScopedParameterValue()` et les paramètres
`TE_HOST` et `TE_PORT` :

    [php]
    $clientTE = new \Dcp\TransformationEngine\Client(
        \ApplicationParameterManager::getScopedParameterValue("TE_HOST"),
        \ApplicationParameterManager::getScopedParameterValue("TE_PORT")
    );

### Notes {#te-manex-ref:18e7feab-64ac-4fcc-85c6-434655694f85}

Aucune.

### Voir aussi {#te-manex-ref:9782a645-57c2-4f44-b2bb-4f35a3317802}

Aucun.

<!-- links -->
