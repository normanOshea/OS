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



###Webographie
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
