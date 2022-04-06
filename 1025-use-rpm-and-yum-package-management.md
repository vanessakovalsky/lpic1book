# 102.5.  Utiliser les gestionnaire de paquets RPM et YUM

## **102.5 Utiliser les gestionnaire de paquets RPM et YUM**

**Poids:** 3

**Description:** Les candidats doivent être capable de gérer les paquets en utilisant l'outil de paquets RPM et YUM.

**Connaissances clés:**

* Installation, reinstallation, mise à jour et suppression des paquets en utilisant RPM et YUM
* Obtenir les informations sur les paquets RPM comme la version, le statut, les dépendances, l'intégrité et les signatures
* Determiner quels fichiers le paquet fournit, et trouver de quel paquet vient un fichier spécifique 

**Concepts et Utilitaires:**

* rpm
* rpm2cpio
* /etc/yum.conf
* /etc/yum.repos.d/
* yum
* yumdownloader

Dans cette leçon nous allons apprendre comment fonctionne les paquets dans les distributions RedHat. Si vous n'êtes pas familier avec les paquets et les concepts de gestionnaire de paquets dans linux merci d'étudier la permière partie de la leçon précédente.

![](.gitbook/assets/redpack-red.jpg)

## rpm

RPM (Red Hat Package Manager) est un gestionnaire de paquet puissant, qui est utilisé pour construire, installer, requêter, vérifier, mettre à jour et supprimer des paquets logiciels individuels.

Un paquet consiste en une archive de fichiers et de méta-donées utilisé pour installer et supprimer des fichiers archives. Les méta-données inclus des scripts d'aide,  des attributs de fichier et des information descriptive à propos du paquet.

Les paquets vienne avec deux variantes : **paquets binaires**, utilisé pour encapsulé les logiciel à installer, et **paquets source**, contenant le code source et les recette nécessaires pour produire le paquet binaire. RPM est la seule manière d'installer des paquets sur les système Linux, si vous avez installé des pquets en utilisé le code source, alors RPM ne pourra pas les gérer.

![](.gitbook/assets/redpack-rpmbox.jpg)

RPM travaille avec des fichiers .rpm, ce sont eux qui contiennent les informations à propos des paquets comme : ce que c'est, d'où il vient, les informations de dépendances, les informations de versions, etc.

### Où trouver des paquets RPM

La liste des sites rpm, où vous pouvez trouver et télécharger tous les paquets RPM :

* [http://rpmfind.net](http://rpmfind.net)
* [http://www.redhat.com](http://www.redhat.com)
* [http://freshrpms.net/](http://freshrpms.net)
* [http://rpm.pbone.net/](http://rpm.pbone.net)

RPM garde l'information de tous les paquets installé dans la base de données dans /var/lib/rpm.

```
[root@centos7-1 ~]# ls -l /var/lib/rpm
total 86688
-rw-r--r--. 1 root root  3821568 Dec  5 10:02 Basenames
-rw-r--r--. 1 root root    16384 Dec  5 10:02 Conflictname
-rw-r--r--  1 root root   311296 Dec  8 11:26 __db.001
-rw-r--r--  1 root root    90112 Dec  8 11:26 __db.002
-rw-r--r--  1 root root  1318912 Dec  8 11:26 __db.003
-rw-r--r--. 1 root root  1134592 Dec  5 10:02 Dirnames
-rw-r--r--. 1 root root    32768 Dec  5 10:02 Group
-rw-r--r--. 1 root root    20480 Dec  5 10:02 Installtid
-rw-r--r--. 1 root root    69632 Dec  5 10:02 Name
-rw-r--r--. 1 root root    32768 Dec  5 10:02 Obsoletename
-rw-r--r--. 1 root root 78909440 Dec  5 10:02 Packages
-rw-r--r--. 1 root root  2404352 Dec  5 10:02 Providename
-rw-r--r--. 1 root root   520192 Dec  5 10:02 Requirename
-rw-r--r--. 1 root root   131072 Dec  5 10:02 Sha1header
-rw-r--r--. 1 root root    73728 Dec  5 10:02 Sigmd5
-rw-r--r--. 1 root root     8192 Dec  5 10:02 Triggername
```

Il y a tous les fichiers binaires, essayez `cat Basenames` pour voir.

Il y a cinq mode basique pour la commande RPM:

* Install : Est utilisé pour installé n'importe quel paquet RPM.
* Remove : Est utilisé pour effacer, supprimer ou désinstaller n'importe quel paquet RPM.
* Upgrade : Est utilisé pour mettre à jour les paquets RPM existants.
* Verify : Est utilisé pour vérifier un paquet RPM.
* Query : Est utilisé pour requêter n'importe quel paquet RPM.

#### Installer un paquet RPM

\-i est utilisé pour installer un paquet logiciel RPM

```
[root@centos7-1 ~]# rpm -ivh zip-3.0-11.el7.x86_64.rpm 
Preparing...                          ################################# [100%]
Updating / installing...
   1:zip-3.0-11.el7                   ################################# [100%]
```

`-v` verbose pour un affichage plus complet\
,`-h` affiche la marcque chiffré lors de l'extraction de l'archive.

#### Vérifier un paquet RPM installé

`-q` option pour requêter un paquet:

```
[root@centos7-1 ~]# rpm -q zip
zip-3.0-11.el7.x86_64
```

#### Lister tous les fichiers d'un paquet RPM installé

Utiliser `-ql` **(query list)** avec la commande rpm :

```
[root@centos7-1 ~]# rpm -ql zip
/usr/bin/zip
/usr/bin/zipcloak
/usr/bin/zipnote
/usr/bin/zipsplit
/usr/share/doc/zip-3.0
/usr/share/doc/zip-3.0/CHANGES
/usr/share/doc/zip-3.0/LICENSE
/usr/share/doc/zip-3.0/README
/usr/share/doc/zip-3.0/README.CR
/usr/share/doc/zip-3.0/TODO
/usr/share/doc/zip-3.0/WHATSNEW
/usr/share/doc/zip-3.0/WHERE
/usr/share/doc/zip-3.0/algorith.txt
/usr/share/man/man1/zip.1.gz
/usr/share/man/man1/zipcloak.1.gz
/usr/share/man/man1/zipnote.1.gz
/usr/share/man/man1/zipsplit.1.gz
```

#### Requêter un fichier qui appartient à un paquet RPM

`-qf` **(query file) **option pour nous aider à trouver à quel paquet appartient un fichier.

```
[root@centos7-1 ~]# rpm -qf /usr/bin/zipsplit 
zip-3.0-11.el7.x86_64
```

#### Requêter une information sur un paquet RPM installé

`-qi` **(query info)** option affiche les information disponibles sur un paquet installé.

```
[root@centos7-1 ~]# rpm -qi zip
Name        : zip
Version     : 3.0
Release     : 11.el7
Architecture: x86_64
Install Date: Mon 03 Dec 2018 06:05:54 AM EST
Group       : Applications/Archiving
Size        : 815173
License     : BSD
Signature   : RSA/SHA256, Sun 20 Nov 2016 04:04:58 PM EST, Key ID 24c6a8a7f4a80eb5
Source RPM  : zip-3.0-11.el7.src.rpm
Build Date  : Sat 05 Nov 2016 12:49:55 PM EDT
Build Host  : worker1.bsys.centos.org
Relocations : (not relocatable)
Packager    : CentOS BuildSystem <http://bugs.centos.org>
Vendor      : CentOS
URL         : http://www.info-zip.org/Zip.html
Summary     : A file compression and packaging utility compatible with PKZIP
Description :
The zip program is a compression and file packaging utility.  Zip is
analogous to a combination of the UNIX tar and compress commands and
is compatible with PKZIP (a compression and file packaging utility for
MS-DOS systems).

Install the zip package if you need to compress files using the zip
program.
```

#### Obtenir les informations sur un paquet RPM avant l'installation

`-qip` **(query info package)** affiche les infromation d'un paquet, alors que le paquet n'a pas encore été installé et qu'il n'y a rien à lire sur le rpm dans la base de donnée, nous devons donc lui donner le nom complet du paquet et il  nous fournira les informations directement depuis le paquet:

```
[root@centos7-1 ~]# rpm -qip htop-2.2.0-1.el7.x86_64.rpm 
Name        : htop
Version     : 2.2.0
Release     : 1.el7
Architecture: x86_64
Install Date: (not installed)
Group       : Unspecified
Size        : 222746
License     : GPLv2+
Signature   : RSA/SHA256, Wed 30 May 2018 06:30:22 PM EDT, Key ID 6a2faea2352c64e5
Source RPM  : htop-2.2.0-1.el7.src.rpm
Build Date  : Wed 30 May 2018 06:26:05 PM EDT
Build Host  : buildvm-31.phx2.fedoraproject.org
Relocations : (not relocatable)
Packager    : Fedora Project
Vendor      : Fedora Project
URL         : http://hisham.hm/htop/
Bug URL     : https://bugz.fedoraproject.org/htop
Summary     : Interactive process viewer
Description :
htop is an interactive text-mode process viewer for Linux, similar to
top(1).
```

`-p` est utilisé pour requêter des informations depuis un paquet rpm. Nous pouvons ne pas mettre l'option -p et il utilisera le nom du paquet à la place `rpm -qi htop` .

#### Vérifier les dépendances du paquet RPM avant son installation

utiliser les options `-qpR` pour vérifier les dépendances avant l'installation ou la mise à jour d'un paquet

```
[root@centos7-1 ~]# rpm -qpR htop-2.2.0-1.el7.x86_64.rpm 
libc.so.6()(64bit)
libc.so.6(GLIBC_2.14)(64bit)
libc.so.6(GLIBC_2.15)(64bit)
libc.so.6(GLIBC_2.2.5)(64bit)
libc.so.6(GLIBC_2.3)(64bit)
libc.so.6(GLIBC_2.3.4)(64bit)
libc.so.6(GLIBC_2.4)(64bit)
libc.so.6(GLIBC_2.8)(64bit)
libgcc_s.so.1()(64bit)
libgcc_s.so.1(GCC_3.0)(64bit)
libgcc_s.so.1(GCC_3.3.1)(64bit)
libm.so.6()(64bit)
libm.so.6(GLIBC_2.2.5)(64bit)
libncursesw.so.5()(64bit)
libtinfo.so.5()(64bit)
rpmlib(CompressedFileNames) <= 3.0.4-1
rpmlib(FileDigests) <= 4.6.0-1
rpmlib(PayloadFilesHavePrefix) <= 4.0-1
rtld(GNU_HASH)
rpmlib(PayloadIsXz) <= 5.2-1
```

`-R` Listes les dépendances du paquet. Nous pouvons facilement utilisé `rpm -qR PackageName` pour requêter un paquet installé.

**Requêter le fichier de configuration d'un paquet RPM installé**

`-qc` **(query configuration file)** requête pour un paquet installé de ses fichiers de configuration, s'il en a :

```
[root@centos7-1 ~]# rpm -qc vsftpd
/etc/logrotate.d/vsftpd
/etc/pam.d/vsftpd
/etc/vsftpd/ftpusers
/etc/vsftpd/user_list
/etc/vsftpd/vsftpd.conf
```

Si le paquet n'est pas installé utiliser `-qpc` à la place pour requêter un paquet rpm.

#### Requêter la documentation d'un paquet RPM installé

`-qdf`**(query document file)** donne une liste des documentation disponible pour un paquet installé:

```
[root@centos7-1 ~]# which zip
/bin/zip
[root@centos7-1 ~]# rpm -qdf /bin/zip
/usr/share/doc/zip-3.0/CHANGES
/usr/share/doc/zip-3.0/LICENSE
/usr/share/doc/zip-3.0/README
/usr/share/doc/zip-3.0/README.CR
/usr/share/doc/zip-3.0/TODO
/usr/share/doc/zip-3.0/WHATSNEW
/usr/share/doc/zip-3.0/WHERE
/usr/share/doc/zip-3.0/algorith.txt
/usr/share/man/man1/zip.1.gz
/usr/share/man/man1/zipcloak.1.gz
/usr/share/man/man1/zipnote.1.gz
/usr/share/man/man1/zipsplit.1.gz
```

#### Vérifier un paquet installé

`-V`**(verify)** vérifie un paquet :

```
[root@centos7-1 ~]# rpm -V telnet
[root@centos7-1 ~]#
```

Aucune information est une bonne chose. Lors de la vérification 'un paquet, RPM produit une sortie seulement s'il y a une errreur sur la vérification. Lorsqu'un fichier échoue la vérification, le format de sortie est un peu complexe, mais il rassemble toutes les informations dont vous avez besoin en une ligne par fichier. Voici le fomat : **`SM5DLUGT c <file>`**` . As an example:`

```
[root@centos7-1 ~]# rpm -V vsftpd 
S.5....T.  c /etc/vsftpd/ftpusers
```

Où:

* **S** est la taille du fichier.
* **M** est le mode du fichier.
* **5** est la vérification de la signature MD5 du fichier.
* **D** est le numéro de version majeur et mineur du fichier.
* **L** est le contenu du lien symbolique d fichier.
* **U** est le propriétaire du fichier.
* **G** est le groupe du fichier.
* **T** est l'heure de modification du fichier.
* **c** apparait seulement si c'est un fichier de configuration. C'est pratique pour l'identification rapide des fichiers de configurations, comme ils changent souvent, et aussi peut pratique pour le succès de la vérification.
* **\<file>** est le fichier qui a échoué la vérification. Le chemin complet est listé pour le trouver plus facilement.

#### Verify all RPM Packages

Utiliser `-Va`**(Verify all)** pour vérifier tous les paquets rpm installé.

```
[root@centos7-1 ~]# rpm -Va
S.5....T.  c /etc/cgrules.conf
missing     /var/run/pluto
....L....  c /etc/pam.d/fingerprint-auth
....L....  c /etc/pam.d/password-auth
....L....  c /etc/pam.d/postlogin
....L....  c /etc/pam.d/smartcard-auth
....L....  c /etc/pam.d/system-auth
missing     /var/run/gluster
S.5....T.  c /etc/yum/pluginconf.d/langpacks.conf
S.5....T.  c /etc/plymouth/plymouthd.conf
S.5....T.  c /etc/cups/cups-browsed.conf
.M....G..    /var/log/gdm
missing     /var/run/pulse
S.5....T.  c /etc/sysconfig/authconfig
missing     /var/run/wpa_supplicant
```

Utiliser `rpm -Vac` pour vérifier seulement les fichiers de configuration. Il y a des scripts systèmes communs qui changent régulièrement.

#### Vérifier un paquet RPM 

Vérifie un paquet en comparant les informations des fichiers installés avec celle du paquet dans la base de données.

The`-Vp` **(verify package)** est utilisé pour vérifier un paquet.

```
[root@centos7-1 ~]# rpm -Vp zip-3.0-11.el7.x86_64.rpm 
[root@centos7-1 ~]# rpm -Vp htop-2.2.0-1.el7.x86_64.rpm 
missing     /usr/bin/htop
missing     /usr/share/applications/htop.desktop
missing     /usr/share/doc/htop-2.2.0
missing   d /usr/share/doc/htop-2.2.0/AUTHORS
missing   d /usr/share/doc/htop-2.2.0/ChangeLog
missing   d /usr/share/doc/htop-2.2.0/README
missing     /usr/share/licenses/htop-2.2.0
missing   l /usr/share/licenses/htop-2.2.0/COPYING
missing   d /usr/share/man/man1/htop.1.gz
missing     /usr/share/pixmaps/htop.png
```

#### Lister tous les paquets RPM installés

L'option `-qa` **(query all)**, liste tous les paquets rpm installés.

```
[root@centos7-1 ~]# rpm -qa
NetworkManager-team-1.8.0-9.el7.x86_64
cdparanoia-libs-10.2-17.el7.x86_64
gtkmm30-3.22.0-1.el7.x86_64
python-configshell-1.1.fb23-3.el7.noarch
khmeros-base-fonts-5.0-17.el7.noarch
liberation-fonts-common-1.07.2-15.el7.noarch
kexec-tools-2.0.14-17.el7.x86_64
iptables-1.4.21-18.0.1.el7.centos.x86_64
gnome-dictionary-libs-3.20.0-1.el7.x86_64
gd-2.0.35-26.el7.x86_64
setserial-2.17-33.el7.x86_64
mozilla-filesystem-1.9-11.el7.x86_64
...
<the output has been truncated>
```

Utiliser l'option `--last` pour lister les paquets RPM installé récemment.

#### Mettre à jour un paquet RPM

`-U` met à jour ou installe le paquet actuellement installé vers une nouvelle version. C'est la même chose, à part que les autres versions du paquets sont supprimé après que le nouveau paquet soit installé.

```
[root@centos7-1 ~]# rpm -Uvh telnet-0.17-64.el7.x86_64.rpm 
Preparing...                          ################################# [100%]
Updating / installing...
   1:telnet-1:0.17-64.el7             ################################# [100%]
```

Un des avantages majeurs à l'utilisation de cette option est qu'il ne va pas seulement mettre à jour tous les paquets, mais qu'il va aussi garder une sauvegarde de l'ancien paquet de sorte à ce que si le nouveau paquet mis à jour ne fonctionne pas, le précédent paquet puisse être encore utilisé. (via rpm   --oldpackage switch ou en utilisant la commande `yum downgrade` )

> **rpm -ivh vs rpm -Uvh**
>
> `rpm -ivh` installe seulement un paquet et si le paquet est déjà installé il ne fait rien, alors que  `rpm -Uvh` l'installe même s'il existe.
>
> Egalement, `rpm -ivh` peut causer le fait d'avoir de multiples versions d'un paquet en même temps, alors qu'en utilisant `rpm -Uvh` nous sommes sur d'avoir seulement la dernière version.

\-F met à jour le paquet, mais seulement ceux dont une version précédente est installé.( signifie met à jour si installé sinon ne fait rien)

#### Supprimer un paquet RPM

L'option `-e`**(erase)**  est utilisé pour supprimé un paquet (ajouter `v` ou `vv` pour plus d'informations):

```
[root@centos7-1 ~]# rpm -evv zip
D: loading keyring from pubkeys in /var/lib/rpm/pubkeys/*.key
D: couldn't find any keys in /var/lib/rpm/pubkeys/*.key
D: loading keyring from rpmdb
D: opening  db environment /var/lib/rpm cdb:0x401
D: opening  db index       /var/lib/rpm/Packages 0x400 mode=0x0
D: locked   db index       /var/lib/rpm/Packages
D: opening  db index       /var/lib/rpm/Name 0x400 mode=0x0
D:  read h#    1319 Header SHA1 digest: OK (489efff35e604042709daf46fb78611fe90a75aa)
D: added key gpg-pubkey-f4a80eb5-53a7ff4b to keyring
D:  read h#    1337 Header SHA1 digest: OK (dd737a402556b7653c2bc971f343532046e26384)
D: added key gpg-pubkey-352c64e5-52ae6884 to keyring
D: Using legacy gpg-pubkey(s) from rpmdb
D:  read h#    1341 Header V3 RSA/SHA256 Signature, key ID f4a80eb5: OK
D: opening  db index       /var/lib/rpm/Conflictname 0x400 mode=0x0
D: ========== --- zip-3.0-11.el7 x86_64/linux 0x2
D: opening  db index       /var/lib/rpm/Requirename 0x400 mode=0x0
D: ========== recording tsort relations
D: ========== tsorting packages (order, #predecessors, #succesors, depth)
D:     0    0    0    1   -zip-3.0-11.el7.x86_64
D: erasing packages
D: Selinux disabled.
D: closed   db index       /var/lib/rpm/Conflictname
D: closed   db index       /var/lib/rpm/Requirename
D: closed   db index       /var/lib/rpm/Name
D: closed   db index       /var/lib/rpm/Packages
D: closed   db environment /var/lib/rpm
D: opening  db environment /var/lib/rpm cdb:0x401
D: opening  db index       /var/lib/rpm/Packages (none) mode=0x42
D: sanity checking 1 elements
D: running pre-transaction scripts
D: computing 17 file fingerprints
D: opening  db index       /var/lib/rpm/Name (none) mode=0x42
D: opening  db index       /var/lib/rpm/Basenames (none) mode=0x42
D: opening  db index       /var/lib/rpm/Group (none) mode=0x42
D: opening  db index       /var/lib/rpm/Requirename (none) mode=0x42
D: opening  db index       /var/lib/rpm/Providename (none) mode=0x42
D: opening  db index       /var/lib/rpm/Conflictname (none) mode=0x42
D: opening  db index       /var/lib/rpm/Obsoletename (none) mode=0x42
D: opening  db index       /var/lib/rpm/Triggername (none) mode=0x42
D: opening  db index       /var/lib/rpm/Dirnames (none) mode=0x42
D: opening  db index       /var/lib/rpm/Installtid (none) mode=0x42
D: opening  db index       /var/lib/rpm/Sigmd5 (none) mode=0x42
D: opening  db index       /var/lib/rpm/Sha1header (none) mode=0x42
Preparing packages...
D: computing file dispositions
D: 0x0000fd00     4096     10667163     23530388 /
D: ========== +++ zip-3.0-11.el7 x86_64-linux 0x2
D:  read h#    1341 Header V3 RSA/SHA256 Signature, key ID f4a80eb5: OK
D:     erase: zip-3.0-11.el7 has 17 files
zip-3.0-11.el7.x86_64
D: erase      100644  1 (   0,   0)   619 /usr/share/man/man1/zipsplit.1.gz
D: erase      100644  1 (   0,   0)   819 /usr/share/man/man1/zipnote.1.gz
D: erase      100644  1 (   0,   0)   951 /usr/share/man/man1/zipcloak.1.gz
D: erase      100644  1 (   0,   0) 28496 /usr/share/man/man1/zip.1.gz
D: erase      100644  1 (   0,   0)  3395 /usr/share/doc/zip-3.0/algorith.txt
D: erase      100644  1 (   0,   0) 13167 /usr/share/doc/zip-3.0/WHERE
D: erase      100644  1 (   0,   0) 15731 /usr/share/doc/zip-3.0/WHATSNEW
D: erase      100644  1 (   0,   0)  6675 /usr/share/doc/zip-3.0/TODO
D: erase      100644  1 (   0,   0)  6430 /usr/share/doc/zip-3.0/README.CR
D: erase      100644  1 (   0,   0) 12748 /usr/share/doc/zip-3.0/README
D: erase      100644  1 (   0,   0)  3412 /usr/share/doc/zip-3.0/LICENSE
D: erase      100644  1 (   0,   0)210354 /usr/share/doc/zip-3.0/CHANGES
D: erase      040755  2 (   0,   0)     6 /usr/share/doc/zip-3.0
D: erase      100755  1 (   0,   0)100096 /usr/bin/zipsplit
D: erase      100755  1 (   0,   0) 95984 /usr/bin/zipnote
D: erase      100755  1 (   0,   0)100456 /usr/bin/zipcloak
D: erase      100755  1 (   0,   0)215840 /usr/bin/zip
D:   --- h#    1341 zip-3.0-11.el7.x86_64
D: removing "zip" from Name index.
D: removing 17 entries from Basenames index.
D: removing "Applications/Archiving" from Group index.
D: removing 13 entries from Requirename index.
D: removing 2 entries from Providename index.
D: removing 4 entries from Dirnames index.
D: removing 1 entries from Installtid index.
D: removing 1 entries from Sigmd5 index.
D: removing "ccee29b0ebdbefdec09bb543d7ef43882836c1da" from Sha1header index.
D: running post-transaction scripts
D: closed   db index       /var/lib/rpm/Sha1header
D: closed   db index       /var/lib/rpm/Sigmd5
D: closed   db index       /var/lib/rpm/Installtid
D: closed   db index       /var/lib/rpm/Dirnames
D: closed   db index       /var/lib/rpm/Triggername
D: closed   db index       /var/lib/rpm/Obsoletename
D: closed   db index       /var/lib/rpm/Conflictname
D: closed   db index       /var/lib/rpm/Providename
D: closed   db index       /var/lib/rpm/Requirename
D: closed   db index       /var/lib/rpm/Group
D: closed   db index       /var/lib/rpm/Basenames
D: closed   db index       /var/lib/rpm/Name
D: closed   db index       /var/lib/rpm/Packages
D: closed   db environment /var/lib/rpm
```

rpm s'occupe des dépendance lorsqu'il supprime un paquet et ne supprime pas les dépendance du paquet. D'un autre côté si un paquet est requis par un autre paquet rpm évide de le supprimer. L'option  `–nodeps` **(Do not check dependencies)**  **force** la suppression du paquet rpm du système. Mais garder en tête que supprimer certains paquets particulier peut casser d'autres applications qui tournent.

note: Si vous avez fait des modifications sur un fichier de configuration installé à la base par RM, les modification ne seront pas perdues si vous effacer le paquet. 

Ce que vous pouvez faire est juste lancer `rpm -qc packageName` qui vous montrera quels sont les fichiers de configuration qui ont été installé sur votre système par un paquet rpm. Lorsque vous désinstaller le paquet rpm, vous pouvez rechercher dans votre sysème si les fichiers ou leurs sauvegardes reste sur votre système et les supprimer manuellement.

### rpm2cpio

De temps en temps, nous pouvons avoir besoin d'extraire un ou plusieurs fichiers d'un paquet. Une manière de le faire est de :

1. Installer le paquet
2. Faire une copie du fichier dont vous avez besoin
3. Supprimer le paquet

Une manière plus simple est d'utiliser **rpm2cpio**.

**Quels sont les fichiers dans un paquet RPM ?**

![](.gitbook/assets/redpack-rpm.jpg)

Un paquet rpm est constitué de plusieurs fichiers et dossier qui sont archivé au format cpio, de plus certaines description et dépendances ont été ajouté à ça.

**Que fait rpm2cpio ?**

Comme son nom implique, rpm2cpio prend un fichier de paquet RPM et le convertit en archive cpio. Ensuite nous avons besoin d'ouvrir l'archive cpio pour voir la structure originale des fichiers.

![](.gitbook/assets/redpack-rpm2cpio.jpg)

Dans ce cas, l'option `-i` extrait un ou plusieurs fichiers d'une archive, `-v`verbose, liste les fichiers présent et  `-d`crée les dossiers nécessaires (nous parlerons de cpio dans un prochain cours)

rpm2cpio prend seulement un argument, et même celui-ci est optionnel! L'argument optionnel est le nom du fichier de paquet qui doit être converti. (Si aucun nom de fichier n'est spécifié à la ligne de commande, rpm2cpio lira l'enrée standard et la convertira en archive cpio.)

```
[root@centos7-1 temp]# ls
zip-3.0-11.el7.x86_64.rpm

[root@centos7-1 temp]# rpm2cpio zip-3.0-11.el7.x86_64.rpm > zip.cpio

[root@centos7-1 temp]# ls
zip-3.0-11.el7.x86_64.rpm  zip.cpio

[root@centos7-1 temp]# cpio -idv < zip.cpio 
./usr/bin/zip
./usr/bin/zipcloak
./usr/bin/zipnote
./usr/bin/zipsplit
./usr/share/doc/zip-3.0
./usr/share/doc/zip-3.0/CHANGES
./usr/share/doc/zip-3.0/LICENSE
./usr/share/doc/zip-3.0/README
./usr/share/doc/zip-3.0/README.CR
./usr/share/doc/zip-3.0/TODO
./usr/share/doc/zip-3.0/WHATSNEW
./usr/share/doc/zip-3.0/WHERE
./usr/share/doc/zip-3.0/algorith.txt
./usr/share/man/man1/zip.1.gz
./usr/share/man/man1/zipcloak.1.gz
./usr/share/man/man1/zipnote.1.gz
./usr/share/man/man1/zipsplit.1.gz
1598 blocks

[root@centos7-1 temp]# ls
usr  zip-3.0-11.el7.x86_64.rpm  zip.cpio
[root@centos7-1 temp]# tree usr/
usr/
├── bin
│   ├── zip
│   ├── zipcloak
│   ├── zipnote
│   └── zipsplit
└── share
    ├── doc
    │   └── zip-3.0
    │       ├── algorith.txt
    │       ├── CHANGES
    │       ├── LICENSE
    │       ├── README
    │       ├── README.CR
    │       ├── TODO
    │       ├── WHATSNEW
    │       └── WHERE
    └── man
        └── man1
            ├── zip.1.gz
            ├── zipcloak.1.gz
            ├── zipnote.1.gz
            └── zipsplit.1.gz

6 directories, 16 files
```

While there's nothing wrong with using rpm2cpio to actually create a cpio archive file, it's takes a few more steps and uses a bit more disk space than is strictly necessary. A somewhat cleaner approach would be to pipe rpm2cpio's output directly into cpio:

```
[root@centos7-1 temp]# rm -rf usr/

[root@centos7-1 temp]# ls
zip-3.0-11.el7.x86_64.rpm  zip.cpio

[root@centos7-1 temp]# rpm2cpio zip-3.0-11.el7.x86_64.rpm | cpio -t
./usr/bin/zip
./usr/bin/zipcloak
./usr/bin/zipnote
./usr/bin/zipsplit
./usr/share/doc/zip-3.0
./usr/share/doc/zip-3.0/CHANGES
./usr/share/doc/zip-3.0/LICENSE
./usr/share/doc/zip-3.0/README
./usr/share/doc/zip-3.0/README.CR
./usr/share/doc/zip-3.0/TODO
./usr/share/doc/zip-3.0/WHATSNEW
./usr/share/doc/zip-3.0/WHERE
./usr/share/doc/zip-3.0/algorith.txt
./usr/share/man/man1/zip.1.gz
./usr/share/man/man1/zipcloak.1.gz
./usr/share/man/man1/zipnote.1.gz
./usr/share/man/man1/zipsplit.1.gz
1598 blocks
[root@centos7-1 temp]# ls
zip-3.0-11.el7.x86_64.rpm  zip.cpio
```

Nous utilisons l'option`-t` pour que cpio produise directement une "table des matières" de l'archive qu'il a créa avec rpm2cpio. Cela rend plus facile d'avoir le bon nom de fichier et le chemin pour extraire un fichier. Une manière plus simple d'extraire serait  `rpm2cpio package.rpm | cpio id .`

{% hint style="danger" %}
Attention! Soyez prudent lorsque vous supprimé un paquet extrait (usr vs /usr)
{% endhint %}

## YUM

YUM (Yellowdog Updater Modified) est un utilitaire de gestion de paquet en ligne de commande open-source pour les système d'exploitation Linux qui utilise les gestionnaires de paquet RPM. Yum permet des mises à jour automatique, la gestion des paquets et des dépendances, sur les distributions basés sur RPM.

En tant qu'outil haut-niveau, comme Advanced Packaging Tool (APT) sur Debian, yum travaillle avec des dépôts de logiciels (collections de paquets), qui peuvent être accéder localement ou au travers d'une connexion réseau.

Yum est implémenté comme bibliothèques dans le langage de programmation Pytho, avec un petit ensemble de programmes qui fournisse une interface en ligne de commande. Des interfaces graphiques qui s'appuie sur YUM comme **yumex** existe également.

### /etc/yum.conf

Le fichier de configuration pour yum et les utilitaires liés se trouve dans /etc/yum.conf.

```
[root@centos7-1 ~]# cat /etc/yum.conf 
[main]
cachedir=/var/cache/yum/$basearch/$releasever
keepcache=0
debuglevel=2
logfile=/var/log/yum.log
exactarch=1
obsoletes=1
gpgcheck=1
plugins=1
installonly_limit=5
bugtracker_url=http://bugs.centos.org/set_project.php?project_id=23&ref=http://bugs.centos.org/bug_report_page.php?category=yum
distroverpkg=centos-release


#  This is the default, if you make this bigger yum won't see if the metadata
# is newer on the remote and so you'll "gain" the bandwidth of not having to
# download the new metadata and "pay" for it by yum not having correct
# information.
#  It is esp. important, to have correct metadata, for distributions like
# Fedora which don't keep old packages around. If you don't like this checking
# interupting your command line usage, it's much better to have something
# manually check the metadata once an hour (yum-updatesd will do this).
# metadata_expire=90m

# PUT YOUR REPOS HERE OR IN separate files named file.repo
# in /etc/yum.repos.d
```

Ce fichier contient une section obligatoire \[main] , qui nous permet de définir des optoiins à Yum qui ont un effet globales, et peut aussi contenir une ou plusieurs sections \[repository], qui nous permette de définir des options spécifiques à un dépôt. Cependant, il est recommandé de définir des dépôt individuel dans des fichiers nouveaux ou existants dans le dossier /etc/yum.repos.d/ .

### /etc/yum.repos.d

Les fichiers de configuration des dépôt de YUM son stockés dans le dossier /etc/yum.repos.d. Il contient différents fichiers .repo.

```
[root@centos7-1 ~]# ls /etc/yum.repos.d/
CentOS-Base.repo       CentOS-fasttrack.repo  CentOS-Vault.repo
CentOS-CR.repo         CentOS-Media.repo      epel.repo
CentOS-Debuginfo.repo  CentOS-Sources.repo    epel-testing.repo
```

Voyons le contenu du fichier CentOS-Base.repo :

```
[root@centos7-1 ~]# cat /etc/yum.repos.d/CentOS-Base.repo 
# CentOS-Base.repo
#
# The mirror system uses the connecting IP address of the client and the
# update status of each mirror to pick mirrors that are updated to and
# geographically close to the client.  You should use this for CentOS updates
# unless you are manually picking other mirrors.
#
# If the mirrorlist= does not work for you, as a fall back you can try the 
# remarked out baseurl= line instead.
#
#

[base]
name=CentOS-$releasever - Base
mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
#baseurl=http://mirror.centos.org/centos/$releasever/os/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

#released updates 
[updates]
name=CentOS-$releasever - Updates
mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
#baseurl=http://mirror.centos.org/centos/$releasever/updates/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

#additional packages that may be useful
[extras]
name=CentOS-$releasever - Extras
mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
#baseurl=http://mirror.centos.org/centos/$releasever/extras/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

#additional packages that extend functionality of existing packages
[centosplus]
name=CentOS-$releasever - Plus
mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
#baseurl=http://mirror.centos.org/centos/$releasever/centosplus/$basearch/
gpgcheck=1
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
```

Les fichiers de configuration du dépôt dise à yum les information sur le dépôt actuel (où sont les fichiers de paquets physiquement). Alors qu'il y a de nombreux éléments optionnels, chaque fichier .repo doit contenir les éléments suivants :

* **Repository ID** - Un identifiant unique en un mot du dépôt par exemple \[localrepo].
* **Name** - Un nom lisible pour un humain du dépôt par exemple. name=Awesome Local Repo
* **MirrorList** : Une liste de mirroir qui est une liste d'URLs où les dépôt de paquets sont stockés / présents.
* **Baseurl** - Une URL vers le dossier qui contient les données sur le dépôt (où les fichiers actuels sont gardés). Les adresses file://path, ftp://link, [http://link](http://link), et [https://link](https://link) sont toutes des options valides.
* **Enabled** - Si oui ou non le déoôt est activer pour l'utilisation lors des actions de mise à jour et d'installation par exemple enabled=1 (1 signifie "utiliser ce dépôt", 0 signifie "ne pas utiliser ce dépôt").
* **Gpgcheck **- Activer/ désactiver la vérification de signature GPG  (exemple: gpgcheck=1)
* **Gpgkey **- URL de la clé GPG (exemple: gpgkey=[http://mirror.cisp.com/CentOS/6/os/i386/RPM-GPG-KEY-CentOS-6\\](http://mirror.cisp.com/CentOS/6/os/i386/RPM-GPG-KEY-CentOS-6\)/)

{% hint style="info" %}
GPG est une vérification de signature digitale, qui est utilisé pour vérifié si un paquet a été modifié entre les téléchragement ou après avoir construit le paquet. Il aide à vérifier que vous installer des paquets corrects dans aucune modification tieres ou d'un hacker.
{% endhint %}

YUM peut utiliser de nombreux dépôts tiers pour installer ces paquets automatiquement et résoudre les problèmes liés aux dépendances.

### Lister les dépôts YUM activés

`yum repolist` lister tous les dépôts Yum activés sur votre système :

```
[root@centos7-1 ~]# yum repolist 
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: bay.uchicago.edu
 * epel: mirror.karneval.cz
 * extras: mirror.atlanticmetro.net
 * updates: mirror.atlanticmetro.net
repo id                  repo name                                               status
base/7/x86_64            CentOS-7 - Base                                          9,911
epel/x86_64              Extra Packages for Enterprise Linux 7 - x86_64          12,725
extras/7/x86_64          CentOS-7 - Extras                                          434
updates/7/x86_64         CentOS-7 - Updates                                       1,614
repolist: 24,684
```

Pour lister les dépôts activés et désactivés utiliser la commande `yum repolist all`.

### Installer un paquet avec YUM

Nous pouvons installer de nouveaux logiciels sur les distributions Linux Red Hat/CentOS avec la commande `yum install packagename` dans une console.

Lancer la commande va d'abord vérifier que les ficheirs de configuration du dépôt YUM existe dans le dossier /etc/yum.repos.d/. Il lit chaque fichier de configuration de dépôt YUM et obtient les informations nécessaire pour télécharger et installer le nouveau logiciel, résoudre ses dépendances et installer les fichiers requis du paquet RPM.

```
[root@centos7-1 ~]# yum install zip
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: bay.uchicago.edu
 * epel: epel.mirror.far.fi
 * extras: mirror.atlanticmetro.net
 * updates: mirror.atlanticmetro.net
Resolving Dependencies
--> Running transaction check
---> Package zip.x86_64 0:3.0-11.el7 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

=======================================================================================
 Package          Arch                Version                  Repository         Size
=======================================================================================
Installing:
 zip              x86_64              3.0-11.el7               base              260 k

Transaction Summary
=======================================================================================
Install  1 Package

Total download size: 260 k
Installed size: 796 k
Is this ok [y/d/N]: y
Downloading packages:
zip-3.0-11.el7.x86_64.rpm                                       | 260 kB  00:00:46     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : zip-3.0-11.el7.x86_64                                               1/1 
  Verifying  : zip-3.0-11.el7.x86_64                                               1/1 

Installed:
  zip.x86_64 0:3.0-11.el7                                                              

Complete!
```

Utiliser l'option `-y` si vous ne voulez pas que l'on vous demande une confirmation. Si vous avez un paquet rpm sur votre machine utiliser `yum localinstall abc.rpm` .

### Supprimer un paquet avec YUM

`yum remove packagename` supprime un paquet complètement avec toutes ses dépendances.

```
[root@centos7-1 ~]# yum remove zip
Loaded plugins: fastestmirror, langpacks
Resolving Dependencies
--> Running transaction check
---> Package zip.x86_64 0:3.0-11.el7 will be erased
--> Finished Dependency Resolution

Dependencies Resolved

=======================================================================================
 Package          Arch                Version                 Repository          Size
=======================================================================================
Removing:
 zip              x86_64              3.0-11.el7              @base              796 k

Transaction Summary
=======================================================================================
Remove  1 Package

Installed size: 796 k
Is this ok [y/N]: y
Downloading packages:
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Erasing    : zip-3.0-11.el7.x86_64                                               1/1 
  Verifying  : zip-3.0-11.el7.x86_64                                               1/1 

Removed:
  zip.x86_64 0:3.0-11.el7                                                              

Complete!
```

De la même façon la commande remove vous demandera confirmation avant de supprimer un paquet. Pour la désactiver ajouter juste l'option `-y` .

### Recherche un paquet en utilisant YUM

Utiliser la fonction `yum search` pour rechercher dans tous les paquets disponibles ceux qui correspondent au nom de paquet que vous avez specifié.

```
[root@centos7-1 ~]# yum search firefox
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: bay.uchicago.edu
 * epel: epel.mirror.far.fi
 * extras: mirror.atlanticmetro.net
 * updates: mirror.atlanticmetro.net
================================ N/S matched: firefox =================================
firefox.x86_64 : Mozilla Firefox Web browser
firefox.i686 : Mozilla Firefox Web browser
firefox-noscript.noarch : JavaScript white list extension for Mozilla Firefox
firefox-pkcs11-loader.x86_64 : Helper script for Firefox that sets up the browser for
                             : authentication with Estonian ID-card
mozilla-adblockplus.noarch : Adblocking extension for Mozilla Firefox, Thunderbird, and
                           : SeaMonkey
mozilla-https-everywhere.noarch : HTTPS enforcement extension for Mozilla Firefox
mozilla-noscript.noarch : JavaScript white list extension for Mozilla Firefox
mozilla-requestpolicy.noarch : Firefox and Seamonkey extension that gives you control
                             : over cross-site requests
mozilla-ublock-origin.noarch : An efficient blocker for Firefox
webextension-token-signing.x86_64 : Firefox extension for signing with your eID on the
                                  : web

  Name and summary matches only, use "search all" for everything.
```

### Obtenir des informations sur un paquet en utilisant YUM

Avec `yum info packagename` nous pouvons obtenir des information sur un paquet avant de l'installé ou sur un paquet déjà installé :

```
[root@centos7-1 ~]# htop
bash: htop: command not found...
Similar command is: 'top'
[root@centos7-1 ~]# yum info htop
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: bay.uchicago.edu
 * epel: epel.mirror.far.fi
 * extras: mirror.atlanticmetro.net
 * updates: mirror.atlanticmetro.net
Available Packages
Name        : htop
Arch        : x86_64
Version     : 2.2.0
Release     : 1.el7
Size        : 103 k
Repo        : epel/x86_64
Summary     : Interactive process viewer
URL         : http://hisham.hm/htop/
License     : GPLv2+
Description : htop is an interactive text-mode process viewer for Linux, similar to
            : top(1).
```

### Obtenir des information sur les dépendances d'un paquet

Utiliser la commande `yum deplist` pour connaitre les dépendances d'un paquet qui est installé sur notre système :

```
[root@centos7-1 ~]# yum deplist htop
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: bay.uchicago.edu
 * epel: mirror.23media.de
 * extras: mirror.atlanticmetro.net
 * updates: mirror.atlanticmetro.net
package: htop.x86_64 2.2.0-1.el7
  dependency: libc.so.6(GLIBC_2.15)(64bit)
   provider: glibc.x86_64 2.17-222.el7
  dependency: libgcc_s.so.1()(64bit)
   provider: libgcc.x86_64 4.8.5-28.el7_5.1
  dependency: libgcc_s.so.1(GCC_3.0)(64bit)
   provider: libgcc.x86_64 4.8.5-28.el7_5.1
  dependency: libgcc_s.so.1(GCC_3.3.1)(64bit)
   provider: libgcc.x86_64 4.8.5-28.el7_5.1
  dependency: libm.so.6()(64bit)
   provider: glibc.x86_64 2.17-222.el7
  dependency: libm.so.6(GLIBC_2.2.5)(64bit)
   provider: glibc.x86_64 2.17-222.el7
  dependency: libncursesw.so.5()(64bit)
   provider: ncurses-libs.x86_64 5.9-14.20130511.el7_4
  dependency: libtinfo.so.5()(64bit)
   provider: ncurses-libs.x86_64 5.9-14.20130511.el7_4
  dependency: rtld(GNU_HASH)
   provider: glibc.x86_64 2.17-222.el7
   provider: glibc.i686 2.17-222.el7
```

`yum install` les installe toutes automatiquement.

### Fonctions fournies par Yum

`yum provide` trouve des paquet qui fournisse les fichiers demandés:

```
[root@centos7-1 ~]# yum provides /etc/updatedb.conf 
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: bay.uchicago.edu
 * epel: epel.besthosting.ua
 * extras: mirror.atlanticmetro.net
 * updates: mirror.atlanticmetro.net
mlocate-0.26-8.el7.x86_64 : An utility for finding files by name
Repo        : base
Matched from:
Filename    : /etc/updatedb.conf



mlocate-0.26-6.el7.x86_64 : An utility for finding files by name
Repo        : @anaconda
Matched from:
Filename    : /etc/updatedb.conf
```

Nous pouvons aussi utiliser `yum whatprovide /*filename` à la place.

### Lister tous les paquets disponibles

`yum list` liste tous les paquets disponibles dans la base de données de Yum.

```
[root@centos7-1 ~]# yum list
```

### Lister tous les paquets installés en utilisant YUM

```
[root@centos7-1 ~]# yum list installed
```

### Travailler avec le **cache** de yum

Par défaut, yum supprime les fichiers de données téléchargés lorsqu'il ne sont plus nécessaire après une opération réussie. Cela permet de minimiser la quantité de stockage que yum utilise. Cependant, nous popuvons activer du cache, pour que chaque fichier de paquet télécharger par yum reste dans un dossier de cache. En utilisant les données mises en cache, vous pouvez effectuer certaines opérations sans avoir besoin d'une connexion réseau, nous pouvon aussi copier les paquets stockés dans le cache et les réutiliser ailleurs.

Yum stocke les fichiers temporaires dans le dossier `/var/cache/yum/$basearch/$releasever/`,

```
[root@centos7-1 ~]# ls /var/cache/yum/x86_64/7/
base  epel  extras  timedhosts  timedhosts.txt  updates
```

Chaque dépôt configura a un sous-dossier. Pour changer la localisation du cache par défaut, modifier l'option **cachedir** dans la section **\[main]** du fichier de configuration `/etc/yum.conf`.

**Activer le cache**

Pour garder le cache des paquet après une installation réussi, nous pouvons activer le cache en modifiant **keppcache = 1** dans la section \[main] de`/etc/yum.conf`.

Pour télécharger et rendre utilisable toutes les métadonnées sur les dépôts activés de yum, utiliser la commande `yum makecache`. Cela permet de s'assurer que le cache est complètement à jour avec toutes les métadonnées. Pour définir une période après laquelle les métadonnées expirent, utiliser le paramètre **metadata-expire** dans `/etc/yum.conf`.

**Utiliser le mode Cache-only de yum**

Pour exécuter une commande yum sans connexion réseau, ajouter l'option `-C` (pas `-c` )ou `--cacheonly` à la ligne de commande. Avec cette option, yum travaille sans aucune vérificcation des dépôts sur le réseau, et utilise uniquement les fichiers mis en cache. Dans ce mode, yum pourra seulement installé les paquets qui ont été téléchargés et mis en cache par une opération précédente.

```
[root@centos7-1 ~]# yum -C install zip
Loaded plugins: fastestmirror, langpacks
Resolving Dependencies
--> Running transaction check
---> Package zip.x86_64 0:3.0-11.el7 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

=======================================================================================
 Package          Arch                Version                  Repository         Size
=======================================================================================
Installing:
 zip              x86_64              3.0-11.el7               base              260 k

Transaction Summary
=======================================================================================
Install  1 Package

Total download size: 260 k
Installed size: 796 k
Is this ok [y/d/N]:
```

**Vider le cache de yum**

C'est souvent utile de supprimer les entrées accumulés dans le dossier  `/var/cache/yum/`. Si nous supprimons un paquet du cache, cela n'affecte pas la copie du logiciel installée sur notre système. Pour supprimer toutes les entrées des dépôts actuellement activtés depuis le cache, lancer en tant que root la commande  `yum clean all` .

### Vérifier la disponibilité des mises à jour en utilisant Yum

Utiliser `yum check-update` pour trouver combien de paquets installés sur notre système ont des mises à jour disponibles :

```
[root@centos7-1 ~]# yum check-update 
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: bay.uchicago.edu
 * epel: epel.mirror.far.fi
 * extras: mirror.atlanticmetro.net
 * updates: mirror.atlanticmetro.net

ModemManager.x86_64                       1.6.10-1.el7                   base   
ModemManager-glib.x86_64                  1.6.10-1.el7                   base   
NetworkManager.x86_64                     1:1.10.2-16.el7_5              updates
NetworkManager-adsl.x86_64                1:1.10.2-16.el7_5              updates
NetworkManager-bluetooth.x86_64           1:1.10.2-16.el7_5              updates
NetworkManager-glib.x86_64                1:1.10.2-16.el7_5              updates
NetworkManager-libnm.x86_64               1:1.10.2-16.el7_5              updates
NetworkManager-ppp.x86_64                 1:1.10.2-16.el7_5              updates
NetworkManager-team.x86_64                1:1.10.2-16.el7_5              updates
NetworkManager-tui.x86_64                 1:1.10.2-16.el7_5              updates
NetworkManager-wifi.x86_64                1:1.10.2-16.el7_5              updates
...
<output has been truncated>
```

### Mettre à jour le système en utilisant Yum

yum update garde notre système à jour avec toutes les mises à jours de sécurités et binaires des paquets

```
[root@centos7-1 ~]# yum update 
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: bay.uchicago.edu
 * epel: epel.mirror.far.fi
 * extras: mirror.atlanticmetro.net
 * updates: mirror.atlanticmetro.net
Resolving Dependencies
--> Running transaction check
---> Package ModemManager.x86_64 0:1.6.0-2.el7 will be updated
---> Package ModemManager.x86_64 0:1.6.10-1.el7 will be an update
...
--> Finished Dependency Resolution

Dependencies Resolved

=======================================================================================
 Package                           Arch   Version                        Repository
                                                                                  Size
=======================================================================================
Installing:
 grub2                             x86_64 1:2.02-0.65.el7.centos.2       base     29 k
     replacing  grub2.x86_64 1:2.02-0.64.el7.centos
 grub2-tools                       x86_64 1:2.02-0.65.el7.centos.2       base    1.8 M
     replacing  grub2-tools.x86_64 1:2.02-0.64.el7.centos
 grub2-tools-extra                 x86_64 1:2.02-0.65.el7.centos.2       base    993 k
     replacing  grub2-tools.x86_64 1:2.02-0.64.el7.centos
 grub2-tools-minimal               x86_64 1:2.02-0.65.el7.centos.2       base    170 k
     replacing  grub2-tools.x86_64 1:2.02-0.64.el7.centos
 kernel                            x86_64 3.10.0-862.14.4.el7            updates  46 M
Updating:
 ModemManager                      x86_64 1.6.10-1.el7                   base    735 k
 ModemManager-glib                 x86_64 1.6.10-1.el7                   base    231 k
 NetworkManager                    x86_64 1:1.10.2-16.el7_5              updates 1.7 M
 NetworkManager-adsl               x86_64 1:1.10.2-16.el7_5              updates 159 k
 NetworkManager-bluetooth          x86_64 1:1.10.2-16.el7_5              updates 179 k
...
mesa-libwayland-egl               x86_64 17.2.3-8.20171019.el7          base     17 k
 unbound-libs                      x86_64 1.6.6-1.el7                    base    405 k
 volume_key-libs                   x86_64 0.3.9-8.el7                    base    140 k

Transaction Summary
=======================================================================================
Install    5 Packages (+20 Dependent packages)
Upgrade  578 Packages

Total size: 552 M
Is this ok [y/d/N]: y
...
```

### Mettre à jour un paquet en utilisant YUM

`yum update packagename` met à jour un paquet et résout automatiquement tous les problèmes de dépendances et les installe.

```
[root@centos7-1 ~]# yum update firefox -y
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: bay.uchicago.edu
 * epel: epel.mirror.far.fi
 * extras: mirror.atlanticmetro.net
 * updates: mirror.atlanticmetro.net
Resolving Dependencies
--> Running transaction check
---> Package firefox.x86_64 0:52.2.0-2.el7.centos will be updated
---> Package firefox.x86_64 0:60.3.0-1.el7.centos will be an update
--> Processing Dependency: nss >= 3.36.0 for package: firefox-60.3.0-1.el7.centos.x86_64
--> Processing Dependency: nspr >= 4.19.0 for package: firefox-60.3.0-1.el7.centos.x86_64
--> Processing Dependency: libdbus-1.so.3(LIBDBUS_1_3)(64bit) for package: firefox-60.3.0-1.el7.centos.x86_64
...
Dependencies Resolved
=======================================================================================
 Package                  Arch         Version                     Repository     Size
=======================================================================================
Updating:
 firefox                  x86_64       60.3.0-1.el7.centos         updates        91 M
Updating for dependencies:
 dbus                     x86_64       1:1.10.24-7.el7             base          245 k
 dbus-libs                x86_64       1:1.10.24-7.el7             base          169 k
 dbus-x11                 x86_64       1:1.10.24-7.el7             base           47 k
...
Transaction Summary
=======================================================================================
Upgrade  1 Package (+10 Dependent packages)

Total size: 93 M
Downloading packages:
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Updating   : nspr-4.19.0-1.el7_5.x86_64                                         1/22 
  Updating   : nss-util-3.36.0-1.el7_5.x86_64                                     2/22 
...
  Cleanup    : 1:dbus-libs-1.6.12-17.el7.x86_64                                  20/22 
  Cleanup    : nspr-4.13.1-1.0.el7_3.x86_64                                      21/22 
  Cleanup    : nss-softokn-freebl-3.28.3-6.el7.x86_64                            22/22 
  Verifying  : 1:dbus-x11-1.10.24-7.el7.x86_64                                    1/22 
  Verifying  : 1:dbus-libs-1.10.24-7.el7.x86_64                                   2/22 
  Verifying  : firefox-60.3.0-1.el7.centos.x86_64                                 3/22 
...
  Verifying  : firefox-52.2.0-2.el7.centos.x86_64                                21/22 
  Verifying  : nss-softokn-freebl-3.28.3-6.el7.x86_64                            22/22 

Updated:
  firefox.x86_64 0:60.3.0-1.el7.centos                                                 

Dependency Updated:
  dbus.x86_64 1:1.10.24-7.el7                   dbus-libs.x86_64 1:1.10.24-7.el7      
  dbus-x11.x86_64 1:1.10.24-7.el7               nspr.x86_64 0:4.19.0-1.el7_5          
  nss.x86_64 0:3.36.0-7.el7_5                   nss-softokn.x86_64 0:3.36.0-5.el7_5   
  nss-softokn-freebl.x86_64 0:3.36.0-5.el7_5    nss-sysinit.x86_64 0:3.36.0-7.el7_5   
  nss-tools.x86_64 0:3.36.0-7.el7_5             nss-util.x86_64 0:3.36.0-1.el7_5      

Complete!
```

Si vous avez un paquet rpm vous pouvez utiliser `yum localupdate abc.rpm` .

> ### YUM update vs YUM upgrade
>
> `yum upgrade` et `yum update` effectue la même fonction qui met à jour vers la dernière version du paquet.
>
> `yum upgrade` **forces** la suppression du paquet obsolète, alors que `yum update`peut ou pas faire ça. La suppression des paquets obsolètes peut être risquées, car cela peut entrainer la supppression de paquet que vous utilisez.\
> Cela fait de `yum update` une option plus sûre.
>
> note: Le comportement peut être différent en fonction de votre distribution et version.

### Travailler avec les groupes de paquets (Group Packages)

Dans Linux, de nombreux paquets sont rassemblé dans un groupe particulier, appelé **Group Packages**. Au lieu de travailler avec des paquets individuelles avec yum, vous pouvez travailler avec un groupe particulier qui installera / supprimera / mettra à jour tous les paquets qui appartiennetn au groupe.

```
# yum grouplist :
# yum groupinstall 'MySQL Database'
# yum groupupdate 'DNS Name Server'
# yum groupremove 'DNS Name Server'
```

## YUM Shell

L'utilitaire Yum fournit un shell personnalisé où vous pouvez exécuter de nombreuses commandes.

```
[root@centos7-1 ~]# yum shell 
Loaded plugins: fastestmirror, langpacks
> list
list             list-sec         list-security    list-updateinfo  
> group
group         groupinfo     grouplist     groups        
grouperase    groupinstall  groupremove   groupupdate   
> quit
Leaving Shell
```

## yumdownloader

Comment utiliser yum pour télécharger un paquet sans l'installer ? Il y a de nombreuses manières pour télécharger un paquet sans l'installer. Les deux méthodes les plus communes sont décrites ici.

1. En utilisant le plugin de yum “downloadonly” 
2. En utilisant l'utilitaire “yumdownloader” .

**Méthode 1** : En utilisant le plugin de yum “downloadonly”:

En premier nous devons installer le paquet qui inclut le plugin “downloadonly” :

```
(RHEL5)
# yum install yum-downloadonly

(RHEL6)
# yum install yum-plugin-downloadonly
```

syntaxe:

```
yum install --downloadonly --downloaddir=[directory] [package]
```

Si vous n'utiliser pas l'option, il sera enregistré dans `/var/cache/yum/ in rhel-{arch}-channel/packages directory`.

exemple:

```
 yum install --downloadonly --downloaddir=/tmp firefox
```

**Méthode 2 **: En utilisant l'utilitaire “yumdownloader”

En premier nous installons le paquet yum-utils:

```
yum install yum-utils
```

syntaxe:

```
yumdownloader --destdir [directory] [package]
```

exemple:

```
yumdownloader --destdir /tmp firefox
```

Essayer `yumdownloader --help` pour voir les options utiles comme :

```
--urls     Instead of downloading RPMs, list the URLs that would be downloaded.
--resolve  When downloading RPMs, resolve dependencies and also download the required packages.
--source   Instead of downloading the binary RPMs, download the source RPMs
...
```

L'option`--resolve` est utile pour télécharger un paquet et ses dépendances et l'utiliser dans d'autres systèmes.

### Pour voir l'historique des commandes Yum (update, install, remove)

Vous pouvez utiliser la commande `yum history`  :

```
[root@centos7-1 ~]# yum history 
Loaded plugins: fastestmirror, langpacks
ID     | Login user               | Date and time    | Action(s)      | Altered
-------------------------------------------------------------------------------
    11 | payam  <payam>          | 2018-12-05 10:02 | Update         |   11   
    10 | payam  <payam>          | 2018-12-04 14:54 | Erase          |    1   
     9 | payam  <payam>          | 2018-12-04 14:45 | Install        |    1   
     8 | payam  <payam>          | 2018-12-04 14:44 | Erase          |    1  <
     7 | payam  <payam>          | 2018-12-03 06:03 | Erase          |    3 > 
     6 | payam  <payam>          | 2018-11-19 05:14 | I, U           |    3   
     5 | payam  <payam>          | 2018-11-18 03:46 | Install        |    1   
     4 | payam  <payam>          | 2018-10-20 13:01 | Install        |    1   
     3 | payam  <payam>          | 2018-10-13 03:38 | Install        |   12   
     2 | payam  <payam>          | 2018-08-26 04:48 | I, U           |    3   
     1 | System <unset>           | 2017-10-28 11:18 | Install        | 1318   
history list
```

Ou voir le fichier journal de yum dans le dossier `/var/log/yum` :

```
[root@centos7-1 ~]# tail -5 /var/log/yum.log 
Dec 05 10:02:27 Updated: nss-sysinit-3.36.0-7.el7_5.x86_64
Dec 05 10:02:27 Updated: nss-3.36.0-7.el7_5.x86_64
Dec 05 10:02:37 Updated: firefox-60.3.0-1.el7.centos.x86_64
Dec 05 10:02:37 Updated: nss-tools-3.36.0-7.el7_5.x86_64
Dec 05 10:02:37 Updated: 1:dbus-x11-1.10.24-7.el7.x86_64
```

### Résumé

Système de la famille Fedora/Red Hat :

* **Red Hat Package Manager (rpm)**:
  * gestionnaire de paquet bas-niveau ou sous-jacent
* **Yellowdog Updater (yum)**:
  * Le gestionnaire de paquet haut-niveau qui diffère selon les distributions mais yum est souvent utilisé
* **Dandified Yum (dnf)**:
  * La prochaine génération de version de yum

.

.

.

sources:

[http://www.pepedocs.com/notes?tid=linux\&nid=lfs101x#ch6](http://www.pepedocs.com/notes?tid=linux\&nid=lfs101x#ch6)

[https://linux.die.net/man/8/rpm](https://linux.die.net/man/8/rpm)

[https://www.tecmint.com/20-practical-examples-of-rpm-commands-in-linux/](https://www.tecmint.com/20-practical-examples-of-rpm-commands-in-linux/)

[http://ftp.rpm.org/max-rpm/s1-rpm-verify-output.html](http://ftp.rpm.org/max-rpm/s1-rpm-verify-output.html)(rpm -V)

[https://serverfault.com/questions/747089/whats-the-diff-between-rpm-u-and-rpm-f?rq=1](https://serverfault.com/questions/747089/whats-the-diff-between-rpm-u-and-rpm-f?rq=1)

[http://ftp.rpm.org/max-rpm/s1-rpm-erase-and-config-files.html](http://ftp.rpm.org/max-rpm/s1-rpm-erase-and-config-files.html)

[https://www.linuxquestions.org/questions/linux-software-2/can-rpm-remove-config-files-during-uninstall-537423/](https://www.linuxquestions.org/questions/linux-software-2/can-rpm-remove-config-files-during-uninstall-537423/)

[http://ftp.rpm.org/max-rpm/s1-rpm-miscellania-rpm2cpio.html](http://ftp.rpm.org/max-rpm/s1-rpm-miscellania-rpm2cpio.html)

[https://blog.packagecloud.io/eng/2015/10/13/inspect-extract-contents-rpm-packages/](https://blog.packagecloud.io/eng/2015/10/13/inspect-extract-contents-rpm-packages/)(what is inside rmp)

[https://www.tecmint.com/20-linux-yum-yellowdog-updater-modified-commands-for-package-mangement/](https://www.tecmint.com/20-linux-yum-yellowdog-updater-modified-commands-for-package-mangement/)

[https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/sec-configuring_yum_and_yum_repositories](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/sec-configuring_yum_and_yum_repositories)(yum.conf)

[https://www.quora.com/What-is-gpgcheck-in-a-yum-configuration-file-in-Rhel](https://www.quora.com/What-is-gpgcheck-in-a-yum-configuration-file-in-Rhel)

[https://www.digitalocean.com/community/tutorials/how-to-set-up-and-use-yum-repositories-on-a-centos-6-vps](https://www.digitalocean.com/community/tutorials/how-to-set-up-and-use-yum-repositories-on-a-centos-6-vps)

[https://superuser.com/questions/1252958/explanation-of-the-contents-inside-folder-etc-yum-repos-d](https://superuser.com/questions/1252958/explanation-of-the-contents-inside-folder-etc-yum-repos-d)

[https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/sec-working_with_yum_cache](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/sec-working_with_yum_cache)( working with yum cache)

[https://www.hivelocity.net/kb/how-to-clear-the-yum-cache/](https://www.hivelocity.net/kb/how-to-clear-the-yum-cache/) (clear cache)

[https://access.redhat.com/solutions/10154](https://access.redhat.com/solutions/10154)

[https://www.thegeekdiary.com/how-to-use-yum-downloadonly-to-download-a-package-without-installing-it/](https://www.thegeekdiary.com/how-to-use-yum-downloadonly-to-download-a-package-without-installing-it/)

.
