# 101.2. Démarrage du système

### 101.2.Démarrage du système

**Poids:**3

**Description:** Les candidats doivent être capable de guider le système durant le processus de démarrage.

**Connaissances clés:**

* Provide common commands to the boot loader and options to the kernel at boot time
* Demonstrate knowledge of the boot sequence from BIOS to boot completion
* Understanding of SysVinit and systemd
* Awareness of Upstart
* Check boot events in the log files

**Concepts et Utilitaires:**

* dmesg
* BIOS
* bootloader
* kernel
* initramfs
* init
* SysVinit
* systemd

 N'avez-vous jamais penser à ce qu'il se passe lorsque vous démarrer le bouton marche d'un pc ? Il n'y a ni génie ou fantôle qui démarre un coeur de métal hurlant et amène le système d'exploitation démarré et fonctionnel. Voyons ce qu'il se passe ?

## Procédure de démarrage

Dessinons une vue d'ensemble pour avoir un aperçu de la procédure générique de démarrage depuis le BIOS jusqu'au shell

![](.gitbook/assets/boot-bootseq.jpg)

Maintenant expliquons chacune de ces étapes :

**1-**Premier démarrage du système.

**2-**Chargement du BIOS (dans les systèmes modernes cela sera UEFI)

## BIOS

BIOS, qui signifie Basic Input Output System, est un logiciel stocké sur une petite partie de mémoire de la carte mère. BIOS est responsable du POST. POST acronyme pour Power On Self Test, est l'ensemble de diagnostiques initiaux fait par l'ordinateur après son démarrage, qui ont pour but de vérifier si chaque matériel a des problèmes. Donc BIOS fait de POST le tout premier logiciel lancé lorsque l'ordinateur est démarré.

![](.gitbook/assets/boot-bios.jpg)

**3-**BIOS scanne et va sur le premier secteur d'amorçage  du disque (ou celui qu'on a choisi. Un secteur d'amorçage  est une région du disque dur, d'une disquette, d'un cdrom ou d'un autre périphérique de stockage de donnée qui contient du code machine à charger dans la random-access memory (RAM) par le système d'un ordinateur vie le logiciel d'usine fourni comme BIOS.

Qu'est ce qui existe sur un secteur d'amorçage ? MBR

## MBR

Un enregistrement maitre de démarrage ( master boot record (souvent raccourci en MBR) est une sorte de secteur d'amorçage stocké sur un disque dur ou un autre périphérique de stockage qui contient le code nécessaire à l'ordinateur pour démarrer le processus de démarrage.


Le MBR est localisé dans le premier secteur d'un disque. L'adresse spécifique sur un disque est Cylinder: 0, Head: 0, Sector: 1 et il est de 512 bytes.

Qu'est ce que le Master Boot Record inclue et comment il continue la séquence de démarrage ?

![](.gitbook/assets/boot-MBR.JPG)

Le MBR lui-même contient deux choses : le programme de chargement du démarrage et la table de partition

Lorsque le BIOS charge, il cherche les données stockées dans ce premier secteur du disque dur, le MCR; utilisant les données stockées dans le MBR, le BIOS active le chargement du démarrage.

{% hint style="info" %}
Qu'est ce que le nombre magique ? Localisé dans les deux derniers bytes du  **MBR** (511-512), cette section doit contenir la valeur hexadécomale AA55, qui la classe officiellement comme un **MBR** valide. Un **magic number** invalide indique un  **MBR** corrompu ou absent, c'est pourquoi ces bytes sont critiques pour démarrer ou utiliser le disque.
{% endhint %}

**4-**Le chargement du démarrage est exécuté.

### Chargement du démarrage (boot loader)

Très simplement, un chargeur de démarrage charge le système d'exploitation. La plupart des chargeurs de démarrage charge deux étapes.

**A-** Dans la première étape du démarrage, le BIOS charge une partie du chargeur de démarrage connue comme le  **initial program loader**, ou **IPL**. L' **IPL** ** interroge la table de partition et est donc capable de charger les données si elles existes sur les différents médias. **Cette action est utilisé initialement pour localiser la seconde étape du chargeur de démarrage, qui contient le reste du chargeur.

**B-**La seconde étape du chargeur de démarrage est la partie principal du chargeur de démarrage; beaucoup considère que c'est la seule vraie partie du chargeur de démarrage. Elle contient les parties les plus **disk-intensive parts** du chargeur, comme l'interface utilisateur \[bootloader] et les chargements du noyau. (Ces interfaces utilisateurs peuvent aller de la simple ligne de commande à des interfaces graphique moderne.)

**5-**Lilo / Grub / Grub2 démarrage.

Il y a différent chargeurs de démarrage . Lilo, Grub et Grub2:

## Lilo

LILO (Linux Loader) est un chargeur de démarrage pour Linux et a été le chargeur de démarrage par défaut de nombreuses distributions Linux dans les années après la popularité de loadlin. Aujourd'hui, de nombreuses distributions utilise GRUB comme chargeur de démarrage par défaut, mais LILO et sa variante ELILO sont encore largement utilisés. Le dévelopement de LILO a été arrêté en December 2015 quite à la demande de Joachim Wiedorn pour de nouveaux développeurs.

![](.gitbook/assets/boot-lilo.jpg)

Le fichier de configuration de lilo est positionné dans “/etc/lilo.conf”. Lilo lit ce fichier de configuration et il permet de dire à Lilo où le chargeur de démarrage doit être placé. Le fichier de configuration exemple est présenté ci-dessous :

```
boot=/dev/hda              # This tells LILO where to install the bootloader.
map=/boot/map              # This file is automatically generated by LILO during bootup
install=/boot/boot.b       # This file contains the “bootstrap” code that starts your operating system and it holds both the primary and secondary parts of the bootloader.
prompt                     # This tells LILO to use the user interface. For example, to choose an operating system or enter parameters for the Linux kernel.
timeout=50                 # Tells LILO how long to wait at the prompt before booting the default operating system.
image=/boot/vmlinuz-2.0.36 # Name of a Linux kernel for LILO to boot.
label                      # specifies the default OS to boot.
root=/dev/hda2             # This option tells LILO where the OS file system actually lives.
read-only                  # This tells LILO to perform the initial boot to the file system read-only.
other=/dev/hda1            # It tells LILO to boot an operating system other than Linux.
label=win                  # same as all other label options.
```

lilo a une commande et quelques options qui peuvent être utile :

```
 -c config-file: Specifies the alternative configuration file other than default file /etc/lilo.conf.
 -q: Lists the mapped files.
 -V: Prints the Lilo version number.
 -v: Specifies verbose output.
 -u device-name: Uninstalls LILO and restores the saved boot sector after validating timestamp. Device-name is the name of the device on which LILO is installed.
 -U device-name: This is the same as -u, but it does not check the timestamp.
 -R command-line: Sets the default command for the boot loader the next time it executes.
```

## Grub

GRUB (GRand Unified Bootloader) est un packet de chargement de démarrage développé pour supporté de nombreux système d'exploitation et permettre aux utilisateur de sélectionner parmi eux lors du démarrage. Il est développé sous le projet GNU GRUB.

## Grub2

GRUB 2 remplace mainteantn GRUB. Et le nom GRUB a été renommé en GRUB Legacy et n'est plus développé activement, cependant, il peut être utilisé pour démarrer d'anciens systèmes puisque des corrections de bugs sont encore fourni.

En surgace la majorité des utilisateurs ne remarquera aucune différence mais la nouvelle version a un changment structurel majeur et devrait être plus fiable. La nouvelle version stocke aussi ces fichiers de configuration différement.

Les améliorations majeur de GRUB 2' par rapport au GRUB original inclue :

* Support du démarrage multiple
* Support de différentes architectures matériel et système d'exploitation comme  Linux et Windows
* Fournit une interface en ligne de commande interactive Bash-like pour les utilisateurs qui exécutent des commandes GRUB ainsi que l'interraction avec les fichiers de configuration
* Active l'accès à l'éditeur de GRUB
* Support la configuration des mots de passe avec chiffrement pour la sécurité
* Support le démarrage depuis un réseaux combiné avec plusieurs autres fonctionnalités mineures

Sans tenir compte de la version de GRUB, un chargeur de démarrage permet à l'utilisateur de:

1. Modifier la manière dont le système se comporte en spécifiant différents noyaux à utiliser,
2. Choisir entre différents système d'exploitation à démarrer, et
3. Ajouter ou éditer des lignes de configuration pour modifier les options de démarage, parmi d'autres choses.

## Option de démarrage du Kernel 

Les paramètres de la ligne de commande du Kernel sont des paramètres que l'on passe au système durant le processus de démarrage. Ils sont aussi connu comme "boot options".

Nous** devons utiliser un clavier physique** pour passer au kernel des commande de démarrage car lors du démarrage il n'y a pas de service réseau. Redémarrer le système et appuyer sur la touche Esc pendant le démarrage:

![](.gitbook/assets/boot-grubmenu.jpg)

En fonction de la configuration l'utilisateur peut choisir dans un menu de type de démarrage potentiel ou de versions de noyau ou simplement être autorisé à lancer les options par défaut. Appuyer sur la touche 'e' pour voir certains paramètres du noyau :

![](.gitbook/assets/boot-grubmenu2.jpg)

Les paramètres de démmarrage du Noyau Linux sont passé dans une liste de chaines de caractères séparée par des espaces blancs :

```
name[=value_1] [,value_2]........[,value_10]
```

Où ‘**name=unique keyword**‘ est **définit comme partie du noyau** avec les valeurs associées(max 10)

`'linux'`définit l'endroit où le noyau exécutable se lance, et les options de démarrage du noyau vienne après celle-ci.

Il y a de nombreux paramètre qui nous aide à configurer et à déterminer tous les aspects de notre système d'opération durant le processus de démarrage. Certaines d'entre elles sont :

```
root=UUID=... : This argument tells the kernel what device (hard disk, floppy disk) to be used as the root filesystem while booting.
ro : This argument tells the kernel to mount root file system as read-only.
```

Une des options de démarrage du Noyau les plus connus est la récupération du mot de passe en utilisant le paramètre du noyau  `single`. Le paramètre `single` guide ‘init‘ pour démarrer l'ordinateur dans un mode utilisateur unique et désactive le démarrage de tous les démons.

`/proc/cmdline` Ce fichier montre les paramètres passé au noyau lors du démarrage :

```
root@server2:~# cat /proc/cmdline 
BOOT_IMAGE=/boot/vmlinuz-4.10.0-28-generic root=UUID=e4a2c83b-fb68-46f5-a7ec-a83bbad6e3fd ro find_preseed=/preseed.cfg auto noprompt priority=critical locale=en_US quiet
```

**6-** Le noyau Linux est lu et exécuté. 

{% hint style="info" %}

Dès que le noyau Linux a été démarré et le système de fichier racine (/) monté, les programmes peuvent se lancer et d'autre modules du noyau peuvent être intégrer pour fournir des fonctionnalités supplémentaires.

Pour monter le système de fichier racine, certaines conditions doivent être remplies. Le noyeau a besoin des pilotes correspondants pour accéder au périphériques sur lequel le système de fichier racine est positionné (notamment les pilotes SCSI). Le noyau doit aussi contenir le code nécessaire pour lire le système de fichier (ext2, reiserfs, romfs, etc.). Il est aussi possible que le système de fichier racine soit déjà chiffré. Dans ce cas, un mot de passe est nécessaire pour monter le système de fichier.

Le disque RAM initial (aussi appelé initdisk ou initrd) résout précisment le problème décrit ci-dessus.
{% endhint %}

## initramfs

 Le noyau Linux fournit une option pour avoir un petit système de fichier chargé dans un disque RAM et qui exécute des programme avant que le système de fichier racine ne soit monté. 

 initrd contient un ensemble minimum de répertoire et d'exécutables pour le faire, comme l'outil `insmod` pour installer les modules du noyau dans le noyau.

Sa durée de vi est courte, il ne sert que de pont avec le système de fichier racine réel. 

**7- **Le programme 'init' charge et devient le premier processus ID.

### Qu'est ce que init?

Dans Linux, init est une abréviation pour Initialization. L'init est un processus démon qui démarre dès que l'ordinateur démarre et continue à tourner ensuite, jusqu'à son arrêt. De fait init est le premier processus qui démarrer lorsque l'ordinateur démarre, le rendant le parent de tous les autres processus directement et par conséquent il est assigné à“pid=1“.\
'init' sur les anciennes distributions linux était un processus avec un nom /sbin/int . Aujourd'hui 'init' n'est plus 'init', il peut être n'importe quoi.

## Solutions de programme init

Dans le monde linux nous avons trois types différents de programme init qui lance des différents démons, applications et programmes.

* SysV
* upstart
* Systemd

Donc /sbin/init peut être lié à upstart ou systemd.

**Sysv**

SysV est le plus vieux système pour gérer le démarrage des sevice durant le processus de démarrage dans un système Linux.  SysVinit existe depuis toujours (au moins depuis la naissance de Linux).Traditionnellement dans SystemV, la procédure /sbin/init était utilisé pour démarrer des services.

La manière de faire de SysVinit était de définir un ordre strict pour le démarrage des services. Chaque service était assigné à un numéro de priorité et init démarrait les services en séquence par priorité.

L'ordre dans lequel cela se passe est essentiel, le chargement en série est nécessaire pour les dépendances, mais parfois un chargement parallèlle peut être utilisé pour augmenter la vitesse. Le problème avec SysVinit est qu'il était difficile à configurer, à la fois upstart et Systemd ont été developpé pour rendre la configuration du système de chargement plus efficace.

```
### CentOS 5
```

**Upstart**

Dans une tentative pour amener plus de fonctionnalité dans le processus d'initilisation de Linux, Canonical a publié Ubuntu 6.10 (Edgy Eft) en 2006 avec Upstart. Upstart a été conçu avec une compatibilité ascendante depuis le début. Il pouvait lancer des démons sans aucune modification des scripts de démarrage. Graçe à ça, de nombreuses distributions Linux sont passé à Upstart, mais pas toutes.

Upstart est un remplacement basé sur les évènement, il reçoit des évènement et lance les taches (jobs) en se basant sur les évènements.

Le problème avec upstart est qu'il utilise des script shell et denombreuses fonctionnalité qui existaient était déjà initialisé. Bien que rétro-compatible il souffrait du relais de code non optimisé.

> si notre système a un dossier /etc/init Directory, il utilise upstart.

```
### Ubuntu 14
root@ubuntu:~# ls /etc/init
init/            init.d/          initramfs-tools/
```

**Systemd**

{% hint style="info" %}
Un systemd, fait référence à tous les paquets, utilitaires et bibliothèques autour des démons. L'objectif du projet systemd est de fournir un système d'exploitation qui tourne au dessus d'un noyau Linux et prend le contrôle de presque tout après le chargement du noyau. En conséquence il a été développé pour combler les lacunes de l'init.
{% endhint %}

Systemd est conçu pour démarrer les processus en parallèle, ce qui réduit le temps de démarrage et optimiser l'utilisation du matériel. Il a de nombreuses autres fonctionnalités comparé à init.

Systemd s'est rapidement imposé comme manière pour les systèmes linux de démarrer les service et il est actuellement le standard de toutes les distributions majeurs de linux (même Ubuntu ). Systemd n'est pas retro-compatible cependant il y a de nombreux scripts qui convertissent les commandes sysv en commandes systemd .

> Si votre système a un dossier /usr/lib/systemd , vous utilisez systemd

```
### Ubuntu 16 or CentOS 7
root@ubuntu16-1:~# ls /usr/lib/systemd
boot  catalog  network  user  user-generators
```

**8-** Sysv ou upstart ou systemd (peu importe le système de gestion de service) démarre tout. Les pré-requis, les services, ... et shell. Une fois le shell présent l'utilisateur peut se connecter.

## dmesg

La commande dmesg est utilisé pour **afficher les messages liés au noyau** sur les systèmes Unix-like. dmesg attend pour “display message or display driver“. La commande dmesg retrouve ses donnant en lisant le tampon circulaire du noyau.

Le tampon circulaire du noyau est une structure de donnée qui enregistre les message liés aux opérations sur le noyau. Un tampon circulaire est un type spécial de tampon qui a toujours une taille constante, en supprimant les plus ancien message lorsque de nouveaux messages arrivent.

![](.gitbook/assets/boot-dmesgkrb.png)

dmesg peut être utile en cas de problèmes ou juste pour essayer d'obtenir des information sur le matériel d'un système. La syntaxe basique est dmesg \[options].

```
Options:
 -C, --clear                 clear the kernel ring buffer
 -c, --read-clear            read and clear all messages
 -D, --console-off           disable printing messages to console
 -E, --console-on            enable printing messages to console
 -F, --file <file>           use the file instead of the kernel log buffer
 -f, --facility <list>       restrict output to defined facilities
 -H, --human                 human readable output
 -k, --kernel                display kernel messages
 -L, --color[=<when>]        colorize messages (auto, always or never)
                               colors are enabled by default
 -l, --level <list>          restrict output to defined levels
 -n, --console-level <level> set level of messages printed to console
 -P, --nopager               do not pipe output into a pager
 -r, --raw                   print the raw message buffer
 -S, --syslog                force to use syslog(2) rather than /dev/kmsg
 -s, --buffer-size <size>    buffer size to query the kernel ring buffer
 -u, --userspace             display userspace messages
 -w, --follow                wait for new messages
 -x, --decode                decode facility and level to readable string
 -d, --show-delta            show time delta between printed messages
 -e, --reltime               show local time and time delta in readable format
 -T, --ctime                 show human readable timestamp (may be inaccurate!)
 -t, --notime                don't print messages timestamp
     --time-format <format>  show time stamp using format:
                               [delta|reltime|ctime|notime|iso]
```

En utilisant dmesg sans option permet d'afficher sur la sortie standard tous les messages du noyau. 

```
root@ubuntu16-1:~# dmesg 

<Output has been truncated>

[    6.126297] audit: type=1400 audit(1541417236.033:9): apparmor="STATUS" operation="profile_load" profile="unconfined" name="/usr/bin/evince//sanitized_helper" pid=603 comm="apparmor_parser"
[    6.126298] audit: type=1400 audit(1541417236.033:10): apparmor="STATUS" operation="profile_load" profile="unconfined" name="/usr/bin/evince-previewer" pid=603 comm="apparmor_parser"
[    6.403014] shpchp: Standard Hot Plug PCI Controller Driver version: 0.4
[    6.403138] piix4_smbus 0000:00:07.3: SMBus Host Controller not enabled!
[    6.450506] vmw_vmci 0000:00:07.7: Found VMCI PCI device at 0x11080, irq 16
[    6.450585] vmw_vmci 0000:00:07.7: Using capabilities 0xc
[    6.481197] Guest personality initialized and is active
[    6.481237] VMCI host device registered (name=vmci, major=10, minor=55)
[    6.481238] Initialized host personality
[    6.861468] random: crng init done
[    6.870993] AVX2 version of gcm_enc/dec engaged.
[    6.870994] AES CTR mode by8 optimization enabled
[    7.412248] Adding 1045500k swap on /dev/sda5.  Priority:-1 extents:1 across:1045500k FS
[    9.011802] IPv6: ADDRCONF(NETDEV_UP): ens33: link is not ready
[    9.019995] e1000: ens33 NIC Link is Up 1000 Mbps Full Duplex, Flow Control: None
[    9.315344] floppy0: no floppy controllers found
[    9.315374] work still pending
[   10.229508] ip_tables: (C) 2000-2006 Netfilter Core Team
[   13.740999] NET: Registered protocol family 40
```

On peut nettoyer les journaux de dmesg si nécessaire avec la commande `dmesg -c` .

**/var/log/dmesg**

La commande `dmesg` montre le contenu courant du journal du noyau avec les messages du tampon circulaire tandis que le fichier `/var/log/dmesg` contient ce que contenait le tampon circulaire lors du dernier processus de démarrage complet. Essayer `cat /var/log/dmesg`. 



.

.

.

Sources:

[https://www.lifewire.com](https://www.lifewire.com)

[https://www.tecmint.com/linux-boot-process/](https://www.tecmint.com/linux-boot-process/) & [https://www.tecmint.com/best-linux-boot-loaders/](https://www.tecmint.com/best-linux-boot-loaders/)

[https://whatis.techtarget.com/definition/boot-sector](https://whatis.techtarget.com/definition/boot-sector) & [https://whatis.techtarget.com/definition/Master-Boot-Record-MBR](https://whatis.techtarget.com/definition/Master-Boot-Record-MBR)

[https://www.ibm.com/developerworks/library/l-bootload/index.html](https://www.ibm.com/developerworks/library/l-bootload/index.html)

[https://www.interserver.net/tips/kb/what-is-lilo/](https://www.interserver.net/tips/kb/what-is-lilo/)

[https://www.linuxjournal.com/article/1166](https://www.linuxjournal.com/article/1166)

[https://www.oreilly.com/library/view/linux-in-a/0596000251/ch04s02.html](https://www.oreilly.com/library/view/linux-in-a/0596000251/ch04s02.html)

[https://www.cyberciti.biz/tips/10-boot-time-parameters-you-should-know-about-the-linux-kernel.html](https://www.cyberciti.biz/tips/10-boot-time-parameters-you-should-know-about-the-linux-kernel.html)

[https://unix.stackexchange.com/questions/89923/how-does-linux-load-the-initrd-image](https://unix.stackexchange.com/questions/89923/how-does-linux-load-the-initrd-image)

[https://developer.ibm.com/articles/l-initrd/](https://developer.ibm.com/articles/l-initrd/)[https://www.ibm.com/developerworks/community/blogs/mhhaque/entry/anatomy_of_the_initrd_and_vmlinuz?lang=en](https://www.ibm.com/developerworks/community/blogs/mhhaque/entry/anatomy_of_the_initrd_and_vmlinuz?lang=en)

[http://www.linuxfromscratch.org/blfs/view/svn/postlfs/initramfs.html](http://www.linuxfromscratch.org/blfs/view/svn/postlfs/initramfs.html)

[https://help.ubuntu.com/community/Grub2](https://help.ubuntu.com/community/Grub2)

[https://www.tecmint.com/systemd-replaces-init-in-linux/](https://www.tecmint.com/systemd-replaces-init-in-linux/)

[https://fossbytes.com/systemd-vs-sys-v-vs-upstart/](https://fossbytes.com/systemd-vs-sys-v-vs-upstart/)

[https://unix.stackexchange.com/questions/196166/how-to-find-out-if-a-system-uses-sysv-upstart-or-systemd-initsystem](https://unix.stackexchange.com/questions/196166/how-to-find-out-if-a-system-uses-sysv-upstart-or-systemd-initsystem)

[https://www.tecmint.com/dmesg-commands/](https://www.tecmint.com/dmesg-commands/) 

 [https://www.linuxtechi.com/10-tips-dmesg-command-linux-geeks/](https://www.linuxtechi.com/10-tips-dmesg-command-linux-geeks/)

[https://www.quora.com/What-is-a-ring-buffer-in-Linux](https://www.quora.com/What-is-a-ring-buffer-in-Linux)

[https://unix.stackexchange.com/questions/191560/difference-between-output-of-dmesg-and-content-of-var-log-dmesg](https://unix.stackexchange.com/questions/191560/difference-between-output-of-dmesg-and-content-of-var-log-dmesg)
