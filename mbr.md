#MBR

##Master Boot Record

###Définition

Le MBR *Master Boot Record*, ou parfois *Zone amorce*, est une partie d'un disque dur, d'une clé USB ou d'un média amovible quelconque. 
Plus précisément, c'est le **premier** 'secteur' adressable d'un disque dur. Il commence par l'adresse mémoire zero et sa taille est de 512 octets.

Sa présence est éssentiel pour accéder aux systèmes de fichiers présents et en cas de corruption de ce dernier, il sera impossible d'accéder aux données ou, dans une moindre mesure, impossible de redimensionner les partitions. 

Accéder aux adresses mémoire du **MBR** est délicat, et la moindre modification peut corrompre tout le système de fichier. Son accès en lignes de commandes ne peut s'effectuer qu'en mode superutilisateur (sudo).

###Structure du MBR

Le MBR contient 

##Sources

- [Wiki linuxpedia](http://www.linuxpedia.fr/doku.php/util/boot)
- [IBM : Inside the linux boot process](http://www.ibm.com/developerworks/linux/library/l-linuxboot/)
- [Ubuntu-fr](https://doc.ubuntu-fr.org/mbr)

