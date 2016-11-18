##Mise en route d'un OS Linux

**Le processus de démarrage d'un système d'exploitation Linux fonctionne selon plusieurs étapes telles que :**
* Lecture du BIOS
* Lecture du MBR ou Master Boot Record
* Lancement du chargeur Grub ou Lilo
* Chargement du noyau Linux en mémoire avec chargement de certains drivers via initrd
* Lancement du processus init

#Le BIOS

###Définition

Le BIOS ou en anglais *Basic Input Output System* (système d'entrée sortie de base) est un composant essentiel à tout PC, il se trouve généralement dans une mémoire morte ou ROM qui est directement implantée sur la carte mère du PC. Il est associé à une mémoire sauvegardée par une petite pile bouton sur la carte mère (le “setup” qui est la sauvegarde de la configuration).  Le BIOS permet d'effectuer des opérations élémentaires lors de sa mise sous tension. Par exemple la lecture d'un secteur sur un disque. Par extension, le terme est souvent utilisé pour décrire l'ensemble du micrologiciel de la carte mère.

Si votre PC ne démarre pas (ou ne boot pas) c'est à cause du BIOS et de sa configuration (le setup). On peut accéder au setup et le modifier en pressant une touche dès la mise sous tension du PC. Selon les fabricants, cette touche est Suppr, F2, F10 … (la touche à utiliser est souvent très brièvement affichée au tout début du démarrage).

![Pile](http://www2.futaie.org:4280/~rousselotv/lp/pile-bios-ordinateur.png)

*La pile bouton d'une carte mère*


###Rôle du BIOS

Le BIOS teste le matériel et y applique les réglages mémorisés dans le setup, tout en s'assurant qu'il n'existe pas de dysfonctionnement matériel et que tout est présent dans la machine, mémoire CPU principalement. Ensuite il regarde la présence des périphériques nécessaires au boot : lecteur de disquette, cd rom, dvd rom, clef usb, mais surtout disque dur. Si vous avez installé un système d'exploitation Linux ou Windows, le BIOS est normalement configuré pour activer le MBR (Master Boot Record ou *zone amorçe* qu'on verra dans un autre chapitre) de celui ci, les étapes du démarrage peuvent alors commencer….

Il peut aussi fournir un ensemble de services permettant de faire le plus possible abstraction de la couche matérielle. Ces services sont utilisés par le système d'exploitation et sont accessibles via certaines zones mémoires (40:00 par exemple) ou certaines interruptions logicielles (10h, 11h, 12h et 13h notamment). Cependant, dans la pratique, les systèmes d'exploitation modernes n'utilisent ces services que lors de l'amorçage. Ils utilisent ensuite soit leurs propres pilotes, soit les fonctions ACPI pour les opérations liées à la carte mère.

Peu importe comment le fabricant a développé cette carte mère (quels composants il a choisi, comment fonctionne la partie  hardware de cette carte mère), en utilisant les mêmes fonctions du BIOS sur deux cartes mères différentes, on obtiendra le même résultat. Les systèmes d'exploitation peuvent utiliser ces fonctions, au moins pendant les premières étapes du démarrage de l'ordinateur, et ensuite lors du fonctionnement normal.

![Bios](http://www2.futaie.org:4280/~rousselotv/lp/Award_BIOS_setup_utility.png)

*Aperçu de l'écran d'un BIOS*

###Fonctionnalités

Le BIOS comprend également le logiciel nécessaire à l'amorçage de l'ordinateur. La première phase de l'amorçage (boot) est l'auto-configuration à l'allumage (POST, Power-On Self-Test), qui compte la quantité de mémoire, teste les disques et configure les composants. La séquence d'amorçage continue avec la recherche d'un système d'exploitation, avant de le lancer.

Le BIOS prend en charge à bas niveau les communications avec les périphériques, néanmoins le système d'exploitation peut aussi s'adresser directement aux périphériques s'il le juge nécessaire. Parmi les prises en charge offertes par le BIOS, il y a celle du clavier, au moins dans un mode (pas forcément l'USB) et celle d'un mode d'affichage simplifié.

Le BIOS contient également des outils de diagnostic pour vérifier sommairement l'intégrité des composants critiques comme la mémoire, le clavier, le disque dur, les ports d'entrée/sortie, etc.

Certains paramètres du BIOS peuvent être réglés par l'utilisateur (ordre des périphériques à scruter pour détecter une zone de boot, type et fréquence du processeur, etc.). L'ensemble de ces paramètres est stocké de manière permanente grâce à une mémoire de taille réduite (quelques centaines d'octets) à faible consommation (type CMOS) alimentée par une pile (généralement au lithium) présente sur la carte mère. Cette mémoire est communément appelée, par abus, « CMOS ».

Le BIOS est presque toujours développé par le fabricant de la carte mère à partir d'un code de référence fourni par un constructeur de BIOS (tel que AMI ou Phoenix Technologies) car il contient les paramètres spécifiques à l'initialisation de la carte mère, tels que la configuration des ports GPIO.

###Failles potentielles du BIOS

Le BIOS étant le firmware lancé au démarrage, il peut être la cible de logiciels malveillants. En 1991, le BIOS a connu sa première attaque d'envergure avec la propagation du virus CIH qui était très agressif puisqu'il effaçait l'intégralité du contenu des disques. L’utilisation permanente d'un logiciel anti-virus efficace et mis à jour est la meilleure parade à ce type d'attaque.

Depuis que les mises à jour du BIOS peuvent être réalisées à la volée via le programmateur interne de la carte mère, il est possible que ceux-ci soient la cible d'attaques de type *man in the middle*.



#MBR

##Master Boot Record

###Définition

Le MBR *Master Boot Record*, ou parfois *Zone amorce*, est une partie d'un disque dur, d'une clé USB ou d'un média amovible quelconque.
Plus précisément, c'est le **premier** 'secteur' adressable d'un disque dur. Il commence par l'adresse mémoire zero et sa taille est de 512 octets.

Sa présence est éssentiel pour accéder aux systèmes de fichiers présents et en cas de corruption de ce dernier, il sera impossible d'accéder aux données ou, dans une moindre mesure, impossible de redimensionner les partitions.

Accéder aux adresses mémoire du **MBR** est délicat, et la moindre modification peut corrompre tout le système de fichier. Son accès en lignes de commandes ne peut s'effectuer qu'en mode superutilisateur (sudo).

###Structure du MBR

Le MBR contient une routine d'amorçage dont le but est de charger le système d'exploitation (ou le *boot loader* si il existe) présent sur la partitions active.

Le MBR fait partie de l'enssemble *chargeur* ou *loader* et il a pour fonction de lancer le boot secondaire.

####En détails :

Le MBR est composé de trois zones :
-Les 446 premiers octets correspondent au code exécutable démarré juste après le BIOS.
-Les 64 octets suivants constituent la table de partition.
-Les 2 derniers octets contiennent toujours 55AA.

On peut afficher le MBR est en voir le détails grâce à la commande :

* __sudo dd if=/dev/sda bs=512 count=1 | hexdump -C__ *

En claire :
  En tant que superutilisateur __sudo__ je demande de faire une copie par blocs __dd__ avec comme source __if__ le matériel __/dev/sda__ (premier disque dur), je précise que le bloc désiré doit faire 512 octets et que je ne copie qu'un bloc.
  La sortie par défaut est ensuite redirigée __| (pipe)__ dans la moulinette __hexdump__ qui formate les données sous la forme adresse, valeurs hexadécimale de 16 octets consécutifs et caractères ascii correspondants.

Sur mon disque dur, voici le résultat :

![MBR sous linux](http://www2.futaie.org:4280/~rousselotv/lp/cap1) *Affichage du MBR sous linux*

On remarque les valeurs **55AA** à la fin du MBR. C'est la *marque* d'un media amorçable.

#####Partie exécutable

  La première partie, adresses comprises entre **000** et **1b7**, correspond à la partie exécutable du mbr. Elle est chargée en mémoire vive (ainsi que le reste du mbr) lorsque le PC démarre ("boote") sur ce disque. Elle a pour fonction de vérifier la validité de la table des partitions et de rechercher une partition amorçable du disque.

#####Identifiant du disque

  L'identifiant du disque se situe entre **1b8** et **1bb**. Soit les octets : **12 ed 07 00**.

#####Le partitionnement

  Le partitionnement du disque démmarre à l'adresse **1be** et chaque partitions à une longueur de 15 octets.


###Problèmes connus

  Il arrive parfois que la table des partitions soit corrompue pouvant empêcher tout redimensionnement. Ceci peut être la conséquence d'un virus de mbr, de l'utilisation d'un outil pour partitionner inadapté…
  Un cas classique est le chevauchement des partitions, on aura par exemple sda1 qui se terminera dans la partition sda2 si ta taille indiquée de sda1 est trop grande.

  Ces problèmes peuvent aussi affecter les partitions logiques et générer aussi un blocage. Il arrive aussi que la taille indiquée d'une partition excède la taille du disque.




## **LILO et GRUB**
----------


LILO et GRUB sont des bootloader ("chargeurs d'OS"), plus précisement un petit logiciel qui a la capacité de lancer plusieurs systèmes d'exploitation. Concrètement, cela laissera le choix aux démarrage entre vos différents systèmes d'exploitations
### **LILO**
----------
LILO est le plus souvent chargé dans le premier petit bout du disque dur appelé MBR (Master Boot Record).
Ainsi, LILO est appelé juste apès le chargement du BIOS.
Vous devez donc préciser dans le fichier lilo.conf tous les OS présents dans votre machine et leur partition associée (si vous avez Windows, la partition associée est souvent /dev/hda1 ), vous devez également préciser l'OS qui va se charger par défaut et le temps que va attendre LILO pour vous laisser le choix (infini si vous touchez le clavier).
La configuration de LILO se situe dans le fichier /etc/lilo.conf
Après avoir modifié ce fichier, ne pas oublier de relancer la commande:
`#Lilo                                         `
Codes erreurs affichés par LILO

| Etape                                                                                                               	| Affichage en cas d'erreur |
|:---------------------------------------------------------------------------------------------------------------------:|---------------------------	|
| Le chargeur d'amorçage de la partie 1 est chargé en mémoire et démarré                                              	| L                         |
| Le chargeur d'amorçage de la partie 2 est chargé en mémoire                                                         	| LI                        |
| Le chargeur d'amorçage de la partie 2 a été chargé à une adresse incorrecte                                         	| LIL?                      |
| Démarrage de la partie 2 du chargeur d'amorçage OK, mais la GDT (table des descripteurs de segment) est introuvable 	| LIL                       |
| La GDT est corrompue                                                                                                	| LIL-                      |
| Toutes les étapes de LILO se sont déroulées correctement                                                            	| LILO                      |

![interface lilo](http://www2.futaie.org:4280/~rousselotv/lp/lilo.png "inteface lilo")

### **GRUB**
GNU GRUB (acronyme signifiant en anglais « GRand Unified Bootloader »). C'est un logiciel libre. Il permet l'amorçage de systèmes GNU/Linux ou Windows (ainsi que d'autres systèmes moins répandus FreeBSD et OpenBSD, Hurd, Solaris), la lecture de la configuration au démarrage (pas besoin de réinstaller GRUB dans le secteur d'amorçage après un changement de configuration, contrairement à LILO), une ligne de commande permettant de changer la configuration au démarrage et surtout la reconnaissance en natif de divers systèmes de fichiers existants.  Langage de commande simple permettant de « rattraper » un amorçage qui se serait mal passé, à la suite du mauvais adressage d'une partition. Il est beaucoup plus volumineux que LILO.
#####**Fonctionnalités**

 *   Contrairement à LILO, GRUB n'a pas besoin d'être réinstallé pour mettre à jour sa configuration. GRUB prend en compte les modifications de son fichier de configuration dynamiquement.
 *  Au cas où le fichier de configuration serait incorrect, GRUB peut fournir un interpréteur de commandes pour permettre à l'utilisateur de charger un système d'exploitation manuellement.
 *     GRUB est très portable : il permet de charger aussi bien des systèmes compatibles avec le multiboot que des systèmes non-compatibles avec cette fonction (comme Microsoft Windows). GRUB prend en charge en outre beaucoup de systèmes de fichiers comme ext3, VFAT ou NTFS. GRUB est également compatible avec le mode Logical block addressing (LBA).
 *     GRUB peut être utilisé avec différentes interfaces. Beaucoup de distributions GNU/Linux utilisent la prise en charge graphique de GRUB pour afficher au démarrage de l'ordinateur un menu avec une image de fond, et parfois une prise en charge de la souris.
 *     GRUB peut télécharger des images de systèmes d'exploitation depuis un réseau, ce qui permet d'amorcer un système sur les ordinateurs sans disques. GRUB peut décompresser ces images pour les charger ensuite.
 *     GRUB prend également en charge des systèmes d'exploitation non Linux via le chargement en chaîne (chain loading) d'un autre bootloader tel ceux de Windows, ou une autre version de lui-même (coexistence entre Grub Legacy (version 1) et Grub2).

#####**Configuration basique**
La configuration n'est pas extrêmement complexe, il faut connaître :

le périphérique où se trouve le noyau (kernel) :
* hd0 : correspond au premier disque,
* hd1 : correspond au deuxième disque,
* hd0,2 : correspond au premier disque, 3e partition Grub Legacy, 2e partition Grub23

Le nom du kernel.
Le nom du initrd si besoin.
Puis : boot.

Grâce à un système de prompt interactif, GRUB gère la complétion, ceci permet de retrouver les noms de disque/kernel/initrd si le menu de chargement ne fonctionne plus.

![interface grub](http://www2.futaie.org:4280/~rousselotv/lp/grub.png "inteface grub")

#Le Kernel 
##Une définition
Un noyau de système d’exploitation, ou simplement noyau, ou kernel (de l'anglais), est une des parties fondamentales de certains systèmes d’exploitation. Il gère les ressources de l’ordinateur et permet aux différents composants — matériels et logiciels — de communiquer entre eux.

En tant que partie du système d’exploitation, le noyau fournit des mécanismes de traitements du matériel, notamment de la mémoire, du  processeur, et des échanges d’informations entre logiciels et périphériques matériels. Le noyau facilite la communication entre les processus. Son rôle central impose par ailleurs des performances élevées. Cela fait du noyau la partie la plus critique d’un système d’exploitation et rend sa conception et sa programmation particulièrement délicates.



##L'utilité d'un kernel
La fonction principale du noyau est de distribuer l'accès aux ressources de l'ordinateur :

 - L'unité centrale de traitement
 - Mémoire vive
 - La gestion du matériel
 -- Claviers 
 -- Souris 
 -- Imprimantes 
 -- Écrans
 -- Etc...
 
Mais un noyau remplis beaucoup d'autres fonctions essentielles au bon fonctionnement d'un ordinateur, nous allons donc voir les principales.

### Gestion de la Mémoire
<img align="left" src="https://upload.wikimedia.org/wikipedia/commons/thumb/f/ff/Memoire_virtuelle.svg/220px-Memoire_virtuelle.svg.png">
<break>
<break>
<break>
Le noyau a l'accès complet à la mémoire du système et doit permettre aux processus d'avoir un accès sans risque à cette mémoire et l'utiliser comme ils l'exigent. Linux utilise l’adressage virtuel, cela permet au kernel de substituer une adresse mémoire physique en mémoire virtuelle qu'il va donner aux programmes.


<break>
<break>
<break>
<break>
<break>
###L'Ordonnanceur
 L’ordonnanceur choisit l'ordre d'exécution des processus sur les processeurs d'un ordinateur.
 <img align="right" src="https://upload.wikimedia.org/wikipedia/commons/thumb/f/f1/Ordonnanceur_noyau_os.svg/300px-Ordonnanceur_noyau_os.svg.png">
 
Celui de Linux est _le Completely Fair Scheduler_, ou ordonnanceur complètement équitable.
C'est un ordonnanceur de tâches qui a fait son apparition avec la version 2.6.23 sortie le 9 octobre 2007, remplaçant ainsi le précédent ordonnanceur qui était apparu dans le noyau 2.5.2-pre10 en janvier 2002.

Il gère l'allocation de ressource processeur pour l'exécution des processus, en maximisant l'utilisation globale du CPU.

<break>
<break>
##Le boot d'un Kernel sous Linux, une courte explication
Quand le noyau est chargé, il initialise immédiatement et configure la mémoire de l'ordinateur et configure le matériel divers attaché au système, y compris tous les processeurs et des dispositifs de stockage. Il cherche alors l'image compressée **_initrd _** dans un emplacement prédéterminé dans la mémoire, la décompresse , la monte et charge tous les drivers nécessaires. 

Ensuite, il initialise des dispositifs virtuels liés au système de fichiers, comme LVM ou le RAID logiciel avant le démontage de l'image de disque  **_initrd _** et la libération en haut toute la mémoire l'image de disque une fois occupée.
Le noyau crée alors un une racine, monte celle-ci en lecture seule et libère n'importe quelle mémoire inutilisée.

À ce point, le noyau est chargé dans la mémoire et opérationnel. Cependant, il n'y a aucune applications d'utilisateur qui permet d'utiliser le système. Donc, le noyau exécute le programme de/sbin/init pour configurer l'environnement d'utilisateur

##L'initialisation du kernel Linux
###La routine d'initialisation
Le **_boot loader_** passe deux pièces d'informations pour l'initialisation de la routine de boot du noyau :

 1. Un indicateur sur le début de la RAM sur le disque.
 2. Un pointeur vers une chaine de caractère contenant des paramètres pour initialiser le noyau. Cette chaîne de caratère est appellée **_kernel command-line parameters_**

Ceux-ci seront sauvegardés dans des variables du noyau pendant la première initialisation.
Un dossier contenant le noyau, compressé pour sauver l'espace sur le disque, est décompressées grâce à une routine d'assembleur qui décompressera le noyau et transférera ensuite le contrôle au "vrai"  noyau.
L'initialisation du vrai noyau commence alors.

###L'initialisation du noyau et du système
Cette initialisation va suivre une suite d'étape précise pour permettre de lancer le système:

- Le noyau initialise le matériel ainsi que des structures de données du noyau.

- La [command line](https://www.kernel.org/doc/Documentation/kernel-parameters.txt) du noyau est sauvegardée.
- Les informations du disque dur sont récupéré à partir du BIOS.

- La taille de mémoire est déterminée, de nouveau grâce au BIOS.

- L'ordinateur est préparé pour se passer en mode protégé. (Le mode Protégé est le mode de mémoire normal dans lequel le système fonctionne. Il permet, en autre, d'utiliser la mémoire virtuelle. Le mode Protégé est aussi le mode qui permet aux programmes individuels de fonctionner isolément l'un de l'autre pour qu'un programme ne puisse pas accidentellement ou avec malveillance endommager un autre programme.

- Les registres de segment sont mis et la Table Descriptive des Interruptions (**_Interrupt Descriptor Table_**) est initialisée. Celle-ci sont utilisés dans le mode protégé. Les registres de segment sont utilisés avec la mémoire virtuelle. La Table Descriptive des Interruptions est une liste de routines dans le noyau auxquelles on donne le contrôle quand une interruption arrive. 
- Toute interruptions sont désactivées.

- Le bit ** "Protected Mode Enabled" ** est mis à 1. Ce pas allume en réalité le mode protégé.

- Les tables d'index sont initialisées. Celle-ci font partie de l'utilisation de la mémoire virtuelle.

- On permet la pagination mettant le bit de PG dans le registre de contrôle est mis à 0. Ceci active la mémoire virtuelle.

- Les gestionnaires d'exception sont installés. Ces routines obtiennent le contrôle quand une erreur de programmation est détectée par le matériel.

- Le moment de la journée est lu de l'horloge CMOS. L'horloge CMOS est sur une batterie même si l'ordinateur fonctionne ou non.

- Le **"timer interrupt"** est initialisé.

- Les interruptions sont activées

- INITRD (le disque d'initialisation RAM) est monté. (Le Montage d'un système de fichiers fait que les dossiers dans le système de fichiers seront disponible pour le noyau et pour des programmes.)

- Des routines d'initialisation de sous-système diverses sont appelées. Le noyau est composé d'un certain nombre de sous-systèmes et la plupart d'entre eux ont leur sur le code d'initialisation qui est appelé à ce moment.

- D'autres processeurs SMP sont démarrés. La plupart des systèmes modernes ont plus qu'un processeur. Jusqu'à maintenant seulement un processeur était utilisé. Cette étape démarre les autres processeurs.

- À cette étape, l'initialisation du noyau est essentiellement achevée à l'exception des outils d'entrée-sortie. En ce moment, le noyau est prêt à entrer dans son mode de traitement normal qui consiste en cycle suivant :
1. Appelez le planificateur pour choisir et exécuter un programme.
2. Le programme ou un certain autre événement de matériel créent un interrompu. Cela cause que le CPU choisit le noyau pour utiliser le temps processeur.
3. Le noyau s'occupe de l’interruption, c'est-à-dire il fait tout ce qui est nécessaire pour traiter la cause de l'interruption.
4. Retour à l'étape 1. A noter que programme choisi peut ou ne peut pas être le programme qui a créé l'interruption.

- À ce point dans l'ordre de démarrage, il n'y a aucune tâche (ou programmes) à faire tourner. Le noyau crée donc la première tâche dans la file d'attente de tâche. Cette tâche (l'ID de processus 1) est appelée la tâche **_init_**. Le noyau crée alors une deuxième tâche, connue comme kthreadd.

- La tâche kthreadd, fonctionnant comme une "thread" de noyau gère alors les autres "thread "de noyau que kthreadd créé.
La tâche init, fonctionnant aussi comme une "thread" de noyau, fait des initialisations supplémentaires.




#Le processus *init*



###Définition

Une fois que le noyau est lancé et initialisé, il lance la première application de l'espace utilisateur, qui est typiquement */sbin/init* (mais qui peut n'être qu'un simple script dans certains systèmes embarqués n'ayant pas besoin de l'initialisation complète d'*init*). C'est le premier programme invoqué qui est compilé avec la librairie C standard - jusqu'à maintenant dans le démarrage du système, aucune autre application C n'a été exécutée.

Le rôle du processus *init* est de gérer tous les processus (dont les tâches du noyau). Les processus portent tous un numéro d'identification PID (*Process ID*), attribués dans l'ordre. Comme les répertoires, les processus sont organisés en arbre, dont *init* est la racine (PID=1).



###Le fichier de configuration */etc/inittab*

Ceci est le fichier de configuration du processus *init*. Il contient des lignes au format suivant :

* code:niveau:action:commande

Le champ **code** contient une séquence de un à quatre caractères (deux pour compatibilité) unique pour identifier la ligne dans le fichier.

Le champ **niveau** donne le niveau d'exécution pour lequel cette ligne doit être prise en compte. La notion de niveau d'exécution permet de spécifier des configurations d'exécution différentes. Un standard existe et est résumé dans la table ci-dessous. (Il est possible de spécifier plusieurs niveaux lorsque la commande associée doit être lancée à différents niveaux d'exécution.)

| Niveau | Description                                                              |
| :----: | ------------------------------------------------------------------------ |
| 0      | arrêt de la machine                                                      |
| 1      | mode mono-utilisateur, seul le super-utilisateur peut se connecter       |
| 2      | mode multi-utilisateurs, avec peu de services réseaux                    |
| 3      | mode multi-utilisateurs, avec tous les services réseaux (mode par défaut)|
| 4      | définissable par l'utilisateur                                           |
| 5      | démarrage de X11 au boot                                                 |
| 6      | redémarrage de la machine                                                |

Le champ **action** définit la manière d'exécuter la commande du champ commande. Le tableau ci-après présente les actions les plus courantes :

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

* /etc/rc.d/init.d/script [start | stop | restart]

La plupart des démons du système enregistrent leur numéro de processus dans un fichier du répertoire /var/run.

Après l'exécution des scripts, le processus init lance les processus en mode respawn. Ce sont, en général, les programmes d'invitation à l'ouverture de session. Ces programmes doivent être relancés dès qu'une session se termine.


#Sources
###Sources pour la partie Kernel
https://en.wikipedia.org/wiki/Completely_Fair_Scheduler
https://en.wikipedia.org/wiki/Linux_startup_process
http://lxr.linux.no/
http://duartes.org/gustavo/blog/post/kernel-boot-process/
http://smoudere.free.fr/demarrage_linux.html
https://fr.wikipedia.org/wiki/Ordonnancement_dans_les_syst%C3%A8mes_d%27exploitation
https://fr.wikipedia.org/wiki/Completely_Fair_Scheduler
http://www.tuxradar.com/content/how-linux-kernel-works
https://fr.wikipedia.org/wiki/Syst%C3%A8me_d%27exploitation
https://fr.wikipedia.org/wiki/Abstraction_(informatique)
https://en.wikipedia.org/wiki/Scheduling_(computing)#Since_Linux_2.6.23
https://fr.wikipedia.org/wiki/Noyau_de_syst%C3%A8me_d%27exploitation#Fonctions_g.C3.A9n.C3.A9ralement_remplies_par_un_noyau
http://www.linuxpedia.fr/doku.php/util/boot

###Sources pour la partie Init
- [Wikipedia](https://fr.wikipedia.org/wiki/GNU_GRUB)
- [Linuxpedia](http://www.linuxpedia.fr/doku.php/util/boot)
- [IBM](http://www.ibm.com/developerworks/linux/library/l-linuxboot/)
- http://daniel.galvao.free.fr/astrid/coursLinux/x1656.html
- [Ubuntu-fr](https://doc.ubuntu-fr.org/mbr)
- http://fr.wikipedia.org/wiki/Master_boot_record
- [Optimisation du bios](http://www.commentcamarche.net/contents/1022-optimisation-du-bios)
