# Configuration Dynacase {#te-manex-ref:33a5475b-28e7-4b95-b0ea-a127e4794e93}

## Configuration de Dynacase pour utiliser TE {#te-manex-ref:476b37eb-727c-406b-9214-98c435470549}

Pour que Dynacase puisse utiliser TE pour la transformation, et l'indexation plein-texte de documents, il faut positionner les paramètres applicatifs suivants :

| Application             | Paramètre                                            | Valeur                              | Détail |
| -----------             | ---------                                            | ------                              | ------ |
| "bibliothèque dynacase" | "Adresse du serveur de transformation"               | `127.0.0.1`                         | L'adresse d'écoute du serveur TE. |
| "bibliothèque dynacase" | "Numéro de port du serveur de transformation"        | `51968`                             | Le port d'écoute du serveur TE. |
| "bibliothèque dynacase" | "Activation du serveur de transformation"            | `yes`                               | Activation/désactivation de l'utilisation du serveur TE par les transformation de documents Dynacase. |
| "bibliothèque dynacase" | "URL pour les callback du serveur de transformation" | Exemple : `http://ged.example.net/` | L'URL d'accès à votre serveur Dynacase par laquelle TE interagira en retour. |
| "bibliothèque dynacase" | "Activation de l'indexation plein texte avec TE"     | `yes`                               | Activation/désactivation de l'utilisation du serveur TE pour indexer les fichiers (n'est pris en compte que si "Activation du serveur de transformation" est positionné à "yes"). |
