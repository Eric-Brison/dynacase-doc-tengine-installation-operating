
# CLI de contrôle du TE {#te-manex-ref:218e260b-7cfa-4d53-a6c1-cb4c50f36f84}

## Démarrage/Arrêt des éléments de TE (`ted start`) (`ted stop`) {#te-manex-ref:560d0dfe-8b7e-4bba-8262-c847f38c1ef4}

Démarrage `ted start`
:  

Le script démarre les serveurs OpenOffice, TE (communication et transformation).

    [bash]
    # $TE_HOME/bin/ted start
    Starting OOO server... 27023
    Starting te_request_server... 27041
    Starting te_rendering_server... 27043
    Starting ted service:  OK

Le script ted s'occupe de lancer les trois composants, et affiche leur PID.
Les erreurs d'activation sont signalées sur la console de lancement.


Démarrage `ted stop`
:  

Le script démarre les serveurs.

    [bash]
    # $TE_HOME/bin/ted stop
    Stopping te_request_server... 27041
    Stopping te_rendrering_server... 27043
    Stopping OOO server... 27023
     * Stopping ted service:  OK


## Status des éléments de TE (`ted status`) {#te-manex-ref:8e25828d-711c-4162-ad5e-7b0fcc78ca4b}

    [bash]
    # $TE_HOME/bin/ted status
    Request server running (27041)
    Rendering server running (27043)
    OOO server running (27023)

Le script `ted` affiche pour chacun des trois composants s'ils tournent ou non, et leur PID.

## Vérification des moteurs de transformation (`ted check`) {#te-manex-ref:02b9cb27-aca7-4232-8ba5-0455fa43709d}

Le script `ted` permet de lancer une vérification des moteurs de transformations. Pour cela, il faut démarrer les composants (voir `ted start` ci-dessus), et ensuite exécuter la commande suivante :

    [bash]
    # $TE_HOME/bin/ted check
    
    * Checking conversion from ODT to PDF...
      Ok: '/tmp/test.odtn27155.pdf' (7957 bytes)
    
    * Checking conversion from ODT to PDF/A-1...
      Ok: '/tmp/test.odtQ27176.pdfa' (14430 bytes)
    
    * Checking conversion from ODT to TXT...
      Ok: '/tmp/test.odtu27199.txt' (22 bytes)
    
    [etc.]


<span class="flag inline nota-bene"></span> Si le nom d'hôte/nom de domaine du système est mal configuré, les temps de conversion peuvent être long du fait de timeouts de résolution de noms lors de la connexion au serveur OOo.
Pour corriger cela, assurez vous que le nom d'hôte (tel que retourné par la commande `hostname`) et le nom de domaine (tel que retourné par la commande `dnsdomainname`) sont corrects, et que le fichier `/etc/hosts` est correctement renseigné.

## Nettoyage des fichiers temporaires (`ted cleantmpfiles`) {#cleantmpfiles}

L'option `cleantmpfiles` permet de supprimer les fichiers temporaires nommés `tes-*` et `ter-*`, présents dans les répertoire `$REQUEST_DIRECTORY` et `$RENDERING_DIRECTORY` qui ont plus de 7 jours (valeur par défaut).

    [bash]
    # $TE_HOME/bin/ted cleantmpfiles

Si vous voulez spécifier votre propre durée, vous pouvez ajouter le nombre de jours après l'option `cleantmpfiles`.

Exemple pour supprimer les fichiers temporaires de plus de 15 jours :

    [bash]
    # $TE_HOME/bin/ted cleantmpfiles 15

<span class="flag inline nota-bene"></span> Pour effectuer le nettoyage régulièrement, vous pouvez exécuter cette commande à partir d'une crontab.

## Automatisation des démarrage/arrêt  {#te-manex-ref:c0a71f5b-d7e6-466d-b109-3e78ab407ebc}

Pour que TE démarre, et s'arrête, lors du démarrage, et l'arrêt, du système, il vous faut intégrer 
le script `ted` au système d'init de votre serveur.

Vous trouverez ci-dessous, un exemple pour les distributions de type RedHat et Debian.

Distribution de type RedHat
:   

Les distributions de type RedHat utilisent la commande `chkconfig` pour
administrer les scripts rc/init.

Faire un lien symbolique de `$TE_HOME/bin/ted` dans le répertoire `/etc/rc.d/init.d/` :

    [bash]
    # ln -sf $TE_HOME/bin/ted /etc/rc.d/init.d/ted

Enregistrer `ted` :

    [bash]
    # chkconfig --add ted
    # chkconfig ted on
    # chkconfig --list ted
    ted             0:arrêt 1:arrêt 2:marche        3:marche        4:marche        5:marche        6:arrêt

Distribution de type Debian
:   

Les distributions de type Debian utilisent la commande `update-rc.d` pour administrer les scripts rc/init.

Faire un lien symbolique de `$TE_HOME/bin/ted` dans le répertoire `/etc/init.d/` :

    [bash]
    # ln -sf $TE_HOME/bin/ted /etc/init.d/ted

Enregistrer `ted` :

    [bash]
    # update-rc.d ted defaults
    Adding system startup for /etc/init.d/ted ...
     /etc/rc0.d/K20ted -> ../init.d/ted
     /etc/rc1.d/K20ted -> ../init.d/ted
     /etc/rc6.d/K20ted -> ../init.d/ted
     /etc/rc2.d/S20ted -> ../init.d/ted
     /etc/rc3.d/S20ted -> ../init.d/ted
     /etc/rc4.d/S20ted -> ../init.d/ted
     /etc/rc5.d/S20ted -> ../init.d/ted
