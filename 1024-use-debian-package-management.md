# 102.4. Utiliser le gestionnaire de paquet Debian

## **102.4 Utiliser le gestionnaire de paquet Debian**

**Poids:** 3

**Description:** Les candidats doivent être capable de gérer les paquets en utilisant l'outil de paquets de Debian.

**Connaissances clés:**

* Installer, mettre-à-jour et désinstaller un packet binaire Debian
* Trouver un paquet qui contient un fichier spécifique ou une bibliothèque qui doit ou ne doit pas être installée
* Obtenir des informations sur un paquet comme la version, son contenu, ses dépendances, l'intégrité du paquet et son statut d'installation (est ce que le paquet est installé ou non)

**Concepts et Utilitaires:**

* /etc/apt/sources.list
* dpkg
* dpkg-reconfigure
* apt-get
* apt-cache
* aptitude

### Qu'est ce qu'un paquet ?

Un paquet est une pièce de logiciel qui founirt une pièce du système. Exemple de paquets :

* Linux Kernel
* The C compiler
* The Firefox web browser
* The USB library to interact with USB devices

Un paquet contient les fichier et les autres insctruction nécessaire pour faire fonctionner le composant logiciel sur le système.

Les paquets ont des dépendances à d'autres paquets qui doivent aussi être téléchargées et installées avant d'installé le paquet avec les dépendances.

Il y a longtemps les administrateurs de Linux devait gérer les dépenances et rendait compliqué l'installation d'un paquet c'est ce qui a causé la naissance des gestionnaires de paquets.

### Qu'est ce qu'un gestionnaire de paquet(Package manager) ?

Le gestionnaire de paquet est l'utilitaire qui gère le téléchargement, l'extraction et le positionnement des pièce du paquet aux bons endroits.

![](.gitbook/assets/debpack-packagemgmnt.png)

Le flux de travail général commence avec l'utilisateur qui demande un paquet en utilisant le gestionnaire de paquet disponible sur le système. Le gestionnaire de paquet cherche le paquet depuis les endroits connus (appelées dépôts logiciels) et le télécharge. Le gestionnaire de paquets intalle alors le paquet et avertit des étapes manuelles supplémentaire qu'il a trouvé si nécessaire.

Les outils de gestion de paquet aide l'administrateur système/serveur de différentes façons comme :

* Le téléchargement et l'installation du logiciel
* La compilation du logiciel depuis les sources
* Garde une trace de tous les logiciels installées, de leurs mise à jours et mise à niveau
* Gestion des dépendances
* et aussi garder d'autres informations sur les logiciels installées et bien d'autre

#### Qu'est ce qu'un dépôt logiciel (Software Repository)?

Un dépôt Linux **est un espace de stockage depuis lequel notre système retrouve et installe les mise à jour de l'OS et des applications.** Chaque dépôt est une collection de logiciel hébergées sur un serveur distant et a pour but d'être utilisé pour l'installation et la mise à jour des paquets logiciel sur un système Linux.

![](.gitbook/assets/debpack-swrepo.jpg)

Le gestionnaire de paquet est constitué de deux entités :

* outil bas-niveau (low-level tool)
* outil haut-niveau (high-level tool)

![](.gitbook/assets/debpack-all.jpg)

Un outil **bas-niveau** (comme **dpkg** ou **rpm**), s'occupe des détail de l'extraction des paquets individuels, du lancement des script, d'installer correctement les logiciels, alors qu'un outil **haut-niveau** tool (comme **apt-get**, **yum**, ou **zypper**) travaille avec des groupes de paquets, télécharges les paquets depuis le fournisseurs et s'occupe des dépendances

La partie central des distribution Linux et la plupar des logiciels supplémentaires sont installé via un système de gestion de paquets.

Dans ce cours, nous parlerons de la gestion des paquets sur des distributions basées sur Debian.

![](.gitbook/assets/debpack-deb.jpg)

### dpkg

dpkg iest un gestionnaire de paquet pour les système Debian-based. Il peut installer, supprimer, et construire les paquets, mais contrairement à d'autre système de gestion des paquets, **il ne peut pas télécharger et installer les paquets ou leur dépendances.**

dpkg a une base de données qui se trouve dans le dossier /var/lib/dpkg  ;

```
root@ubuntu16-1:~# ls -l /var/lib/dpkg/
total 4416
drwxr-xr-x 2 root root    4096 Dec  2  2018 alternatives
-rw-r--r-- 1 root root      11 Nov 26  2017 arch
-rw-r--r-- 1 root root  170080 Aug  1  2017 available
-rw-r--r-- 1 root root       8 Aug  1  2017 cmethopt
-rw-r--r-- 1 root root    1214 Jul  1  2018 diversions
-rw-r--r-- 1 root root    1133 Jul  1  2018 diversions-old
drwxr-xr-x 2 root root  405504 Dec  2  2018 info
-rw-r----- 1 root root       0 Dec  2  2018 lock
-rw-r----- 1 root root       0 Dec  2  2018 lock-frontend
drwxr-xr-x 7 root root    4096 Dec  2  2018 methods
drwxr-xr-x 2 root root    4096 Jan 12  2016 parts
-rw-r--r-- 1 root root     228 Aug  1  2017 statoverride
-rw-r--r-- 1 root root 1948486 Dec  2  2018 status
-rw-r--r-- 1 root root 1948486 Dec  2  2018 status-old
drwxr-xr-x 2 root root    4096 Dec  1  2018 triggers
drwxr-xr-x 2 root root    4096 Dec  2  2018 updates
```

Le fichier "status" contient la liste des logiciels installé sur le système courant.

**Voyons les opions les plus utiles :**

\-l | --list liste tous les paquets installés sur le système ( --get-selection fait la même chose):

```
root@ubuntu16-1:~# dpkg -l
Desired=Unknown/Install/Remove/Purge/Hold
| Status=Not/Inst/Conf-files/Unpacked/halF-conf/Half-inst/trig-aWait/Trig-pend
|/ Err?=(none)/Reinst-required (Status,Err: uppercase=bad)
||/ Name           Version      Architecture Description
+++-==============-============-============-=================================
ii  a11y-profile-m 0.1.10-0ubun amd64        Accessibility Profile Manager - U
ii  account-plugin 0.12+16.04.2 all          GNOME Control Center account plug
ii  account-plugin 0.12+16.04.2 all          GNOME Control Center account plug
ii  account-plugin 0.12+16.04.2 all          GNOME Control Center account plug
...
<output has been truncated>
```

Pour voir si un paquet spécifique est installé ou non utilisre l'option “-l” avec le nom du paquet.

dpkg -i | --install ,install a local .deb file :

{% hint style="success" %}
.deb est une extension pour les paquet logiciel formaté pour les distributions Linux Debian.
{% endhint %}

```
root@ubuntu16-1:~# dpkg -i zip_3.0-11_amd64.deb 
(Reading database ... 192789 files and directories currently installed.)
Preparing to unpack zip_3.0-11_amd64.deb ...
Unpacking zip (3.0-11) over (3.0-11) ...
Setting up zip (3.0-11) ...
Processing triggers for man-db (2.7.5-1) ...
```

Et si votre paquet nécessite des dépendances vous aurez des problèmes :

```
root@ubuntu16-1:~# dpkg -i jcal_0.4.1-2_amd64.deb 
dpkg: warning: downgrading jcal from 0.4.1-2build1 to 0.4.1-2
(Reading database ... 192782 files and directories currently installed.)
Preparing to unpack jcal_0.4.1-2_amd64.deb ...
Unpacking jcal (0.4.1-2) over (0.4.1-2build1) ...
dpkg: dependency problems prevent configuration of jcal:
 jcal depends on libjalali0; however:
  Package libjalali0 is not installed.

dpkg: error processing package jcal (--install):
 dependency problems - leaving unconfigured
Processing triggers for man-db (2.7.5-1) ...
Errors were encountered while processing:
 jcal
```

dpkg ne gère pas les dépendances et si nous essayons d'installer un paquet avec des dépendances non présente une des deux choses suivantes se passera :

* soit cela échouera complètement !
* soit cela installera le paquet mais le laissera non configuré tant que toutes les dépendances ne sont pas installées et il faudra utiliser l'outil apt pour finir la configurationo(`apt-get install -f` nous le verrons plus tard).

Nous pouvons aussi utiliser `dpkg --force-depends` pour ne pas tenir compte des dépendances ou `--force-conflicts` pour qu'il ferme les yeux sur des conflits possibles ou  `--force-reinstall`pour réinstaller, mais n'oubliez pas que chaque commande `--force` peut entrainer des problèmes et mettre le système dans un état **inconsistant**.

dpkg -L | --listfiles ,Pour lister les fichiers installés par un paquet :

```
root@ubuntu16-1:~# dpkg -L zip 
/.
/usr
/usr/share
/usr/share/doc
/usr/share/doc/zip
/usr/share/doc/zip/WHATSNEW
/usr/share/doc/zip/TODO
/usr/share/doc/zip/copyright
/usr/share/doc/zip/changelog.Debian.gz
/usr/share/man
/usr/share/man/man1
/usr/share/man/man1/zipsplit.1.gz
/usr/share/man/man1/zipcloak.1.gz
/usr/share/man/man1/zip.1.gz
/usr/share/man/man1/zipnote.1.gz
/usr/bin
/usr/bin/zipnote
/usr/bin/zipcloak
/usr/bin/zip
/usr/bin/zipsplit
/usr/share/doc/zip/changelog.gz
```

dpkg -S | --serach , Si nous ne somme pas sur du paquet qui installe un fichier, dpkg -S peut être capable de nous le dire :

```
root@ubuntu16-1:~# dpkg -S /usr/bin/zipcloak 
zip: /usr/bin/zipcloak
```

note: Nous pouvons aussi utiliser `dpkg -S string` et il utilisera les expressions régulières pour rechercher dans le système de fichier si une correspondance existe avec la chaine que nous avons tapé.

dpkg -s | --status , Vérifie si un paquet est installé ou non:

```
root@ubuntu16-1:~# dpkg -s zip
Package: zip
Status: deinstall ok installed
Priority: optional
Section: utils
Installed-Size: 573
Maintainer: Ubuntu Developers <ubuntu-devel-discuss@lists.ubuntu.com>
Architecture: amd64
Multi-Arch: foreign
Version: 3.0-11
Depends: libbz2-1.0, libc6 (>= 2.14)
Recommends: unzip
Description: Archiver for .zip files
 This is InfoZIP's zip program. It produces files that are fully
 compatible with the popular PKZIP program; however, the command line
 options are not identical. In other words, the end result is the same,
 but the methods differ. :-)
 .
 This version supports encryption.
Original-Maintainer: Santiago Vila <sanvila@debian.org>
Homepage: http://www.info-zip.org/Zip.html
```

dpkg -c | --contents , affichera le conenu du paquet “.deb” dans un format de lise longue :

```
root@ubuntu16-1:~# dpkg -c zip_3.0-11_amd64.deb 
drwxr-xr-x root/root         0 2015-08-16 22:10 ./
drwxr-xr-x root/root         0 2015-08-16 22:10 ./usr/
drwxr-xr-x root/root         0 2015-08-16 22:10 ./usr/share/
drwxr-xr-x root/root         0 2015-08-16 22:10 ./usr/share/doc/
drwxr-xr-x root/root         0 2015-08-16 22:11 ./usr/share/doc/zip/
-rw-r--r-- root/root     15731 2008-07-01 20:27 ./usr/share/doc/zip/WHATSNEW
-rw-r--r-- root/root      6675 2008-06-12 19:03 ./usr/share/doc/zip/TODO
-rw-r--r-- root/root      3815 2015-08-16 14:25 ./usr/share/doc/zip/copyright
-rw-r--r-- root/root      1040 2015-08-16 22:11 ./usr/share/doc/zip/changelog.Debian.gz
drwxr-xr-x root/root         0 2015-08-16 22:10 ./usr/share/man/
drwxr-xr-x root/root         0 2015-08-16 22:10 ./usr/share/man/man1/
-rw-r--r-- root/root       596 2015-08-16 22:10 ./usr/share/man/man1/zipsplit.1.gz
-rw-r--r-- root/root       951 2015-08-16 22:10 ./usr/share/man/man1/zipcloak.1.gz
-rw-r--r-- root/root     28488 2015-08-16 22:10 ./usr/share/man/man1/zip.1.gz
-rw-r--r-- root/root       793 2015-08-16 22:10 ./usr/share/man/man1/zipnote.1.gz
drwxr-xr-x root/root         0 2015-08-16 22:10 ./usr/bin/
-rwxr-xr-x root/root     81840 2015-08-16 22:10 ./usr/bin/zipnote
-rwxr-xr-x root/root     86224 2015-08-16 22:10 ./usr/bin/zipcloak
-rwxr-xr-x root/root    192520 2015-08-16 22:10 ./usr/bin/zip
-rwxr-xr-x root/root     81840 2015-08-16 22:10 ./usr/bin/zipsplit
lrwxrwxrwx root/root         0 2015-08-16 22:10 ./usr/share/doc/zip/changelog.gz -> CHANGES.gz
```

Ne pas confondre `-c` avec `-C`, -C vérifie les paquets installés partiellement.

> **Qu'est ce que debconf?**
>
> Lorsque les paquets sont installé, debconf pose des questions à l'utilisateur pour déterminer le contenu des fichiers de configuration du système associé avec ce paquet et stocker les préférences de l'utilisateur/administrateur dans une base de données.
>
> Plus tard pendant l'installation des paquets, leurs scripts utilise les préférences de configuration de la base de donnée pour générer les fichiers de configuration et d'autres tâches administrative. Cela évite de devoir modifier à la main des fichiers de configurations, et aussi d'attendre que chaque paquet soit installé individuellement avant de répondre à certaines questions de configuration.

### dpkg-reconfigure

Après l'installation d'un paquet, il est possible de revenir en arrière et de modifier la configuration d'un paquet en utilisant le programme dpkg-reconfigure (ou un autre programme comme Synaptic). Par exemple essayez :

```
root@ubuntu16-1:~# dpkg-reconfigure tzdata
```

dpkg -r | --remove , supprimer un paquet en utilisant son nom :

```
root@ubuntu16-1:~# dpkg -r telnet
(Reading database ... 229441 files and directories currently installed.)
Removing telnet (0.17-40) ...
Processing triggers for man-db (2.7.5-1) ...
```

dpkg prend soin des dépendances et ne les supprime pas par défaut, (c'est pour cela qu'il n'attends pas un fichier .dev, mais le nom du paquet afin d'explorer les dépendances), cependant nous pouvons le forcer avec la commande `dpkg -r --force-depends` mais cela n'est PAS recommandé. Il est conseillé d'utiliser un gestionnaire de paquet qui gère les dépendance pour s'assurer que le système est dans un état consistant.

note: dpkg -P | --purge élimine un paquet! Nous pouvons aussi utiliser l'option ‘P‘ à la place de ‘r’ ce qui supprimera le paquet et ses fichiers de configuration. L'option ‘r‘ supprimera seulement le paquet sans ses fichiers de configuration.

```
root@ubuntu16-1:~# dpkg -P vsftpd
(Reading database ... 229487 files and directories currently installed.)
Removing vsftpd (3.0.3-3ubuntu2) ...
Purging configuration files for vsftpd (3.0.3-3ubuntu2) ...
Processing triggers for man-db (2.7.5-1) ...
```

Ne pas confondre -P avec -p , -p affichera des information à propos du paquet.

dpkg a de nombreuses options, essayer  dpkg --help pour plus d'information :

```
root@ubuntu16-1:~# dpkg --help
Usage: dpkg [<option> ...] <command>

Commands:
  -i|--install       <.deb file name> ... | -R|--recursive <directory> ...
  --unpack           <.deb file name> ... | -R|--recursive <directory> ...
  -A|--record-avail  <.deb file name> ... | -R|--recursive <directory> ...
  --configure        <package> ... | -a|--pending
  --triggers-only    <package> ... | -a|--pending
  -r|--remove        <package> ... | -a|--pending
  -P|--purge         <package> ... | -a|--pending
  -V|--verify <package> ...        Verify the integrity of package(s).
  --get-selections [<pattern> ...] Get list of selections to stdout.
  --set-selections                 Set package selections from stdin.
  --clear-selections               Deselect every non-essential package.
  --update-avail [<Packages-file>] Replace available packages info.
  --merge-avail [<Packages-file>]  Merge with info from file.
  --clear-avail                    Erase existing available info.
  --forget-old-unavail             Forget uninstalled unavailable pkgs.
  -s|--status <package> ...        Display package status details.
  -p|--print-avail <package> ...   Display available version details.
  -L|--listfiles <package> ...     List files 'owned' by package(s).
  -l|--list [<pattern> ...]        List packages concisely.
  -S|--search <pattern> ...        Find package(s) owning file(s).
  -C|--audit [<package> ...]       Check for broken package(s).
  --yet-to-unpack                  Print packages selected for installation.
  --predep-package                 Print pre-dependencies to unpack.
  --add-architecture <arch>        Add <arch> to the list of architectures.
  --remove-architecture <arch>     Remove <arch> from the list of architectures.
  --print-architecture             Print dpkg architecture.
  --print-foreign-architectures    Print allowed foreign architectures.
  --assert-<feature>               Assert support for the specified feature.
  --compare-versions <a> <op> <b>  Compare version numbers - see below.
  --force-help                     Show help on forcing.
  -Dh|--debug=help                 Show help on debugging.

  -?, --help                       Show this help message.
      --version                    Show the version.

Assertable features: support-predepends, working-epoch, long-filenames,
  multi-conrep, multi-arch, versioned-provides.

Use dpkg with -b, --build, -c, --contents, -e, --control, -I, --info,
  -f, --field, -x, --extract, -X, --vextract, --ctrl-tarfile, --fsys-tarfile
on archives (type dpkg-deb --help).

Options:
  --admindir=<directory>     Use <directory> instead of /var/lib/dpkg.
  --root=<directory>         Install on a different root directory.
  --instdir=<directory>      Change installation dir without changing admin dir.
  --path-exclude=<pattern>   Do not install paths which match a shell pattern.
  --path-include=<pattern>   Re-include a pattern after a previous exclusion.
  -O|--selected-only         Skip packages not selected for install/upgrade.
  -E|--skip-same-version     Skip packages whose same version is installed.
  -G|--refuse-downgrade      Skip packages with earlier version than installed.
  -B|--auto-deconfigure      Install even if it would break some other package.
  --[no-]triggers            Skip or force consequential trigger processing.
  --verify-format=<format>   Verify output format (supported: 'rpm').
  --no-debsig                Do not try to verify package signatures.
  --no-act|--dry-run|--simulate
                             Just say what we would do - don't do it.
  -D|--debug=<octal>         Enable debugging (see -Dhelp or --debug=help).
  --status-fd <n>            Send status change updates to file descriptor <n>.
  --status-logger=<command>  Send status change updates to <command>'s stdin.
  --log=<filename>           Log status changes and actions to <filename>.
  --ignore-depends=<package>,...
                             Ignore dependencies involving <package>.
  --force-...                Override problems (see --force-help).
  --no-force-...|--refuse-...
                             Stop when problems encountered.
  --abort-after <n>          Abort after encountering <n> errors.

Comparison operators for --compare-versions are:
  lt le eq ne ge gt       (treat empty version as earlier than any version);
  lt-nl le-nl ge-nl gt-nl (treat empty version as later than any version);
  < << <= = >= >> >       (only for compatibility with control file syntax).

Use 'apt' or 'aptitude' for user-friendly package management.
```

Lorsque nous travaillons avec dpkg nous devons avoir des paquets .deb dans les main pour que cela fonctionne. Mais qu'en est t'il du gestionnaire de paquet ? Comment un gestionnaire de paquet comme apt trouve les depôt logiciels sur les systèmes basés sur Debian ?

### /etc/apt/sources.list

Ce fichier contient les information sur quel depôt \[online / remote ] le système utilisera. Ce fichier contient des lignes dans le format suivant :

```
deb location-of-resources distribution component(s)
```

```
root@ubuntu16-1:~# cat /etc/apt/sources.list
# deb cdrom:[Ubuntu 16.04.3 LTS _Xenial Xerus_ - Release amd64 (20170801)]/ xenial main restricted

# See http://help.ubuntu.com/community/UpgradeNotes for how to upgrade to
# newer versions of the distribution.
deb http://us.archive.ubuntu.com/ubuntu/ xenial main restricted
# deb-src http://us.archive.ubuntu.com/ubuntu/ xenial main restricted

## Major bug fix updates produced after the final release of the
## distribution.
deb http://us.archive.ubuntu.com/ubuntu/ xenial-updates main restricted
# deb-src http://us.archive.ubuntu.com/ubuntu/ xenial-updates main restricted

## N.B. software from this repository is ENTIRELY UNSUPPORTED by the Ubuntu
## team. Also, please note that software in universe WILL NOT receive any
## review or updates from the Ubuntu security team.
deb http://us.archive.ubuntu.com/ubuntu/ xenial universe
# deb-src http://us.archive.ubuntu.com/ubuntu/ xenial universe
deb http://us.archive.ubuntu.com/ubuntu/ xenial-updates universe
# deb-src http://us.archive.ubuntu.com/ubuntu/ xenial-updates universe

## N.B. software from this repository is ENTIRELY UNSUPPORTED by the Ubuntu 
## team, and may not be under a free licence. Please satisfy yourself as to 
## your rights to use the software. Also, please note that software in 
## multiverse WILL NOT receive any review or updates from the Ubuntu
## security team.
deb http://us.archive.ubuntu.com/ubuntu/ xenial multiverse
# deb-src http://us.archive.ubuntu.com/ubuntu/ xenial multiverse
deb http://us.archive.ubuntu.com/ubuntu/ xenial-updates multiverse
# deb-src http://us.archive.ubuntu.com/ubuntu/ xenial-updates multiverse

## N.B. software from this repository may not have been tested as
## extensively as that contained in the main release, although it includes
## newer versions of some applications which may provide useful features.
## Also, please note that software in backports WILL NOT receive any review
## or updates from the Ubuntu security team.
deb http://us.archive.ubuntu.com/ubuntu/ xenial-backports main restricted universe multiverse
# deb-src http://us.archive.ubuntu.com/ubuntu/ xenial-backports main restricted universe multiverse

## Uncomment the following two lines to add software from Canonical's
## 'partner' repository.
## This software is not part of Ubuntu, but is offered by Canonical and the
## respective vendors as a service to Ubuntu users.
# deb http://archive.canonical.com/ubuntu xenial partner
# deb-src http://archive.canonical.com/ubuntu xenial partner

deb http://security.ubuntu.com/ubuntu xenial-security main restricted
# deb-src http://security.ubuntu.com/ubuntu xenial-security main restricted
deb http://security.ubuntu.com/ubuntu xenial-security universe
# deb-src http://security.ubuntu.com/ubuntu xenial-security universe
deb http://security.ubuntu.com/ubuntu xenial-security multiverse
# deb-src http://security.ubuntu.com/ubuntu xenial-security multiverse
```

`deb` lignes relatives aux paquets binaires, ceux que nous pouvons installer avec apt.\
`deb-src` lignes relatives au paquets sources (comme ceux téléchargé par  apt-get source $package) et après compilés. Les paquets sources sont nécessaire seulement si nous voulons compiler nous même certains paquets, ou inspecter le code source pour un bug. Les utilisateurs ordinaires n'ont pas besoin d'inclure de tel dépôt.

Les composant des dépôts sont :

* Main - Logiciels supportés officiellement.
* Restricted - Logiciels supportés qui ne sont pas disponible sous une licence complètement libre.
* Universe - Logiciels maintenus par la communauté, c'est-à-dire pas les logiciels qui ne sont pas officiellement supportés.
* Multiverse - Logiciels non libre.

Certaines distributions comme Ubuntu ont aussi un dossier `/etc/apt/sources.list.d` qui fournit une manière d'ajouter des entrées sources.list dans des fichiers séparés `.list`.

```
root@ubuntu16-1:/# ls /etc/apt/sources.list.d
peek-developers-ubuntu-stable-xenial.list
root@ubuntu16-1:/# cat /etc/apt/sources.list.d/peek-developers-ubuntu-stable-xenial.list 
deb http://ppa.launchpad.net/peek-developers/stable/ubuntu xenial main
# deb-src http://ppa.launchpad.net/peek-developers/stable/ubuntu xenial main
```

Tous les fichiers `.list` sont compilés et ajouté au fichier `/etc/apt/sources.list` .

## APT (Advanced Packaging Tool)

C'est un système de gestion de paquet très populaire, libre, puissant et utile qui est un front end pour le système de gestion de paquet dpkg. Initialement il a été conçu pour les paquets Debian `.deb.

Apt est un outil disponible uniquement en ligne de commande sans interface graphique. Lorsque vous utiliser la ligne de commande en spécifiant le nom du paquet à installer, il recherche ce paquet dans la liste configuré des sources spécifiées dans ‘/etc/apt/sources.list’ ainsi que les dépendances du paquet et les retrouve et les installe automatiquement avec le paquet courant de sorte à ce que l'utilisateur n'est pas à s'occuper de l'installation des dépendances.

Il est constitué de deux commandes:

* **apt-get**
* **apt-cache**

### **Qu'est ce que apt-get?**

L'outil en ligne de commande apt-get sert à travailler avec les paquets logiciels APT, qui sont utilisés pour travaillé avec la bilbiothèque APT sur Ubuntu afin de faire l'installation des nouveaux logiciels, de supprimer des logiciels existants, de mettre à jour des paquets existants et même de mettre à jour le système d'exploitation. **  **\
****

**apt-get update **

Cela nous aide à télécharger une **liste de paquets** depuis les différents dépôt inclus sur notre système et à les mettre à jour lorsque de nouvelles version des paquets et de leur dépendances sont disponibles. (Après toute modification sur les dépôts nous avons besoin de lancer apt-update manuellement, autrement, le système de cache locale des dépôt précédents est utilisé)

```
root@ubuntu16-1:/# apt-get update 
Hit:1 http://us.archive.ubuntu.com/ubuntu xenial InRelease                     
Hit:2 http://security.ubuntu.com/ubuntu xenial-security InRelease              
Hit:3 http://ppa.launchpad.net/peek-developers/stable/ubuntu xenial InRelease  
Hit:4 http://us.archive.ubuntu.com/ubuntu xenial-updates InRelease             
Hit:5 http://us.archive.ubuntu.com/ubuntu xenial-backports InRelease
Reading package lists... Done
Building dependency tree       
Reading state information... Done
287 packages can be upgraded. Run 'apt list --upgradable' to see them.
```

Pour voir les paquets qui peuvent être mis à jour:

```
root@ubuntu16-1:/# apt-get list --upgradable 
Listing... Done
appstream/xenial-updates 0.9.4-1ubuntu4 amd64 [upgradable from: 0.9.4-1ubuntu3]
apt/xenial-updates 1.2.29 amd64 [upgradable from: 1.2.24]
apt-transport-https/xenial-updates 1.2.29 amd64 [upgradable from: 1.2.24]
apt-utils/xenial-updates 1.2.29 amd64 [upgradable from: 1.2.24]
apturl/xenial-updates 0.5.2ubuntu11.2 amd64 [upgradable from: 0.5.2ubuntu11.1]
apturl-common/xenial-updates 0.5.2ubuntu11.2 amd64 [upgradable from: 0.5.2ubuntu11.1]
avahi-autoipd/xenial-updates 0.6.32~rc+dfsg-1ubuntu2.2 amd64 [upgradable from: 0.6.32~rc+dfsg-1ubuntu2]
...
<the output has been truncated>
```

**apt-get upgrade**

La commande ‘upgrade‘ est utilisé pour mettre à jour tous les paquets actuellement installé sur le système. Quelque soit les circonstances les paquets installés ne sont pas supprimés et les paquets qui ne sont pas déjà installé ne sont pas retrouvé et installé pour satisfaire des mises à jour de dépendances.

Les nouvelles version des logiciels actuellement installé qui ne peuvent pas être mis à jour sans changement du status d'installation d'un autre paquet resteront à leur version actuelle.

Un update doit être fait d'abord pour que apt-get connaisse les nouvelles versions de paquets disponibles.

```
root@ubuntu16-1:/# apt-get upgrade 
Reading package lists... Done
Building dependency tree       
Reading state information... Done
Calculating upgrade... Done
The following packages were automatically installed and are no longer required:
  libecap3 squid squid-common squid-langpack
Use 'apt autoremove' to remove them.
The following packages have been kept back:
  libdrm-amdgpu1 libdrm2 libegl1-mesa libgbm1 libgl1-mesa-dri libmm-glib0
  libqmi-proxy libwayland-egl1-mesa libxatracker2 linux-generic-hwe-16.04
  linux-headers-generic-hwe-16.04 linux-image-generic-hwe-16.04
  mesa-vdpau-drivers modemmanager ubuntu-minimal
The following packages will be upgraded:
  appstream apt apt-transport-https apt-utils apturl apturl-common
  avahi-autoipd avahi-daemon avahi-utils bamfdaemon base-files binutils
  bsdutils compiz compiz-core compiz-gnome compiz-plugins-default
  console-setup console-setup-linux cracklib-runtime desktop-file-utils dpkg
  dpkg-dev firefox firefox-locale-en fonts-opensymbol friendly-recovery fwupd
  fwupdate fwupdate-signed gettext gettext-base ghostscript ghostscript-x
  ...
  <the output has been truncated>
  ...
  update-notifier-common ure util-linux uuid-runtime va-driver-all
  wireless-regdb x11-common xdg-user-dirs xorg xserver-common
  xserver-xorg-video-amdgpu-hwe-16.04 xserver-xorg-video-ati-hwe-16.04
  xserver-xorg-video-intel-hwe-16.04 xserver-xorg-video-nouveau-hwe-16.04
  xserver-xorg-video-radeon-hwe-16.04
272 upgraded, 0 newly installed, 0 to remove and 15 not upgraded.
Need to get 276 MB of archives.
After this operation, 23.1 MB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://us.archive.ubuntu.com/ubuntu xenial-updates/main amd64 base-files amd64 9.4ubuntu4.7 [65.9 kB]
Get:2 http://ppa.launchpad.net/peek-developers/stable/ubuntu xenial/main amd64 peek amd64 1.3.1-0~ppa23~ubuntu16.04.1 [163 kB]
Get:3 http://us.archive.ubuntu.com/ubuntu xenial-updates/main amd64 bsdutils amd64 1:2.27.1-6ubuntu3.6 [51.2 kB]
...
<the output has been truncated>
...
Get:252 http://mirror.iranserver.com/ubuntu xenial-updates/main amd64 xserver-xorg-video-nouveau-hwe-16.04 amd64 1:1.0.15-2~16.04.1 [87.0 kB]
Fetched 270 MB in 4min 49s (933 kB/s)                                          
Extracting templates from packages: 100%
Preconfiguring packages ...
(Reading database ... 192776 files and directories currently installed.)
Preparing to unpack .../base-files_9.4ubuntu4.7_amd64.deb ...
Unpacking base-files (9.4ubuntu4.7) over (9.4ubuntu4.5) ...
Processing triggers for cracklib-runtime (2.9.2-1build2) ...
Processing triggers for man-db (2.7.5-1) ...
...
```

**apt-get dist-upgrade**

dist-upgrade en complément effectue la fonction de mise à jour, et gère aussi intelligemment les changement de dépendances avec les nouvelles versions de paquets (inluant le noyau)

Si vous voumlez mettre à jour, sans vous preoccupé des paquets qui seront ajoutés ou supprimé pour compléter les dépendance, utiliser la commande ‘dist-upgrade‘.

Si apt-get a un "leger" conflit de résolution du système, et il essayera de mettre à jour la plupart des paquets au détriment des paquets jugé moins importants si nécessaire.

```
root@ubuntu16-1:~# apt-get dist-upgrade 
Reading package lists... Done
Building dependency tree       
Reading state information... Done
Calculating upgrade... Done
The following packages were automatically installed and are no longer required:
  libecap3 squid squid-common squid-langpack
Use 'apt autoremove' to remove them.
The following NEW packages will be installed:
  amd64-microcode intel-microcode iucode-tool libdrm-common libllvm6.0
  libqmi-glib5 linux-headers-4.15.0-39 linux-headers-4.15.0-39-generic
  linux-image-4.15.0-39-generic linux-modules-4.15.0-39-generic
  linux-modules-extra-4.15.0-39-generic ubuntu-advantage-tools
The following packages will be upgraded:
  libdrm-amdgpu1 libdrm2 libegl1-mesa libgbm1 libgl1-mesa-dri libmm-glib0
  libqmi-proxy libwayland-egl1-mesa libxatracker2 linux-generic-hwe-16.04
  linux-headers-generic-hwe-16.04 linux-image-generic-hwe-16.04
  mesa-vdpau-drivers modemmanager ubuntu-minimal
15 upgraded, 12 newly installed, 0 to remove and 0 not upgraded.
Need to get 91.8 MB of archives.
After this operation, 428 MB of additional disk space will be used.
Do you want to continue? [Y/n] 
...
Get:1 http://mirror.iranserver.com/ubuntu xenial-updates/main amd64 ubuntu-advantage-tools all 10ubuntu0.16.04.1 [11.5 kB]
Get:2 http://mirror.iranserver.com/ubuntu xenial-updates/main amd64 ubuntu-minimal amd64 1.361.2 [2,662 B]
Get:3 http://mirror.iranserver.com/ubuntu xenial-updates/main amd64 libdrm-common all 2.4.91-2~16.04.1 [4,764 B]
Get:4 http://mirror.iranserver.com/ubuntu xenial-updates/main amd64 libdrm2 amd64 2.4.91-2~16.04.1 [30.8 kB]
Get:5 http://mirror.iranserver.com/ubuntu xenial-updates/main amd64 iucode-tool amd64 1.5.1-1ubuntu0.1 [33.8 kB]
Get:6 http://mirror.iranserver.com/ubuntu xenial-updates/main amd64 libdrm-amdgpu1 amd64 2.4.91-2~16.04.1 [18.9 kB]
...
Fetched 91.8 MB in 53s (1,725 kB/s)                                            
Selecting previously unselected package ubuntu-advantage-tools.
(Reading database ... 192821 files and directories currently installed.)
Preparing to unpack .../ubuntu-advantage-tools_10ubuntu0.16.04.1_all.deb ...
Unpacking ubuntu-advantage-tools (10ubuntu0.16.04.1) ...
Preparing to unpack .../ubuntu-minimal_1.361.2_amd64.deb ...
Unpacking ubuntu-minimal (1.361.2) over (1.361) ...
Selecting previously unselected package libdrm-common.
Preparing to unpack .../libdrm-common_2.4.91-2~16.04.1_all.deb ...
Unpacking libdrm-common (2.4.91-2~16.04.1) ...
Preparing to unpack .../libdrm2_2.4.91-2~16.04.1_amd64.deb ...
Unpacking libdrm2:amd64 (2.4.91-2~16.04.1) over (2.4.76-1~ubuntu16.04.1) ...
Selecting previously unselected package iucode-tool.
Preparing to unpack .../iucode-tool_1.5.1-1ubuntu0.1_amd64.deb ...
Unpacking iucode-tool (1.5.1-1ubuntu0.1) ...
...
```

**apt-get autoremove**

La sous-commande ‘autoremove‘ est utilisé pour supprimé automatiquement les paquets qui ont été installé pour satisfaire certaines dépendances d'autres paquets mais qui ne sont plus nécessaire. Par exemple:

```
root@ubuntu16-1:~# apt-get autoremove
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following packages will be REMOVED:
  libecap3 squid squid-common squid-langpack
0 upgraded, 0 newly installed, 4 to remove and 0 not upgraded.
After this operation, 10.8 MB disk space will be freed.
Do you want to continue? [Y/n] y
(Reading database ... 228575 files and directories currently installed.)
Removing squid (3.5.12-1ubuntu7.6) ...
Removing libecap3:amd64 (1.0.1-3ubuntu3) ...
Removing squid-common (3.5.12-1ubuntu7.6) ...
Removing squid-langpack (20150704-1) ...
Processing triggers for man-db (2.7.5-1) ...
Processing triggers for libc-bin (2.23-0ubuntu10) ...
```

**apt-get install**

Installer un paquet comme suit en specifiant le nom d'un seul paquet ou en installer plusieurs en une seule fois en listant tous leurs noms :

```
root@ubuntu16-1:~# apt-get install vsftpd
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following packages were automatically installed and are no longer required:
  openbsd-inetd pure-ftpd-common
Use 'apt autoremove' to remove them.
The following packages will be REMOVED:
  pure-ftpd
The following NEW packages will be installed:
  vsftpd
0 upgraded, 1 newly installed, 1 to remove and 2 not upgraded.
Need to get 115 kB of archives.
After this operation, 213 kB disk space will be freed.
Do you want to continue? [Y/n] y
Get:1 http://mirror.hmc.edu/ubuntu xenial/main amd64 vsftpd amd64 3.0.3-3ubuntu2 [115 kB]
Fetched 115 kB in 7s (15.8 kB/s)                                               
Preconfiguring packages ...
(Reading database ... 229455 files and directories currently installed.)
Removing pure-ftpd (1.0.36-3.2build1) ...
Processing triggers for man-db (2.7.5-1) ...
Selecting previously unselected package vsftpd.
(Reading database ... 229443 files and directories currently installed.)
Preparing to unpack .../vsftpd_3.0.3-3ubuntu2_amd64.deb ...
Unpacking vsftpd (3.0.3-3ubuntu2) ...
Processing triggers for systemd (229-4ubuntu21.10) ...
Processing triggers for ureadahead (0.100.0-19) ...
Processing triggers for man-db (2.7.5-1) ...
Setting up vsftpd (3.0.3-3ubuntu2) ...
Installing new version of config file /etc/ftpusers ...
Processing triggers for systemd (229-4ubuntu21.10) ...
Processing triggers for ureadahead (0.100.0-19) ...
```

**apt-get remove**

Supprime les paquets sans supprimer leurs fichiers de configuration :

```
root@ubuntu16-1:~# apt-get remove vsftpd 
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following packages were automatically installed and are no longer required:
  openbsd-inetd pure-ftpd-common
Use 'apt autoremove' to remove them.
The following packages will be REMOVED:
  vsftpd
0 upgraded, 0 newly installed, 1 to remove and 2 not upgraded.
After this operation, 336 kB disk space will be freed.
Do you want to continue? [Y/n] y
(Reading database ... 229498 files and directories currently installed.)
Removing vsftpd (3.0.3-3ubuntu2) ...
Processing triggers for man-db (2.7.5-1) ...
```

**apt-get purge**

apt avec remove, supprime seulement les fichiers du paquet mais les fichiers de configuration reste sur le système. Pour supprimer un paquet et ses fichiers de configuration nous devons utiliser la sous-commande purge.

```
root@ubuntu16-1:~# apt-get purge vsftpd
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following packages were automatically installed and are no longer required:
  openbsd-inetd pure-ftpd-common
Use 'apt autoremove' to remove them.
The following packages will be REMOVED:
  vsftpd*
0 upgraded, 0 newly installed, 1 to remove and 2 not upgraded.
After this operation, 0 B of additional disk space will be used.
Do you want to continue? [Y/n] y
(Reading database ... 229446 files and directories currently installed.)
Removing vsftpd (3.0.3-3ubuntu2) ...
Purging configuration files for vsftpd (3.0.3-3ubuntu2) ...
```

#### apt-get clean

Pour supprimer les paquets téléchargés (.deb), et déjà unstallé (et qui ne sont donc plus nécessaire) et cela permettra de faire de la place en nettoyant le cache.

```
root@ubuntu16-1:~# apt-get clean
```

#### apt-get autoclean

Pour supprimer toutes les archives stockés dans le cache pour les paquets qui ne peuvent plus être téléchargés (les paquets qui ne sont plus dans le depôt ou qui ont une nouvelle version dans le dépôt).

```
root@ubuntu16-1:~# apt-get autoclean 
Reading package lists... Done
Building dependency tree       
Reading state information... Done
```

#### apt-get check

Vérifie les paques actuellement installé pour voir les installations cassées.

```
root@ubuntu16-1:~# apt-get check 
Reading package lists... Done
Building dependency tree       
Reading state information... Done
```

### **Qu'est ce que apt-cache?**

L'outil en ligne de commande apt-cache est utilisé pour chercher des logiciels dans le cache de paquets apt. En d'autres termes, cet outil est utilisé pour chercher des paquets logiciels, collecter des information sur les paquets et aussi chercher quels sont les paquets disponibles pour l'installation sur un système basé sur Debian ou Ubuntu. Les fichiers de cache de APT se trouvent dans `/var/cache/apt/archives/`

Avant de lancer apt-cache il est conseillé de faire d'abord `apt-get check` :

```
root@ubuntu16-1:~# apt-get check 
Reading package lists... Done
Building dependency tree       
Reading state information... Done
```

Cette commande construit un nouveau cache en comparant l'état courant avec l'état des paquets listés dans les dépôts. Une fois que le apt-cache a été construit nous pouvons le requêter.

**apt-cache search**

Trouver des noms de paquet et la description des logiciels (ne nécessite pas d'accès root car il utilise le cache)

```
root@ubuntu16-1:~# apt-cache search netcat
netcat-openbsd - TCP/IP swiss army knife
corkscrew - tunnel TCP connections through HTTP proxies
cryptcat - A lightweight version netcat extended with twofish encryption
kafkacat - generic producer and consumer for Apache Kafka
libexpect-perl - Expect.pm - Perl Expect interface
netcat - TCP/IP swiss army knife -- transitional package
netcat-traditional - TCP/IP swiss army knife
netrw - netcat like tool with nice features to transport files over network
netsed - network packet-altering stream editor
socat - multipurpose relay for bidirectional data transfer
```

**apt-cache show**

Vérifie les informations sur un paquet et les affiches dans une description courte (numéro de version, vérifications des sums, taille, taille installé, categorie etc).

```
root@ubuntu16-1:~# apt-cache show netcat
Package: netcat
Priority: optional
Section: universe/net
Installed-Size: 30
Maintainer: Ubuntu Developers <ubuntu-devel-discuss@lists.ubuntu.com>
Original-Maintainer: Ruben Molina <rmolina@udea.edu.co>
Architecture: all
Version: 1.10-41
Depends: netcat-traditional (>= 1.10-39)
Filename: pool/universe/n/netcat/netcat_1.10-41_all.deb
Size: 3438
MD5sum: ef404dcc16fdf7b7fc049e8499b5d6dd
SHA1: 6445cd6789821840978146ef1d33a2475e092c42
SHA256: 1865119bdf88c21df3c039f987492defac68668bf7293ba279e76943c0fd1785
Description-en: TCP/IP swiss army knife -- transitional package
 This is a "dummy" package that depends on lenny's default version of
 netcat, to ease upgrades. It may be safely removed.
Description-md5: 1353f8c1d079348417c2180319bdde09
Bugs: https://bugs.launchpad.net/ubuntu/+filebug
```

**apt-cache showpkg**

La sous-commande ‘showpkg‘ vérifie les dépendance pour un paquet logiciel spécifique. Que les dépendances soient installés ou non.

```
root@ubuntu16-1:~# apt-cache showpkg netcat
Package: netcat
Versions: 
1.10-41 (/var/lib/apt/lists/mirror.iranserver.com_ubuntu_dists_xenial_universe_binary-amd64_Packages) (/var/lib/apt/lists/mirror.iranserver.com_ubuntu_dists_xenial_universe_binary-i386_Packages) (/var/lib/dpkg/status)
 Description Language: 
                 File: /var/lib/apt/lists/mirror.iranserver.com_ubuntu_dists_xenial_universe_binary-amd64_Packages
                  MD5: 1353f8c1d079348417c2180319bdde09
 Description Language: en
                 File: /var/lib/apt/lists/mirror.iranserver.com_ubuntu_dists_xenial_universe_i18n_Translation-en
                  MD5: 1353f8c1d079348417c2180319bdde09


Reverse Depends: 
  netcat-openbsd,netcat 1.10-35
  playonlinux,netcat
  tomcat8-user,netcat
  tomcat7-user,netcat
  ltsp-client-core,netcat
  ltsp-client,netcat
 ...
Dependencies: 
1.10-41 - netcat-traditional (2 1.10-39) 
Provides: 
1.10-41 - 
Reverse Provides: 
netcat-traditional 1.10-41 (= )
netcat-openbsd 1.105-7ubuntu1 (= )
```

**apt-cache depends** et **apt-cache rdepends** nous permettent d'obtenir des informations sur les dépendances.

```
root@ubuntu16-1:~# apt-cache depends telnet
telnet
  Depends: netbase
  Depends: libc6
  Depends: libstdc++6
  Replaces: <netstd>
root@ubuntu16-1:~# apt-cache rdepends telnet
telnet
Reverse Depends:
  ubuntu-standard
  ubuntu-standard
 |zssh
  tucnak
  ser2net
  procserv
  mininet
 |matanza
  libtelnet-utils
  libtelnet-dev
  lavapdu-daemon
 |dish
 |coturn
  coldfire
```

Regarder les dépendances de telnet et voir quels paquets dépendent de lui.

Utiliser **apt-cache stat** pour voir les statistiques des paquets installés sur votre ordinateur.

## dselect

> dselect est un front-end pour dpkg qui est utilisé pour gérer les paquer logiciel sur Debian et les distributions basées sur Debian. Vous pouvez utiliser dselect pour installer les paquets sur votre système depuis des archives APT définis dans /etc/apt/sources.list, voir les paquets déjà installé, déinstaller et mettre à jour des paquets.
>
> ![](.gitbook/assets/debpack-dselect.png)

## aptitude

aptitude est un autre gestionnaire de paquet haut-niveau pour les systèmes basé sur Debian, et peut être utiliser pour faire des tâches de gestion (installation, mise à jour, suppression des paquets, et aussi résolutions des dépendances automatique) de manière rapide et facile. Il fournit les mêmes fonctionnalités que apt-get et en ajoute quelques unes comme l'accès à différentes version d'un paquet.

Vous devrez peut être installer aptitude d'abord ( `apt-get install aptitude` )

```
root@ubuntu16-1:~#apt-get install aptitude

root@ubuntu16-1:~# aptitude --help
aptitude 0.7.4
Usage: aptitude [-S fname] [-u|-i]
       aptitude [options] <action> ...
  Actions (if none is specified, aptitude will enter interactive mode):

 install      - Install/upgrade packages.
 remove       - Remove packages.
 purge        - Remove packages and their configuration files.
 hold         - Place packages on hold.
 unhold       - Cancel a hold command for a package.
 markauto     - Mark packages as having been automatically installed.
 unmarkauto   - Mark packages as having been manually installed.
 forbid-version - Forbid aptitude from upgrading to a specific package version.
 update       - Download lists of new/upgradable packages.
 safe-upgrade - Perform a safe upgrade.
 full-upgrade - Perform an upgrade, possibly installing and removing packages.
 build-dep    - Install the build-dependencies of packages.
 forget-new   - Forget what packages are "new".
 search       - Search for a package by name and/or expression.
 show         - Display detailed information about a package.
 versions     - Displays the versions of specified packages.
 clean        - Erase downloaded package files.
 autoclean    - Erase old downloaded package files.
 changelog    - View a package's changelog.
 download     - Download the .deb file for a package.
 reinstall    - Download and (possibly) reinstall a currently installed package.
 why          - Show the manually installed packages that require a package, or
                why one or more packages would require the given package.
 why-not      - Show the manually installed packages that lead to a conflict
                with the given package, or why one or more packages would
                lead to a conflict with the given package if installed.

  Options:
 -h             This help text.
 --no-gui       Do not use the GTK GUI even if available.
 -s             Simulate actions, but do not actually perform them.
 -d             Only download packages, do not install or remove anything.
 -P             Always prompt for confirmation of actions.
 -y             Assume that the answer to simple yes/no questions is 'yes'.
 -F format      Specify a format for displaying search results; see the manual.
 -O order       Specify how search results should be sorted; see the manual.
 -w width       Specify the display width for formatting search results.
 -f             Aggressively try to fix broken packages.
 -V             Show which versions of packages are to be installed.
 -D             Show the dependencies of automatically changed packages.
 -Z             Show the change in installed size of each package.
 -v             Display extra information. (may be supplied multiple times).
 -t [release]   Set the release from which packages should be installed.
 -q             In command-line mode, suppress the incremental progress
                indicators.
 -o key=val     Directly set the configuration option named 'key'.
 --with(out)-recommends    Specify whether or not to treat recommends as
                strong dependencies.
 -S fname       Read the aptitude extended status info from fname.
 -u             Download new package lists on startup.
                  (terminal interface only)
 -i             Perform an install run on startup.
                  (terminal interface only)

                  This aptitude does not have Super Cow Powers.
```

Comme exemple recherchons et obtenons des informations sur netcat en utilisant aptitude :

```
root@ubuntu16-1:~# aptitude update
Hit http://mirror.iranserver.com/ubuntu xenial InRelease                  
Get: 1 http://mirror.iranserver.com/ubuntu xenial-updates InRelease [109 kB]
Hit http://ppa.launchpad.net/peek-developers/stable/ubuntu xenial InRelease 
Get: 2 http://mirror.iranserver.com/ubuntu xenial-backports InRelease [107 kB]
Get: 3 http://mirror.iranserver.com/ubuntu xenial-security InRelease [107 kB]
Fetched 323 kB in 1s (208 kB/s)                             

root@ubuntu16-1:~# aptitude search netcat
i   netcat                         - TCP/IP swiss army knife -- transitional p
v   netcat:i386                    -                                          
i   netcat-openbsd                 - TCP/IP swiss army knife                  
p   netcat-openbsd:i386            - TCP/IP swiss army knife                  
i A netcat-traditional             - TCP/IP swiss army knife                  
p   netcat-traditional:i386        - TCP/IP swiss army knife                  

root@ubuntu16-1:~# aptitude show netcat
[  0%] Reading package lists
Package: netcat                          
State: installed
Automatically installed: no
Version: 1.10-41
Priority: optional
Section: universe/net
Maintainer: Ubuntu Developers <ubuntu-devel-discuss@lists.ubuntu.com>
Architecture: all
Uncompressed Size: 30.7 k
Depends: netcat-traditional (>= 1.10-39)
Provided by: netcat-openbsd (1.105-7ubuntu1), netcat-traditional (1.10-41)
Description: TCP/IP swiss army knife -- transitional package
 This is a "dummy" package that depends on lenny's default version of netcat,
 to ease upgrades. It may be safely removed.
```

**Differences entre APT et Aptitude**

En dehors de la différence principal qui est que Aptitude est un gestionnaire de paquet haut-niveau alors que APT est un gestionnaire de paquet bas-niveau qui peut être utilisé par des gestionnaires de paquets de plus haut-niveau, les autres points qui séparent ces deux gestionnaires de paquets sont :

* Aptitude possède plus de fonctionnalité que apt-get et intègre des fonctionnalité de apt-get et ses variante incluant apt-cache et apt-mark.
* Alors que apt-get n'a pas de GUI (Graphical User Interface), Aptitude a une Interface Utilisateur interractive en mode texte

![](.gitbook/assets/debpack-aptitude.jpg)

* Aptitude a une meilleure gestion des paquets que apt-get:

1.Lors de la suppresionn d'un paquet installé, Aptitude supprime automatique les paquets inutilisés, alors que apt-get nécessite que l'utilisateur lui spécifie en ajoutant l'option ‘—auto-remove’ ou en spécifiant ‘apt-get autoremove’.

2.Pour aller plus loin lorsque certaines action sont blockés ou pas possible, Aptitude propose les commande 'why' et ‘why-not’.

3.Alors que apt-get s'arretera probablement en cas de conflit lors d'une installation ou d'une suppresionn d'un paquet avec un message, Aptitude peut suggérer des actions possibles pour supprimer le conflit.

### Résumé

Système de la famille Debian

**dpkg:  **\
****

* bas-niveau ou gestionnaire de paquet sous-marin
* Extrait, installe, supprime et construit les paquets
* Il ne peut pas télécharger ou résoudre les dépendances

**Advance Package Tool (apt) :**

* Construit au dessus de dpkg (il en dépend)
* Travaille avec des groupes de paquets
* Il peut télécharger automatiquement et intaller les paques en s'occupant de la résolution de dépendances
* L'interface native se fait via les commandes apt-get et apt-cache
* Habituellement une interface utilisateur est créé par dessus pour chaque distribution spécifique (Software updater est un exemple)

.

.

.

sources:

[http://www.pepedocs.com/notes?tid=linux\&nid=lfs101x](http://www.pepedocs.com/notes?tid=linux\&nid=lfs101x)

[https://www.dennyzhang.com/linux_package_mgmt](https://www.dennyzhang.com/linux_package_mgmt)

[https://www.networkworld.com/article/3305810/linux/how-to-list-repositories-on-linux.html](https://www.networkworld.com/article/3305810/linux/how-to-list-repositories-on-linux.html)

[https://help.ubuntu.com/lts/serverguide/dpkg.html.en](https://help.ubuntu.com/lts/serverguide/dpkg.html.en)

[https://www.tecmint.com/dpkg-command-examples/](https://www.tecmint.com/dpkg-command-examples/)

[https://geek-university.com/linux/dselect/](https://geek-university.com/linux/dselect/)( like dpkg but has UI)

[https://unix.stackexchange.com/questions/20504/the-difference-between-deb-versus-deb-src-in-sources-list](https://unix.stackexchange.com/questions/20504/the-difference-between-deb-versus-deb-src-in-sources-list)

[https://askubuntu.com/questions/58364/whats-the-difference-between-multiverse-universe-restricted-and-main](https://askubuntu.com/questions/58364/whats-the-difference-between-multiverse-universe-restricted-and-main)

[https://www.tecmint.com/apt-advanced-package-command-examples-in-ubuntu/](https://www.tecmint.com/apt-advanced-package-command-examples-in-ubuntu/)

[https://wiki.debian.org/debconf](https://wiki.debian.org/debconf)

[https://www.tecmint.com/useful-basic-commands-of-apt-get-and-apt-cache-for-package-management/](https://www.tecmint.com/useful-basic-commands-of-apt-get-and-apt-cache-for-package-management/)

[https://www.tecmint.com/apt-advanced-package-command-examples-in-ubuntu/](https://www.tecmint.com/apt-advanced-package-command-examples-in-ubuntu/)

[https://askubuntu.com/questions/32191/how-do-i-remove-cached-deb-files](https://askubuntu.com/questions/32191/how-do-i-remove-cached-deb-files)

[https://askubuntu.com/questions/81585/what-is-dist-upgrade-and-why-does-it-upgrade-more-than-upgrade](https://askubuntu.com/questions/81585/what-is-dist-upgrade-and-why-does-it-upgrade-more-than-upgrade)

[https://askubuntu.com/questions/222348/what-does-sudo-apt-get-update-do](https://askubuntu.com/questions/222348/what-does-sudo-apt-get-update-do)

[https://www.tecmint.com/linux-package-management/](https://www.tecmint.com/linux-package-management/)

[https://www.tecmint.com/difference-between-apt-and-aptitude/](https://www.tecmint.com/difference-between-apt-and-aptitude/)
