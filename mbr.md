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

1+0 enregistrements lus
1+0 enregistrements écrits
__
00000000  eb 63 90 10 8e d0 bc 00  b0 b8 00 00 8e d8 8e c0  |.c..............|

512 bytes copied, 0,000314534 s, 1,6 MB/s

00000010  fb be 00 7c bf 00 06 b9  00 02 f3 a4 ea 21 06 00  |...|.........!..|
00000020  00 be be 07 38 04 75 0b  83 c6 10 81 fe fe 07 75  |....8.u........u|
00000030  f3 eb 16 b4 02 b0 01 bb  00 7c b2 80 8a 74 01 8b  |.........|...t..|
00000040  4c 02 cd 13 ea 00 7c 00  00 eb fe 00 00 00 00 00  |L.....|.........|
00000050  00 00 00 00 00 00 00 00  00 00 00 80 01 00 00 00  |................|
00000060  00 00 00 00 ff fa 90 90  f6 c2 80 74 05 f6 c2 70  |...........t...p|
00000070  74 02 b2 80 ea 79 7c 00  00 31 c0 8e d8 8e d0 bc  |t....y|..1......|
00000080  00 20 fb a0 64 7c 3c ff  74 02 88 c2 52 bb 17 04  |. ..d|<.t...R...|
00000090  80 27 03 74 06 be 88 7d  e8 17 01 be 05 7c b4 41  |.'.t...}.....|.A|
000000a0  bb aa 55 cd 13 5a 52 72  3d 81 fb 55 aa 75 37 83  |..U..ZRr=..U.u7.|
000000b0  e1 01 74 32 31 c0 89 44  04 40 88 44 ff 89 44 02  |..t21..D.@.D..D.|
000000c0  c7 04 10 00 66 8b 1e 5c  7c 66 89 5c 08 66 8b 1e  |....f..\|f.\.f..|
000000d0  60 7c 66 89 5c 0c c7 44  06 00 70 b4 42 cd 13 72  |`|f.\..D..p.B..r|
000000e0  05 bb 00 70 eb 76 b4 08  cd 13 73 0d f6 c2 80 0f  |...p.v....s.....|
000000f0  84 d0 00 be 93 7d e9 82  00 66 0f b6 c6 88 64 ff  |.....}...f....d.|
00000100  40 66 89 44 04 0f b6 d1  c1 e2 02 88 e8 88 f4 40  |@f.D...........@|
00000110  89 44 08 0f b6 c2 c0 e8  02 66 89 04 66 a1 60 7c  |.D.......f..f..`|
00000120  66 09 c0 75 4e 66 a1 5c  7c 66 31 d2 66 f7 34 88  |f..uNf.\|f1.f.4.|
00000130  d1 31 d2 66 f7 74 04 3b  44 08 7d 37 fe c1 88 c5  |.1.f.t.;D.}7....|
00000140  30 c0 c1 e8 02 08 c1 88  d0 5a 88 c6 bb 00 70 8e  |0........Z....p.|
00000150  c3 31 db b8 01 02 cd 13  72 1e 8c c3 60 1e b9 00  |.1......r...`...|
00000160  01 8e db 31 f6 bf 00 80  8e c6 fc f3 a5 1f 61 ff  |...1..........a.|
00000170  26 5a 7c be 8e 7d eb 03  be 9d 7d e8 34 00 be a2  |&Z|..}....}.4...|
00000180  7d e8 2e 00 cd 18 eb fe  47 52 55 42 20 00 47 65  |}.......GRUB .Ge|
00000190  6f 6d 00 48 61 72 64 20  44 69 73 6b 00 52 65 61  |om.Hard Disk.Rea|
000001a0  64 00 20 45 72 72 6f 72  0d 0a 00 bb 01 00 b4 0e  |d. Error........|
000001b0  cd 10 ac 3c 00 75 f4 c3  12 ed 07 00 00 00 80 20  |...<.u......... |

000001c0  21 00 83 fe ff ff 00 08  00 00 00 08 7e 03 00 fe  |!...........~...|
000001d0  ff ff 05 fe ff ff fe 17  7e 03 02 a8 69 0b 00 00  |........~...i...|
000001e0  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
000001f0  00 00 00 00 00 00 00 00  00 00 00 00 00 00 55 aa  |..............U.|
00000200



##Sources

- [Wiki linuxpedia](http://www.linuxpedia.fr/doku.php/util/boot)
- [IBM : Inside the linux boot process](http://www.ibm.com/developerworks/linux/library/l-linuxboot/)
- [Ubuntu-fr](https://doc.ubuntu-fr.org/mbr)

