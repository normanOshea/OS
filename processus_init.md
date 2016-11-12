#Le processus *init*



###Définition

Une fois que le noyau est lancé et initialisé, il lance la première application de l'espace utilisateur, qui est typiquement */sbin/init* (mais qui peut n'être qu'un simple script dans certains systèmes embarqués n'ayant pas besoin de l'initialisation complète d'*init*). C'est le premier programme invoqué qui est compilé avec la librairie C standard - jusqu'à maintenant dans le démarrage du système, aucune autre application C n'a été exécutée.

Le rôle du processus *init* est de gérer tous les processus (dont les tâches du noyau). Les processus portent tous un numéro d'identification PID (*Process ID*), attribués dans l'ordre. Comme les répertoires, les processus sont organisés en arbre, dont *init* est la racine (PID=1).



###Le fichier de configuration */etc/inittab*

Ceci est le fichier de configuration du processus *init*. Il contient des lignes au format suivant :

`code:niveau:action:commande`

Le champ `code` contient une séquence de un à quatre caractères (deux pour compatibilité) unique pour identifier la ligne dans le fichier.

Le champ `niveau` donne le niveau d'exécution pour lequel cette ligne doit être prise en compte. La notion de niveau d'exécution permet de spécifier des configurations d'exécution différentes. Un standard existe et est résumé dans la table ci-dessous. (Il est possible de spécifier plusieurs niveaux lorsque la commande associée doit être lancée à différents niveaux d'exécution.)

| Niveau | Description                                                              |
| :----: | ------------------------------------------------------------------------ |
| 0      | arrêt de la machine                                                      |
| 1      | mode mono-utilisateur, seul le super-utilisateur peut se connecter       |
| 2      | mode multi-utilisateurs, avec peu de services réseaux                    |
| 3      | mode multi-utilisateurs, avec tous les services réseaux (mode par défaut)|
| 4      | définissable par l'utilisateur                                           |
| 5      | démarrage de X11 au boot                                                 |
| 6      | redémarrage de la machine                                                |

Le champ `action` définit la manière d'exécuter la commande du champ commande. Le tableau ci-après présente les actions les plus courantes :

| Action      | Description                                                                                                 |
| ----------- | ----------------------------------------------------------------------------------------------------------- |
| respawn     | relance la commande lorsqu'elle se termine                                                                  |
| wait        | attend la fin de la commande avant de continuer                                                             |
| once        |	la commande est exécutée une fois                                                                           |
| boot        |	la commande est exécutée au démarrage du système (le champ niveau est ignoré)                               |
| bootwait    |	comme ci-dessus avec attente                                                                                |
| off	      | ne rien faire (permet de conserver la ligne pour une utilisation future)                                    |
| initdefault |	permet de spécifier le niveau d'exécution par défaut                                                        |
| sysinit     |	la commande est exécutée au démarrage avant celles des directives boot et bootwait                          |
| ctrlaltdel  |	la commande est exécutée lorsque l'utilisateur tape les trois caractères <CTRL>-<ALT>-<SUPPR> sur le clavier|
| powerfail   | la commande est exécutée lorsque le processus init reçoit le signal SIGPWR (défaut d'alimentation)          |



###Le répertoire de scripts */etc/rc.d*

Ce répertoire contient les scripts utilisés pour l'initialisation du système. Ils sont prévus pour démarrer les différents services et processus et effectuer quelques vérifications de configuration.

La table suivante présente les différents scripts :

| Niveau     | Description                                                         |
| -----------| ------------------------------------------------------------------- |
| rc.sysinit | exécuté une fois au démarrage pour initialiser le système           |
| rc         | script de gestion du niveau d'exécution (reçu en paramètre)         |
| rc.local   | script utilisé pour les initialisations particulières à la machine  |
| init.d     | répertoire contenant les scripts d'initialisation des sous-systèmes |
| rc0.d, rc1.d, rc2.d, rc3.d, rc4.d, rc5.d et rc6.d | répertoires contenant des liens sur les scripts du répertoire init.d devant être lancés à un niveau d'exécution particulier                             |

Le fichier rc.sysinit réalise les opérations suivantes :
- initialise la variable PATH pour les autres scripts
- active la partition de swap
- initialise le nom du système (hostname)
- vérifie l'intégrité du système de fichiers
- démarre la gestion des quotas
- initialise le "Plug and Play"
- prépare la gestion des modules
- initialise l'horloge système
- détruit les fichiers de verrouillage

Le fichier rc exécute les scripts du répertoire rcN.d où N correspond au niveau d'exécution. Ces scripts sont des liens symboliques sur les fichiers de démarrage des sous-systèmes du répertoire init.d. Le lien reprend le nom du fichier d'origine précédé de la lettre S et d'un nombre pour les scripts de démarrage ou de K et d'un nombre pour les scripts d'arrêt du sous-système. La valeur numérique permet de spécifier l'ordre d'exécution des scripts.

Les scripts du répertoire init.d. peuvent être appelés par la suite pour forcer un arrêt ou un démarrage d'un sous-système. La syntaxe utilisée est :

`/etc/rc.d/init.d/script [start | stop | restart]`

La plupart des démons du système enregistrent leur numéro de processus dans un fichier du répertoire /var/run.

Après l'exécution des scripts, le processus init lance les processus en mode respawn. Ce sont, en général, les programmes d'invitation à l'ouverture de session. Ces programmes doivent être relancés dès qu'une session se termine.



####Sources :
- [IBM](http://www.ibm.com/developerworks/linux/library/l-linuxboot/)
- [Linuxpédia](http://www.linuxpedia.fr/doku.php/util/boot)
- http://daniel.galvao.free.fr/astrid/coursLinux/x1656.html

