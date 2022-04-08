# 102.3. Gérer les bibliothèques partagées

## **102.3 Gérer les bibliothèques partagées**

**Poids:** 1

**Description:** Candidates should be able to determine the shared libraries that executable programs depend on and install them when necessary.

**Connaissances clés:**

* Identify shared libraries
* Identify the typical locations of system libraries
* Load shared libraries

**Concepts et Utilitaires:**

* ldd
* ldconfig
* /etc/ld.so.conf
* LD_LIBRARY_PATH

#### Qu'est ce qu'une bibliothèque?

En programmation, un bibliothèque est un assortiment de pièce pré-compilé de code que nous pouvons réutilisés dans un programme. Les bibliothèques simplifie la vie pour les développeurs, dans le sens où elles fournissent des fonctions réutilisables, des routines, des classes, des structures de données et d'autres choses (écrites par un autre développeur), qui peuvent être utiliser dans leurs programmes.

![](.gitbook/assets/sharedlib-intro.jpg)

Linux supporte deux classes de bibliothèque, nommées :

* Bibliothèque statique - sont lié à un programme de manière statique lors de la compilation.
* Bibliothèques dynamiques ou partagées – sont chargé lors du lancement d'un programme et chargé en mémoire et la liaison intervient lors de l'éxecution.

**Bibliothèque statique vs Bibliothèques partagées**

* Chaque programme utilise des routine depuis une bibliothèque statique a une copie de celle-ci dans le fichier exécutable. Cela gaspille de l'espace disque et (dans le cas ou plus d'un programme utilise celle-ci en même temps) de la RAM également. Aussi, lors de la mise à jour d'une bibliothèque statique, tous les programmes qui l'utilise doivent être recompilé afin de bénéficier du nouveau code. 
* Alors que, lorsqu'un programme utilise une bibliothèque partagée, le binaire du programme n'inclut pas de copie du code, mais seulement une référence vers la bibliothèque. Lors du chargement du programme, la bibliothèque est trouvé et chargé en mémoire au même moment que le programme.** ** Un avantage de l'utilisation des bibliothèque dynamique est que lors de la mise à jour de la bibliothèque (pour des raisons de sécurités) tous les programmes bénéficie du nouveau code sans besoin d'être recompilé.

{% hint style="info" %}
Les bilbiothèques paragées ou dynamique peuvent être catégorisé en deux groupe (selon le périmètre de l'exam LPIC) :

* **Bibliothèque dynamiquement liées (Dynamically linked libraries)** – ici un programme est lié avec une bibliothèque partagée et le noyau charge la bibliothèque (dans le cas où elle n'est pas déjà en mémoire) lors de l'exécution.
* **Bibliothèque dynamiquement chargées (Dynamically loaded libraries)** – le programme prend le contrôle complet en appelant des fonctions avec la bibliothèque.
{% endhint %}

### Localisation des bibliothèques sur linux

En fonction des bibliothèques, Linux stocke ses bibliothèques principalement dans trois localisations :

* **/lib**
* **/usr/lib**
* **/usr/local/lib**

**/lib** : Le dossier /lib contient les **images de bibliothèques partagées nécessaire au démarrage** que le système lance et ceux des commandes du système de fichier racine, comme par exemple ceux des binaires de /bin et /sbin.

```
root@ubuntu16-1:~# ls /lib
apparmor  ifupdown                              modules        udev
brltty    init                                  recovery-mode  ufw
cpp       klibc-k3La8MUnuzHQ0_kG8hokcGAC0PA.so  resolvconf     x86_64-linux-gnu
crda      linux-sound-base                      systemd        xtables
firmware  lsb                                   sysvinit
hdparm    modprobe.d                            terminfo
```

Le dossier /lib et ses dossiers associés qui sont /lib32 et /lib64.

**/usr/lib** :**Toutes les bibliothèques des logiciels** sont installés ici. Cela **ne contient pas les bibliothèques du système par défaut ou les bibliothèques du noyau**.

```
root@ubuntu16-1:~# ls /usr/lib/
```

**/usr/local/lib**: Pour placer des **fichiers de bibliothèques supplémentaires**. Ces fichiers de bibliothèques peuvent être utiliser dans différentes applications.

```
root@ubuntu16-1:~# ls /usr/local/lib/
python2.7  python3.5
```

Noter aussi que le dossier **/var/lib**, contient **les fichiers / données des bibliothèques dynamiques** comme la base de données rpm/dpkg et les scores des jeux.

```
root@ubuntu16-1:~# ls /var/lib/
AccountsService      fwupd            nginx                    udisks2
acpi-support         gconf            nssdb                    update-manager
alsa                 ghostscript      os-prober                update-notifier
apparmor             git              pam                      update-rc.d
app-info             hp               plymouth                 upower
apt                  initramfs-tools  polkit-1                 urandom
aspell               initscripts      python                   ureadahead
avahi-autoipd        insserv          resolvconf               usb_modeswitch
binfmts              ispell           samba                    usbutils
bluetooth            libreoffice      sgml-base                vim
colord               lightdm          snapd                    vmware
dbus                 lightdm-data     snmp                     vmware-caf
dhcp                 locales          sudo                     whoopsie
dictionaries-common  logrotate        systemd                  xfonts
doc-base             man-db           ubiquity                 xkb
dpkg                 misc             ubuntu-drivers-common    xml-core
emacsen-common       mlocate          ubuntu-release-upgrader
fail2ban             NetworkManager   ucf
```

Dans cette section nous verrons plus spécifiquement les bibliothèques partagées.

### Convention de nommage des bibliothèques partagées

Les bibliothèques partagées sont nommées de trois manières :

* nom de la bibliothèque (aussi appelé "soname")  
* nom du fichier (aussi appelé "real name")(chemin absolu ver le fichier qui stocke le code de la bibliothèque)
* "linker name"

**1)**Chaque bibliothèque partagée à un nom special appelé **"soname''**. Le soname a le préfixe "**lib**'', le nom de la bibliothèque, l'extension "**.so**'', suivi par une période et un  **numéro de version** qui est incrémenté lors des changements d'interface . 

```
libreadline.so.6
```

Un soname complet inclut en préfix le dossier dans lequel il se trouve.

```
/usr/lib/libreadline.so.6
```

Sur un sysème fonctionnel un soname complet est simplement un lien symbolique vers le "real name" de la bibliothèque partagée.

**2)**Chaque bibliothèque partagé a aussi un **"real name''**, qui est le nom du fichier qui contient le code actuel de la bibliothèque. Le nom réel ajoute au soname une période, un numéro mineur, une autre période et un numéro de publication (release number). La dernière période et le numéro de publication sont optionnels.

```
libreadline.so.6.3
```

**3)**De plus, il y a le nom que le compilateur utilise lorsqu'il appelle une bibliothèque (qui est appelé **'linker name'**), qui est simplement le soname sans aucune numéro de version.

```
libreadline.so
```

Illustrons cela avec un exemple :

```
root@ubuntu16-1:/lib/x86_64-linux-gnu# ls -l | grep libread
lrwxrwxrwx 1 root root      18 Nov 26  2017 libreadline.so.6 -> libreadline.so.6.3
-rw-r--r-- 1 root root  282392 Feb  4  2016 libreadline.so.6.3
```

Ce, `/usr/lib/libreadline.so.6` est le soname complet, qui est défini comme un lien symbolique vers le nom réel comme `/usr/lib/libreadline.so.6.3`. Il devrait y avoir un nom lié(linker name), `/usr/lib/libreadline.so` qui pourrait être un lien symbolique se référent à  `/usr/lib/libreadline.so.6`.

### ldd

Pour **obtenir une liste de toutes les bibliothèques partagées dont dépendant un fichier binaire**, vous pouvez utiliser l'utilitaire ldd. Par exemple voyons quels sont les bibliothèques partagées requises par la commande ls :

```
root@ubuntu16-1:~# which ls
/bin/ls
root@ubuntu16-1:~# ldd /bin/ls
    linux-vdso.so.1 =>  (0x00007ffe511d5000)
    libselinux.so.1 => /lib/x86_64-linux-gnu/libselinux.so.1 (0x00007f687d3b1000)
    libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f687cfe7000)
    libpcre.so.3 => /lib/x86_64-linux-gnu/libpcre.so.3 (0x00007f687cd77000)
    libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007f687cb72000)
    /lib64/ld-linux-x86-64.so.2 (0x00005591d42e3000)
    libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007f687c955000)
```

Essayer `ltrace ls` pour obtenir une surprise! Mais comment ls peut trouver ses bibliothèque partagées ?

### Trouver les bibliothèques partagées sur Linux

Un programme peut appelé une bibliothèque en utilisant le nom de la bibliothèque ou le nom du fichier, et un chemin de bibliothèque stocke les dossier dans lequel les bibliothèques peuvent être trouvées sur le système de fichier. Mais comment les retrouver ? Voyons la structure avec une image :

![](.gitbook/assets/sharedlib-filestructure.jpg)

Ces trois fichiers aide les programme à trouver les bibliothèques partagées dont elles ont besoin.

### ld.so.conf

Le fichier ld.so.conf dit au système où chercher les fichier de bibliothèques, comme une sorte de carnet d'adresses.

```
root@ubuntu16-1:~# cat /etc/ld.so.conf
include /etc/ld.so.conf.d/*.conf
```

Historiquement, il incluait aussi tous les chemins dans lesquels les bibliothèques existaient. Aujourd'hui, plus d'abstraction a été mise en place et il pointe seulement vers le dossier ld.so.conf.d. Simplement il dit va dans le dossier ld.so.conf.d et charge tous les fichiers de configuration.

### /etc/ld.so.conf.d

Le dossier ld.so.conf.d contient différents fichiers de configuration pour le noyau ou pour les autres applications tiers.

```
root@ubuntu16-1:~# ls -la /etc/ld.so.conf.d/
total 32
drwxr-xr-x   2 root root  4096 Oct  7 00:37 .
drwxr-xr-x 142 root root 12288 Nov 11 00:36 ..
-rw-rw-r--   1 root root    38 Nov 24  2014 fakeroot-x86_64-linux-gnu.conf
-rw-r--r--   1 root root    44 Jan 27  2016 libc.conf
-rw-r--r--   1 root root   276 Oct  7 00:37 vmware-tools-libraries.conf
-rw-r--r--   1 root root    68 Apr 14  2016 x86_64-linux-gnu.conf
lrwxrwxrwx   1 root root    43 Nov 26  2017 x86_64-linux-gnu_EGL.conf -> /etc/alternatives/x86_64-linux-gnu_egl_conf
lrwxrwxrwx   1 root root    42 Nov 26  2017 x86_64-linux-gnu_GL.conf -> /etc/alternatives/x86_64-linux-gnu_gl_conf
```

Voyons ce qu'il y a dans ces fichiers de configuration :

```
root@ubuntu16-1:~# cat /etc/ld.so.conf.d/libc.conf 
# libc default configuration
/usr/local/lib
root@ubuntu16-1:~# cat /etc/ld.so.conf.d/x86_64-linux-gnu.conf 
# Multiarch support
/lib/x86_64-linux-gnu
/usr/lib/x86_64-linux-gnu
root@ubuntu16-1:~# cat /etc/ld.so.conf.d/vmware-tools-libraries.conf 
/usr/lib/vmware-tools/lib32/libvmGuestLib.so
/usr/lib/vmware-tools/lib64/libvmGuestLib.so
/usr/lib/vmware-tools/lib32/libvmGuestLibJava.so
/usr/lib/vmware-tools/lib64/libvmGuestLibJava.so
/usr/lib/vmware-tools/lib32/libDeployPkg.so
/usr/lib/vmware-tools/lib64/libDeployPkg.so
```

Nous pouvons ajouter un nouveau fichier de configuration ou modifier n'importe lequel afin d'inclure de nouveau chemin.

### ldconfig

Puisque les bibliothèques partagées peuvent exister dans de nombreux dossiers différents, rechercher dans tous ces dossier lorsqu'un programme est lancé serait grandement inefficace, ce qui est un des grand inconvénient des bibliothèques partagées. Cependant un mécanisme de cache est utilisé, il est mis en place par le programme  ldconfig.

Par défaut, ldconfig lit le contenu de /etc/ld.so.conf, crée les liens symboliques appropriés dans le dossier des liens dynamique, et il écrit un cache dans  /etc/ld.so.cache qui est facilement utilisable par d'autres programmes.

### ld.so.cache

C'est un fichier binaire, donc quelque chose que nous avons pas l'habitude de voir qui ressemble à ça :

```
root@ubuntu16-1:~# cat /etc/ld.so.cache 
ld.so-1.7.0�^^<^P^~^�^�^�^�^
                                       _<_F_f_s_�_�_�_�_``<`K`t`�`�`�`�`�`a#aJa\a�a�a�a�a�a
b4bFbrb�b�b�b�b�b$c7cdcuc�c�c�c�cd(dTdgd�d�d�d�de0e^ere�e�e�e�ef,fVfgf�f�f�f�f
                                           ggFgSgvg�g�g�g�g�g(h7h`hth�h�h�h�h"i9iji�i�i�i�ij>jRj�j�j�j�jk%kTklk�k�k�kl6lKlzl�l�l�lm"mXmpm�m�m�m�mn%nLn]n�n�n�n�n6oFopo�o�o�o�o�o,p=phpp�p�p�p    q:qQq�q�q�q�q�q�q&r3rZrmr�r�r�r�rssHsZs�s�s�s�s�stWtlt�t�tuuPucu�u�u�u�u
vv@vKvpv}v�v�v�v�vww<wMwxw�w�w�w�w�wx5xfxzx�x�x�x�x&y<yly�y�y�yzz>z\z�z�z�z�z {.{V{l{�{�{�{|N|a|�|�|�|�|*}?}n}�}�}�}
~"~T~f~�~�~�~�~�~
4Adr�����'�N�Z�����Ѐހ��P�m�����ց����J�W�~���������$�6�b�t�����؃��
�4�L�~�����ȅ����$�3�\�h�����Ćц���@�Z����������;�t������ڈ���(�N�i�����މ��
�:�G�n�~��������� �6�f�x�����Ҏ���)�Z�x���ď��ь��
<The out put has been truncuated>
```

Utiliser la commande `string` a la place et vous aurez un contenu lisible par un humain du fichier binaire, à noter aussi que `lconfig -p` affiche le cache.

Ceci est très important spécialement lorsque nous venons d'installer un nouvelle bibliothèque partagée ou que nous en avons créé une, ou créer un nouveau dossier de bibliohtèque. Nous avons besoin de lancer la commande ldconfig pour que les changements soient appliqués.(La sortie a été tronquée):

```
root@ubuntu16-1:~# ldconfig -v
/sbin/ldconfig.real: Path `/lib/x86_64-linux-gnu' given more than once
/sbin/ldconfig.real: Path `/usr/lib/x86_64-linux-gnu' given more than once
/usr/lib/x86_64-linux-gnu/libfakeroot:
    libfakeroot-0.so -> libfakeroot-tcp.so
/usr/local/lib:
/usr/lib/vmware-tools/lib32/libvmGuestLib.so:
    libvmGuestLib.so -> libvmGuestLib.so
/usr/lib/vmware-tools/lib64/libvmGuestLib.so:
    libvmGuestLib.so -> libvmGuestLib.so
/usr/lib/vmware-tools/lib32/libvmGuestLibJava.so:
    libvmGuestLibJava.so -> libvmGuestLibJava.so
/usr/lib/vmware-tools/lib64/libvmGuestLibJava.so:
    libvmGuestLibJava.so -> libvmGuestLibJava.so
/usr/lib/vmware-tools/lib32/libDeployPkg.so:
    libDeployPkg.so -> libDeployPkg.so
/usr/lib/vmware-tools/lib64/libDeployPkg.so:
    libDeployPkg.so -> libDeployPkg.so
/lib/x86_64-linux-gnu:
    libsepol.so.1 -> libsepol.so.1
    libisc-export.so.160 -> libisc-export.so.160.0.0
    libdns-export.so.162 -> libdns-export.so.162.1.3
    libselinux.so.1 -> libselinux.so.1
    libuuid.so.1 -> libuuid.so.1.3.0
    libc.so.6 -> libc-2.23.so
    libnss_mdns6.so.2 -> libnss_mdns6.so.2
    libnss_files.so.2 -> libnss_files-2.23.so
    ...
/sbin/ldconfig.real: /lib/x86_64-linux-gnu/ld-2.23.so is the dynamic linker, ignoring

    ld-linux-x86-64.so.2 -> ld-2.23.so
    libnih-dbus.so.1 -> libnih-dbus.so.1.0.0
    libnss_mdns.so.2 -> libnss_mdns.so.2
    libatasmart.so.4 -> libatasmart.so.4.0.5
    libdbus-1.so.3 -> libdbus-1.so.3.14.6
    libpthread.so.0 -> libpthread-2.23.so
    libutil.so.1 -> libutil-2.23.so
    libz.so.1 -> libz.so.1.2.8
    ...
/usr/lib/x86_64-linux-gnu:
    libplc4.so -> libplc4.so
    libcdr-0.1.so.1 -> libcdr-0.1.so.1.0.2
    libmwaw-0.3.so.3 -> libmwaw-0.3.so.3.0.7
    libjbig.so.0 -> libjbig.so.0
    libnssutil3.so -> libnssutil3.so
    libOxideQtQuick.so.0 -> libOxideQtQuick.so.0
    libubsan.so.0 -> libubsan.so.0.0.0
    libgudev-1.0.so.0 -> libgudev-1.0.so.0.2.0
    libx265.so.79 -> libx265.so.79
    libnetfilter_conntrack.so.3 -> libnetfilter_conntrack.so.3.5.0
    libsamba-util.so.0 -> libsamba-util.so.0.0.1
    ...
d.so
    libtotem-plparser-mini.so.18 -> libtotem-plparser-mini.so.18.1.0
    libhcrypto.so.4 -> libhcrypto.so.4.1.0
    libxcb-shape.so.0 -> libxcb-shape.so.0.0.0
    libQt5PrintSupport.so.5 -> libQt5PrintSupport.so.5.5.1
    libdmapsharing-3.0.so.2 -> libdmapsharing-3.0.so.2.9.34
    libyaml-0.so.2 -> libyaml-0.so.2.0.4
    libfreerdp-core.so.1.1 -> libfreerdp-core.so.1.1.0
    libzeitgeist-1.0.so.1 -> libzeitgeist-1.0.so.1.1.4
    libHalf.so.12 -> libHalf.so.12.0.0
    libxcb-util.so.1 -> libxcb-util.so.1.0.0
2.4.so.1.3.0
    libhardware.so.2 -> libhardware.so.2.0.0
    libindicator3.so.7 -> libindicator3.so.7.0.0
    libcmis-0.5.so.5 -> libcmis-0.5.so.5.0.0
    libmpxwrappers.so.0 -> libmpxwrappers.so.0.0.0
    libkrb5.so.3 -> libkrb5.so.3.3
    libavdevice-ffmpeg.so.56 -> libavdevice-ffmpeg.so.56.4.100
    libndp.so.0 -> libndp.so.0.0.2
    libao.so.4 -> libao.so.4.0.0
    libsignon-plugins.so.1 -> libsignon-plugins.so.1.0.0
    liba52-0.7.4.so -> liba52-0.7.4.so
    ...
/usr/lib/x86_64-linux-gnu/mesa-egl:
    libEGL.so.1 -> libEGL.so.1.0.0
/usr/lib/x86_64-linux-gnu/mesa:
    libGL.so.1 -> libGL.so.1.2.0
/lib:
/usr/lib:
    libgdiplus.so.0 -> libgdiplus.so.0.0.0
    libMonoPosixHelper.so -> libMonoPosixHelper.so
    libMonoSupportW.so -> libMonoSupportW.so
    libnetpbm.so.10 -> libnetpbm.so.10.0
    libfsplib.so.0 -> libfsplib.so.0.0.0
```

La commande ldconfig a quelque options:

```
root@ubuntu16-1:~# ldconfig --help
Usage: ldconfig.real [OPTION...]
Configure Dynamic Linker Run Time Bindings.

  -c, --format=FORMAT        Format to use: new, old or compat (default)
  -C CACHE                   Use CACHE as cache file
  -f CONF                    Use CONF as configuration file
  -i, --ignore-aux-cache     Ignore auxiliary cache file
  -l                         Manually link individual libraries.
  -n                         Only process directories specified on the command
                             line.  Don't build cache.
  -N                         Don't build cache
  -p, --print-cache          Print cache
  -r ROOT                    Change to and use ROOT as root directory
  -v, --verbose              Generate verbose messages
  -X                         Don't update symbolic links
  -?, --help                 Give this help list
      --usage                Give a short usage message
  -V, --version              Print program version

Mandatory or optional arguments to long options are also mandatory or optional
for any corresponding short options.

For bug reporting instructions, please see:
<https://bugs.launchpad.net/ubuntu/+source/glibc/+bugs>.
```

Après avoir créer une bibliothèque partagée, nous avons besoin de l'installer.

### Installer une bibliothèque dynamique: <a href="f3ce" id="f3ce"></a>

Il y a différentes manière d'installer une bibliothèque partagée : 

**1)**Nous pouvons la déplacer dans un des dossier standard mentionné précédemment et lancer la commande ldconfig.

**2)**La méthode ci-dessus rend le chemin de la bibliothèque permanenent. Pour le définir de manière temporaire, utiliser la variable d'environnement LD_LIBRARY_PATH depuis la ligne de commande.

## LD_LIBRARY_PATH

Dans Linux, la variable d'environnement LD_LIBRARY_PATH est un ensemble de dossier séparé par des ':' où les bibliothèques partagées doivent être rechercher en premier, avant l'ensemble de dossiers standard; c'est utile pour débogguer une nouvelle bibliothèque ou utiliser une bibliothèque non standard pour un besoin spécifique.

```
root@ubuntu16-1:~# echo $LD_LIBRARY_PATH
```

Les liens dynamiques habituels sur linux utilise un cache pour rechercher ses bibliothèques. Donc il n'y a pas de valeur par défaut pour LD_LIBRARY_PATH, la recherche par défaut des bibliothèques n'en a pas besoin. Si LD_LIBRARY_PATH est défini, alors il sera utilsié en premier, mais ne désactive pas les autres recherche (qui inclut également quelques dossiers par défaut).

Qu'il y ait ou non une valeur par défaut, vous pouvez utiliser la commande ci-dessous pour ajouter notre chemin vers la nouvelle bibliothèque :

```
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/your/custom/path/
```

N'oubliez pas que ces modifications ne sont pas permanentes et si vous voulez rendre ces changements permanent, ajouter la ligne ci-dessus à l'initilisation du shell dans le fichier**/etc/profile**(global) or**\~/.profile**(spécifique à l'utilisateur), ce que nous verrons dans la prochaine.

C'est terminé!


## Exercices

### Exercices guidés



1. Séparez les noms des bibliothèques partagées suivantes en leurs composantes :

| Nom complet du fichier | Nom de la bibliothèque | suffixe so | Numéro de version |
| --------------- |---------------| --------------- | --------------- |
| linux-vdso.so.1 |  | | |
| libprocps.so.6 |  |  |   |
| libdl.so.2 |  |  | | 
| libc.so.6 |  |  |  |
| libsystemd.so.0 |  |  |  |
| ld-linux-x86-64.so.2 |  |  |  |

<details>
  <summary>Réponse</summary>

| Nom complet du fichier | Nom de la bibliothèque | suffixe so | Numéro de version |
| --------------- |---------------| --------------- | --------------- |
| linux-vdso.so.1 | linux-vdso | so | 1 |
| libprocps.so.6 | libprocps | so |  6 |
| libdl.so.2 | libdl | so | 2 | 
| libc.so.6 | libc | so | 6 |
| libsystemd.so.0 | libsystemd | so | 0 |
| ld-linux-x86-64.so.2 | ld-linux-x86-64 | so | 2 |

</details>

2. Vous avez développé un logiciel et vous souhaitez ajouter un nouveau répertoire de bibliothèques partagées à votre système (/opt/lib/mylib). Vous écrivez son chemin absolu dans un fichier appelé mylib.conf.
    * Dans quel répertoire devez-vous ranger ce fichier ?

    <details>
        <summary>Réponse</summary>
         /etc/ld.so.conf.d
    </details>
        
    * Quelle commande devez-vous exécuter pour que les changements soient pleinement pris en compte ?
    
    <details>
    <summary>Réponse</summary>
            ldconfig
    </details>
    
3. Quelle commande utiliseriez-vous pour recenser les bibliothèques partagées requises par kill ?

<details>
  <summary>Réponse</summary>
    ldd /bin/kill
</details>

### Exercices d’approfondissement

1. objdump est un outil en ligne de commande qui affiche les informations relatives aux fichiers objets. Vérifiez s’il est installé sur votre système avec which objdump. Si ce n’est pas le cas, veuillez l’installer.

    * Utilisez objdump avec l’option p (ou --private-headers) et grep pour afficher les dépendances de glibc :

    <details>
    <summary>Réponse</summary>
        objdump -p /lib/x86_64-linux-gnu/libc.so.6 | grep NEEDED
    </details>

    * Utilisez objdump avec l’option p (ou --private-headers) et grep pour afficher le soname de glibc :

    <details>
    <summary>Réponse</summary>
        objdump -p /lib/x86_64-linux-gnu/libc.so.6 | grep SONAME
    </details>

    * Utilisez objdump avec l’option p (ou --private-headers) et grep pour afficher les dépendances de Bash :

    <details>
    <summary>Réponse</summary>
        objdump -p /bin/bash | grep NEEDED
    </details>

.

.

.

Sources:

[https://www.tecmint.com/understanding-shared-libraries-in-linux/](https://www.tecmint.com/understanding-shared-libraries-in-linux/)

[https://www.ibm.com/developerworks/linux/library/l-dynamic-libraries/](https://www.ibm.com/developerworks/linux/library/l-dynamic-libraries/)

[http://www.linuxforums.org/forum/newbie/151875-what-libraries.html](http://www.linuxforums.org/forum/newbie/151875-what-libraries.html)

[https://www.linuxnix.com/linux-directory-structure-lib-explained/](https://www.linuxnix.com/linux-directory-structure-lib-explained/)

[https://askubuntu.com/questions/17545/where-does-ubuntu-store-its-library-files](https://askubuntu.com/questions/17545/where-does-ubuntu-store-its-library-files)

[https://medium.com/@jhuang1012bn/static-library-and-shared-library-de6def6b1d3b](https://medium.com/@jhuang1012bn/static-library-and-shared-library-de6def6b1d3b)

[http://tldp.org/HOWTO/Program-Library-HOWTO/shared-libraries.html](http://tldp.org/HOWTO/Program-Library-HOWTO/shared-libraries.html)

[https://unix.stackexchange.com/questions/354295/what-is-the-default-value-of-ld-library-path](https://unix.stackexchange.com/questions/354295/what-is-the-default-value-of-ld-library-path)
