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

__sudo dd if=/dev/sda bs=512 count=1 | hexdump -C__

En claire : 
  En tant que superutilisateur __sudo__ je demande de faire une copie par blocs __dd__ avec comme source __if__ le matériel __/dev/sda__ (premier disque dur), je précise que le bloc désiré doit faire 512 octets et que je ne copie qu'un bloc.
  La sortie par défaut est ensuite redirigée __| (pipe)__ dans la moulinette __hexdump__ qui formate les données sous la forme adresse, valeurs hexadécimale de 16 octets consécutifs et caractères ascii correspondants. 

Sur mon disque dur, voici le résultat : 


![Pile]()




##Sources

- [Wiki linuxpedia](http://www.linuxpedia.fr/doku.php/util/boot)
- [IBM : Inside the linux boot process](http://www.ibm.com/developerworks/linux/library/l-linuxboot/)
- [Ubuntu-fr](https://doc.ubuntu-fr.org/mbr)

