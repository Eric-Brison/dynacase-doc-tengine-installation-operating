# Création d'un moteur {#te-manex-ref:87061b8c-19ca-4a7b-b370-706ae3fe1160}

## Protocole moteur {#te-manex-ref:fad5a3c6-0106-4363-b91f-f648d4c21d33}

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

## Enregistrement d'un moteur {#te-manex-ref:3649a6a1-03eb-42d4-aa4c-1d473f081354}

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



<!-- links -->
[MIME_type]: #te-manex-ref:3e1d421e-9cb9-48f5-8c86-dd873a970eec
[workflow]: #te-manex-ref:099d71d4-76dc-41c9-aa19-9126fe7cb555
