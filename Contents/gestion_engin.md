*[TE]: Dynacase Transformation Engine

# Gestion des engins

## Création d'un engin

Un engin TE est un script qui prend en entrée deux valeurs :

* 1er argument : le path du fichier à transformer,
* 2ème argument : le path où déposer le fichier un fois transformé

## Ajout/Suppression d'un engin

Une fois le script conçu, il doit être déposé dans le répertoire `lib/engines/` du TE, être exécutable, et être inscrit en base de la manière suivante :

    [sql]
    INSERT INTO engine (name, mime, command, comment) VALUES ('<mon_moteur>', '<le_type_mime_applicable>', '@TE_HOME@/lib/engines/<nom_du_script>', '<mon_comentaire>');

NB : la liste des engins disponibles sur le TE est accessible en parcourant la table *engine* de la base TE.