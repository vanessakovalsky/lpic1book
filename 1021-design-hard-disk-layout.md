# 102.1. Modèle de conception d'un disque dur

## **102.1 Modèle de conception d'un disque dur**

**Poids:** 2

**Description:** Les candidats doivent être capable de concevoir le schema de partitionnement d'un disque pour un système Linux.

**Connaissances clés:**

* Allouer le système de fichier et l'espace swap pour séparer les partitions ou les disques
* Adapter la conception pour correspondre à l'usage du système
* S'assurer que la partition /boot est conforme aux pré-requis de l'architecture matériel pour le démarrage
* Connaissances des fonctionnalités basic de LVM

**Concepts et Utilitaires:**

* / (root) filesystem
* /var filesystem
* /home filesystem
* /boot filesystem
* swap space
* mount points
* partitions

Dans cette petite leçon nous ferons le tours de la structure d'un disque dur et comment les données sont stockées sur eux, puis nous irons voir le modèle de disque de linux.

### Pistes de siques, cylindre et secteurs

Un disque est divisé en pistes, cylindres et secteurs.

![](.gitbook/assets/disklayout-diskstructure.jpg)

Une **piste** est une portiion du disque qui passe sous une tête unique stationnaire pendant la rotation du disque. Un anneau est large de 1 bit.

Un **cylindre** est comprit dans l'ensemble des pistes décrit par toutes les têtes (sur des plateaux séparés) sur une position de recherche unique. Chaque cylindre est équidistant du centre du disque. Une piste est divisé en segments de secteurs, ce qui est l'unité basique de stockage.

Un **secteur**, est la plus petite unité physique de stockage sur un disque, il fait presque toujours 512 bytes car 512 st un multiple de 2 ( 2 puissance 9). Le nombre 2 est utilisé car il y a deux état dans le plus basique des langages de l'ordinateur : on et off.

### Partitions

Le partitionnement du disque ou division du disque est la création d'une ou plusieurs régions sur un disque dur ou un autre stockage secondaire,  **pour que le système d'exploitation puisse gérer les information dans chaque région séparément**. Ces régions sont appelées partitions.

Le disque stocke la localisation de l'** information à propos des partitions' et leur tailles** dans une zone appelé la **table de partition** que le système d'opération lead avant tout autre partie du disque. Chaque partition apparait alors dans le système d'exploitation comme un disque "logique" distinct qui utilise une partie du disque.

```
[root@centos7-1 ~]# lsblk
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   50G  0 disk 
├─sda1            8:1    0    1G  0 part /boot
└─sda2            8:2    0   49G  0 part 
  ├─centos-root 253:0    0 45.1G  0 lvm  /
  └─centos-swap 253:1    0  3.9G  0 lvm  [SWAP]
sr0              11:0    1 55.7M  0 rom  /run/media/payam/VMware Tools
```

Lorsque vous faite une installation il y a normalement au moins deux partitions qui ont besoin d'être créé :

* **/ **(root): dossier racine qui contient la distribution Linux.
* **Espace Swap**

### **Qu'est ce que l'espace swap?**

L'espace Swap dans Linux est utilisé lorsque la totalité de la mémoire physique disponible (RAM) est occupé. Si le système a besoin de plus de ressources mémoires et que la RAM est pleine, les pages inacties en mémoire sont déplacer dans l'espace swap. Alors que l'espace swap peut aider les machines avec de faibles capacité de RAM, il ne doit pas être considéré comme un remplacemnet pour plus de RAM. L'espace Swap est placé sur le disque dur, qui a un temps d'accès plus lent que la mémoire physique.

**L'espace Swap peut être** une **parition dédié au swap** (recommendé), un **fichier swap**, ou une **combinaison de paritions swap et de fichier swap**.

Swap doit être équivalent à 2X la RAM physique pour le RAM physique jusqu'à 2GB et environ 1.5X la RAM physique pour la RAM de plus de 2GB. Cepenant c'est une recette ancienne et cela dépend du type de système dont nous parlons.

### **Point de montage**

Toutes les **partitions sont attaché au système via un point de montage**. Le point de montage défini la place d'un ensemble particulier de donnée dans le système de fichier. Généralement, toutes les partitions sont connecté au travers de la partition racine. Sur cettte parition, qui est indiqué avec le slash (/), les dossiers sont créé. Ces dossiers vides sont les points de départ des partitions attachés à eux.

```
[root@centos7-1 ~]# df -h
Filesystem               Size  Used Avail Use% Mounted on
/dev/mapper/centos-root   46G  4.4G   41G  10% /
devtmpfs                 1.9G     0  1.9G   0% /dev
tmpfs                    1.9G     0  1.9G   0% /dev/shm
tmpfs                    1.9G   26M  1.9G   2% /run
tmpfs                    1.9G     0  1.9G   0% /sys/fs/cgroup
/dev/sda1               1014M  179M  836M  18% /boot
tmpfs                    378M   32K  378M   1% /run/user/1000
/dev/sr0                  56M   56M     0 100% /run/media/pabros/VMware Tools
tmpfs                    378M     0  378M   0% /run/user/0
```

Pendant le démarrage du système, toutes les partitions sont montés (et seront décirte dans le fichier /etc/fstab). Certaines partitions ne sont pas monté par défaut, par exemple si elles ne sont **pas connectés en permanence au système, comme les stockage usb**. S'il est bien configuré, le périphérique sera monté dès que le système sera informé qu'il est connecté, ou il peut être monté par l'utilisateur.

### Système de fichiers

**Un système de fichier est l'ensemble des méthodes et des structures de données qu'un système d'exploitation utilise pour garder une trace des fichiers sur un disque ou une partition**; c'est, d'une certaine manière, la manière dont les fichiers sont organisés sur le disque. Sans un système de fichier, les informations placés sur un media de stockage sera un ensemble important de données sans moyen de savoir où est quelle information, où elle s'arrête et où commence la prochaine.

Des systèmes de fichiers différents ont une structure différente pour déterminer où sont les données et indexer l'information qui est stockée :

* **FAT32**: FAT32 est un ancien système de fichier de Windows, mais il est encore utilisé sur les médias amovibles - seulement les plus petit. Les disque dur externes de plus de 1TB sont formatté avec NTFS
* **NTFS**: les versions modernes de Windows — depuis Windows XP — utilise le système de fichier NTFS pour leur système de partitions. Les disques externes peuvent être formatté soit en  FAT32 ou en  NTFS.
* **HFS**+: Macs utilise HFS+ pour ses partitions internes, et aime formater les disques externe également avec  HFS+. 
* **Ext2/Ext3/Ext4**: Vous verrez souvnet les systèmes de fichier Ext2, Ext3, et Ext4 sur Linux.
* **Btrfs**: Btrfs — “better file system” — est un nouveau système de fichier Linux qui est encore en développement. Ce n'est pas celui utilisé par défaut sur la plupart des distribution Linux, mais il remplacera probablement Ext4 un jour. L'objectif est de fournir des fonctionnalités supplémentaire pour permettre à Linux de se mettre à l'échelle sur des stockage plus important.
* **Swap**: Sur Linux, le système de fichier “swap” n'est pas vraiment un système de fichier. Une partition formatté comme “swap” peut seulement être utilisé comme espace swap par le système d'exploitation 

#### Structure des dossiers Linux

Linux utilise un système structuré hiérarhique de fichier, qui est plus ou-moins un arbre à l'envers, avec la racine (/) à la base du système de fichier et tous les autres dossiers qui commence depuis. L'arborescence de linux consiste en de nombreux système de fichier qui peuvent être sur de nombreux périphériques et même sur différents serveurs.

```
[root@centos7-1 ~]# tree / -L 1
/
├── bin -> usr/bin
├── boot
├── dev
├── etc
├── home
├── lib -> usr/lib
├── lib64 -> usr/lib64
├── media
├── mnt
├── opt
├── proc
├── root
├── run
├── sbin -> usr/sbin
├── srv
├── sys
├── tmp
├── usr
└── var

19 directories, 0 files
```

Expliquons ce que sont ces dossier:

* **/** (racine): le système de fichier racine, monté avant que le noyau ne charge le premier processus. Le chargeur de démarrage dit au noyau ce qu'il doit utiliser comme système de fichier racine (c'est généralement une partition du disque mais peut être quelque chose sur le réseau).
* **/bin** : Tous les programmes binaire exécutables (fichier) nécecssaire pendant le démarrage, la réparation, fichier nécessaire au fonctionnement en mode utilisateur unique, et autres commandes basiques importante.
* **/boot** : Contient les fichiers important utiliser durant le processus de démarrage, incluant le noyau Linux.
* **/dev** : un système de fichier en-mémoire où les fichiers de priphériques sont automatiquement créé par udev qui s'appuie sur le matériel disponible. Contient les fichiers de périphériques pour tous les périphériques matériel de la machine, par exemple un lecteur cd, le cpu, etc
* **/etc** : Contient les fichiers de configuration des appplications, les script de démarrage, d'arrêt, de rédémarrage pour chaque programme individuel.
* **/home** : Dossier privé des utilisateurs. Chaque fois qu'un nouvel utilisateur est créé, un dossier du nom de l'utilisateur est créé dans ce dossier home qui contient d'autres dossiers comme Bureau(Desktop), Téléchargements(Downloads), Documents, etc.
* **/lib** : Le dossier Lib contient les modules du noyau et les image partagés des bibliothèques nécessaire au démarrage du système et pour lancer des commande dans le système de fichier racine.
* **/media** : Dossier de montage temporaire créé pour les périphériques amovibles .
* **/mnt** : Dossier de montage temporaire pour monter des systèmes de fichier.
* **/opt** : Optional abrégé comme opt. Contient des logiciels tiers.
* **/proc** : Un pseudo  et virtuel système de fichier qui contient des information sur les processus en cours avec un Process-id aka pid particulier.
* **/root**: C'est le répertoire privé de l'utlisateur racine et  ne doit pas être jamais être confondu avec ‘/‘
* **/run** : Ce dossier est la seule solution propre pour les problème early-runtime-dir.
* **/sbin** : Contient les binaires exécutables des programmes, requis par l'administrateur Système, pour les maintenances.
* **/srv** : Service abrégé comme ‘srv‘. Ce dossier contient les éléments spécifique du serveur et les fichiers liés aux services.
* **/sys** : Les distributions Linux modernes inclus un dossier /sys qui est un systme de fichier virtuel, qui stocke et permet la modification des périphériques connectés au système.
* **/tmp** : Dossier temporaire du système, accessible aux utilisateurs et à l'utilisateur racine. Stocke les fichiers temporaires pour les utilisateur et le système, jusqu'au prochain démarrage.
* **/usr** : Contient les binaires exécutables, la documentation, le code source, les bibliothèque pour les programme de second niveau.
* **/var** : Signifie variable. Le contenu de ce fichier est prévu pour grossir. Ce dossier contient les journaux, les verrous, les messages, ls mails et les fichiers temporaires.

{% hint style="danger" %}
Presque tous les dossier de linux peuvent être sur des partitions séparée, sauf le dossier /etc car il contient des fichiers nécessaire pendant le processus de démarrage donc il doit être monté avec la partition / et accessible au tout début du processus de démarrage.
{% endhint %}

### Modèle de conception des disques durs

Bien sur ce n'est pas parce que nous pouvons partionner un disque, que nous devons systèmatiquement le faire. Plus nous créons de partitions, plus il faudra en gérer. Nous ne devrions donc pas créer de partitions supplémentaire sans avoir considérer le travail supplémentaire que leur création et leur maintenance engendre.

Si la partition racine n'a plus d'espace le système plantera. Si certaines partitions non-racines n'ont plus d'espace, le système fonctionnera et l'administrateur Système peut se connecter et corriger les choses. Ces dossier comme /home, /tmp, et /var que les utilisateurs peuvent facilement remplir sotn les premier candidats pour des partitions supplémentaires. Il y a aussi d'autre dossier qui peuvent grossier (les dossiers de transfert de fichier, les fichiers de base de données, etc).

Si la partition qui contient les fichiers journaux n'a plus d'espace, le système ne pourra plus écrire aucune message de journal qui pourrait aider l'administrateur système à comprendre ce qui ne fonctionne pas. D'un autre côté des erreurs répétées rapidement (ou des attaques) peuvent causer l'augmenttion très importante et rapide des fichiers jouraux et remplir la partition qui les contient. Donc le dossier qui contient les fichiers journaux, (généralement /var/log) est un bon choix pour une partition séparée.

Plus que tout essayer de suivre les recommandations des fournisseurs standard de disque, si non soyez malin et faites votre paritionnement en vous basant sur le type de serveur et le comportement de vos applications.

{% hint style="success" %}
#### Blocks vs. Sectors

**Un secteur est un espace physique sur un disque formatté qui contient des informations.** Lorsqu'un disque est formatté, les pisestes sont définis (anneaux circulaires depuis l'intérieur vers l'extérieur sur le plateau du disque). Chqaue piste est divisé en morceau, qui sont un secteur. Sur les disques durs et les disquette, chaque secteur peut contenir 512 bytes de données.

**Un bloc, de l'autre côté, est un groupe de secteur auquel le système d'exploitation peut s'adressé (pointer vers).** Un block peut être un secteur, ou plusieurs secteurs (2,4,8, ou même 16). Plus grand est le disque, plus un block peut contenir de secteurs.

**Pourquoi il y a des blocs ? Pourquoi le système d'exploitation ne pointe pas directement vers les secteurs ?** Parce qu'il y a une limite au nombre de bloc, ou adresse de destitation, auquel le système d'exploitation peut s'adresser. En définissant un bloc pour plusieurs secteurs, un OS peut travailler avec des diques durs plus grand sans avoir besoin d'augmenter le nombre de blocs à adresser.
{% endhint %}

Jusqu'ici nous avons parler de partitions, les partitions sont pratique mais ont une taille fixe et il n'est pas toujour facile de les redimensionner. Sur un serveur nous avons souvent besoin de **plus de flexibilités**. C'est pourquoi **LVM** a été inventé.

### LVM

Logical volume management (LVM) est une **forme de stockage virtualisé** qui offre aux administrateurs système une approche plus **flexible** pour gérer l'espace disque de stockage que le partitionnement classique.

Il y a 3 concepts que LVM gère :

* **Logical Volumes (LV)**: Un Volume Logique est un concept équivalent à une parition disque avec un système non-LVM. Les volumes logiques **sont des périphériques blocs qui sont créé depuis leur présence physique dans un même groupe de volume**. Les systèmes de fichier sont construit au dessus des volumes logiques.
* **Volume Groups (VG)**: Un groupe de Volume **rassemble une collection de volume logique et de volume physique dans une seule unité d'administration**. Les groupes de volumes sont divisés dans des éléments physiques à taille fixe.
* **Physical Volumes (PV)**: Chaque volume physique peut êre une partition de disque ou un disque entier.

![](.gitbook/assets/disklayout-lvm.jpg)

Logical Volume Management (LVM) rend la gestion de l'espace disque plus facile. Si un système de fichier a besoin de plus d'espace, on peut en ajouter à son volume logique depuis l'espace disponible du groupe de volume et le système de fichier peut êre redimensionner comme nous le souhaitons. Si un disque **commence à ééchouer**, le remplacement de ce disque peut êre enregistré comme unvolume physique avec le groupe de volume et l'extension des volumes logiques peut être migrer sur le nouveau disque sans perte de données.

**Comment fonctionne LVM ?**

Il fonctionne en découpant les volumes physiques (PVs) en niveau physique (Physical extents ou PEs). Les PEs sont mappés à des niveau logique (Logical extents ou LEs) qui sont ensuite réunis dans des groupes de volumes(VGs). Ces groupes sont liés ensemble dans des volumes logiques (LVs) qui agisse comme une parition disque virtuelle et qui peuvent être gérée comme telle en utilisant LVM.

![](.gitbook/assets/disklayout-lvmdetails.jpg)

{% hint style="danger" %}
Il y a un élément en plus que vous devriez connaitre à propos de  LVM, nous ne pouvons mettre 'lintégralité du serveur dans LVM! A cause du dossier /boot qui doit être disponible au moment du démarrage, il doit donc être vu par le MBR (ou la partition GUID) .

Afin de voir le dossier /boot au moment du démarrage il doit toujours être sur une partition classique.
{% endhint %}

![](.gitbook/assets/disklayout-lvmboot.jpg)

Terminé.

## Exercices 

### Exercices guidés

1. Sur les systèmes Linux, où sont stockés les fichiers du chargeur de démarrage GRUB ?

<details>
  <summary>Réponse</summary>
    En-dessous de /boot/grub.
</details>

2. Où doit se terminer la partition de démarrage pour garantir qu’un PC sera toujours capable de charger le noyau ?

<details>
  <summary>Réponse</summary>
    Avant le cylindre 1024.
</details>

3. À quel endroit la partition EFI est-elle généralement montée ?

<details>
  <summary>Réponse</summary>
    En-dessous de /boot/efi.
<details>

4. Lors du montage manuel d’un système de fichiers, sous quel répertoire doit-il généralement être monté ?

<details>
  <summary>Réponse</summary>
    En-dessous de /mnt. Toutefois, ce n’est pas obligatoire. Vous pouvez monter une partition sous n’importe quel répertoire.
</details>

### Exercices d'approfondissement

1. Quelle est la plus petite unité à l’intérieur d’un groupe de volumes VG ?

<details>
  <summary>Réponse</summary>
    Les groupes de volumes sont subdivisés en extensions.
</details>

2. Comment définit-on la taille d’un volume logique LV ?

<details>
  <summary>Réponse</summary>
    Par la taille des extensions physiques multipliée par le nombre d’extensions sur le volume.
</details>

3. Sur un disque formaté selon le schéma de partitionnement MBR, quel est l’ID de la partition système EFI ?

<details>
  <summary>Réponse</summary>
    L’ID est 0xEF.
</details>

4. En dehors des partitions d’échange, comment peut-on augmenter rapidement l’espace swap sur un système Linux ?

<details>
  <summary>Réponse</summary>
    Les fichiers swap peuvent être utilisés.
</details>

.

.

.

.

Sources:

[https://www.studytonight.com/operating-system/secondary-storage](https://www.studytonight.com/operating-system/secondary-storage)

[https://www2.cs.duke.edu/csl/docs/sysadmin_course/sysadm-30.html](https://www2.cs.duke.edu/csl/docs/sysadmin_course/sysadm-30.html)

[http://www.ntfs.com/hard-disk-basics.htm](http://www.ntfs.com/hard-disk-basics.htm)

[https://en.wikipedia.org/wiki/Disk_partitioning](https://legacy.gitbook.com/book/borosan/lpic1-exam-guide/edit#)

[https://www.tldp.org/LDP/sag/html/filesystems.html](https://www.tldp.org/LDP/sag/html/filesystems.html)

[https://en.wikipedia.org/wiki/File_system](https://legacy.gitbook.com/book/borosan/lpic1-exam-guide/edit#)

[https://searchstorage.techtarget.com/definition/file-system](https://searchstorage.techtarget.com/definition/file-system)

[https://www.howtogeek.com/196051/htg-explains-what-is-a-file-system-and-why-are-there-so-many-of-them/](https://www.howtogeek.com/196051/htg-explains-what-is-a-file-system-and-why-are-there-so-many-of-them/)

[https://www.centos.org/docs/5/html/5.2/Deployment_Guide/s1-swap-what-is.html](https://www.centos.org/docs/5/html/5.2/Deployment_Guide/s1-swap-what-is.html)

[https://www.linuxtopia.org/online_books/introduction_to_linux/linux_Mount_points.html](https://www.linuxtopia.org/online_books/introduction_to_linux/linux_Mount_points.html)

[https://unix.stackexchange.com/questions/12040/what-mount-points-exist-on-a-typical-linux-system](https://unix.stackexchange.com/questions/12040/what-mount-points-exist-on-a-typical-linux-system)

[https://www.tutorialspoint.com/unix/unix-file-system.htm](https://www.tutorialspoint.com/unix/unix-file-system.htm)

[https://www.howtogeek.com/117435/htg-explains-the-linux-directory-structure-explained/](https://www.howtogeek.com/117435/htg-explains-the-linux-directory-structure-explained/)

[https://wpollock.com/AUnix1/Partitioning.htm](https://wpollock.com/AUnix1/Partitioning.htm)

[http://www.alphaurax-computer.com/computer-tips/hard-drive-knowledge-blocks-vs-sectors](https://legacy.gitbook.com/book/borosan/lpic1-exam-guide/edit#)

[https://searchdatacenter.techtarget.com/definition/logical-volume-management-LVM](https://searchdatacenter.techtarget.com/definition/logical-volume-management-LVM)

[https://wiki.ubuntu.com/Lvm](https://wiki.ubuntu.com/Lvm)

[https://www.thegeekdiary.com/redhat-centos-a-beginners-guide-to-lvm-logical-volume-manager/](https://www.thegeekdiary.com/redhat-centos-a-beginners-guide-to-lvm-logical-volume-manager/)

[https://www.tecmint.com/create-lvm-storage-in-linux/](https://www.tecmint.com/create-lvm-storage-in-linux/)
