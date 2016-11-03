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

##Sources

- [Wikipédia BIOS](https://fr.wikipedia.org/wiki/Basic_Input_Output_System)
- [Wiki linuxpedia](http://www.linuxpedia.fr/doku.php/util/boot)

