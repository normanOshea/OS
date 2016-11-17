

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

![interface lilo](http://www2.futaie.org:4280/~rousselotv/lp/lilo.png) *inteface lilo*

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

![interface grub](http://www2.futaie.org:4280/~rousselotv/lp/grub.png) *inteface lilo*

####Sources :
- ![Wikipedia](https://fr.wikipedia.org/wiki/GNU_GRUB)
- ![Linuxpedia](http://www.linuxpedia.fr/doku.php/util/boot)

