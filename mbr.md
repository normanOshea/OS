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


##Sources

- [Wiki linuxpedia](http://www.linuxpedia.fr/doku.php/util/boot)
- [IBM : Inside the linux boot process](http://www.ibm.com/developerworks/linux/library/l-linuxboot/)
- [Ubuntu-fr](https://doc.ubuntu-fr.org/mbr)
- http://fr.wikipedia.org/wiki/Master_boot_record

