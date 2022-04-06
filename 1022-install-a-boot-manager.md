
# 102.2. Installation d'un gestionnaire de démarrage

## **102.2 Installation d'un gestionnaire de démarrage**

**Poids:** 2

**Description:** Les candidas doivent être capable de sélectionner d'installer et de configurer un gestionnaire de démarrage.

**Connaissances clés:**

* Fournir une alternative à la localisation du démarrage et aux options de sauvegarde du démarrage
* Installer et configurer un gestionnaire de démarrage comme GRUB Legacy
* Effectuer la configuration de base des changements pour GRUB 2
* Interragir avec le gestionnaire de démarrage

**Voici une liste partielle des fichiers utilisés, des concepts et des utilitaires:**

* menu.lst, grub.cfg and grub.conf
* grub-install
* grub-mkconfig
* MBR

Nous avons parlé du processus de démarrage de linux et des chargeurs de démarrage. Nous avons introduit LILO en tant que très vieux système de chargement de démarrage qui a été remplacé par GRUB dans la fin des années 90. Dans ce cours nous allons détailler les chargeurs de démarrage GRUB et GRUB2.

## GRUB

![](.gitbook/assets/Grub_logo.png)![](.gitbook/assets/Grub_logo2.png)

GRUB (GRand Unified Bootloader) est un packet de chargeur de démarrage qui support plusieurs systèmes d'exploitation et permet à l'utilisateur de choisir parmi eux pendant le démarrage.

#### GRUB versions

GRUB a été créé par Erich Stefan Boleyn et a été développé en tant que projet GNU comme GNU GRUB. Le paquet original est encore disponible pour le téléchargement mais n'est plus développé.

**GRUB2** a remplacé ce qui été précédemment connu comme GRUB (c'est-à-dire la version 0.9x), qui est, en retour, devenu **GRUB Legacy.** Des amélioration pour  GRUB2 sont encore faite, mais la version courante publié est utilisable pour des opérations normales.

### Comment fonctionne GRUB?

Lors du démarrage d'un ordinateur, le BIOS transfert le contrôle au premier périphérique de démarrage, qui peut être un disque dur, une disque, un CD-ROM ou n'importe quel autre périphérique reconnu par BIOS.

#### MBR

Le premier secteur d'un disque dur est appelé Master Boot Record (MBR). Ce secteur est long seulement de 512 bytes et contient de petites pièces de code (446 bytes) appelé premier chargeur de démarrage et la table de partitions (64 bytes) décrivant les partitions primaires et étendues.

![](.gitbook/assets/bootloader-mbr.jpg)

GRUB remplace le MBR par défaut avec son propre code:

```
[root@centos7-1 ~]# xxd -l 512 /dev/sda
```

![](.gitbook/assets/mbr_color2.jpg)

Par défaut, le code MCR cherche la partition marqué comme active et une fois qu'une telle partition est trouvé, il charge son secteur de démarrage dans la mémoire et lui passe le contrôle.

```
[root@centos7-1 ~]# fdisk -l /dev/sda 

Disk /dev/sda: 53.7 GB, 53687091200 bytes, 104857600 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x000a1a5f

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048     2099199     1048576   83  Linux
/dev/sda2         2099200   104857599    51379200   8e  Linux LVM
```

En détail, GRUB fonctionne en étapes.

* **Etape 1** est localisé dans le MBR et pointe vers l'étape 2, puisque le MBR est trop petit pour contenir toutes les données nécessaires.
* **Etape 2** pointe sur ses propres fichiers de configuration, qui contienne toute l'interface utilisateur complexe et les options que vous connaissez. L'étape 2 peut être positionné n'importe où sur le disque. Si l'étape 2 ne peut pas trouver son propre tableau de configuration, GRUB arrêtera la séquence de démarrage et propose à l'utilisateur une ligne de commande pour une configuration manuelle.

L'architecture par étape permet à GRUB d'être large (\~20-30K) et pourtant très complexe et hautement configurable, comporé à la plupar des chargeur de démarrage, qui sont éparse et simple pour rentrer dans les limitations de la table de partition.

### GRUB Legacy vs GRUB2

Voyons une vue d'ensemble :

![](.gitbook/assets/bootloader-grubvsgrub2.jpg)

/boot/grub/menu.lst dans GRUB Legacy a été remplacé par /boot/grub/grub.cfg dans GRUB2.

```
### GRUB Legacy
[root@centos5-1 ~]# ls /boot
config-2.6.18-398.el5      message
grub                       symvers-2.6.18-398.el5.gz
initrd-2.6.18-398.el5.img  System.map-2.6.18-398.el5
lost+found                 vmlinuz-2.6.18-398.el5

[root@centos5-1 ~]# ls /boot/grub/
device.map     grub.conf         minix_stage1_5     stage2
e2fs_stage1_5  iso9660_stage1_5  reiserfs_stage1_5  ufs2_stage1_5
fat_stage1_5   jfs_stage1_5      splash.xpm.gz      vstafs_stage1_5
ffs_stage1_5   menu.lst          stage1             xfs_stage1_5
##############################################################
### GRUB2
[root@centos7-1 ~]# ls /boot/
config-3.10.0-693.el7.x86_64
efi
grub
grub2
initramfs-0-rescue-1cf30b938dc94f2bb08fb045c7a0734e.img
initramfs-3.10.0-693.el7.x86_64.img
initramfs-3.10.0-693.el7.x86_64kdump.img
initrd-plymouth.img
symvers-3.10.0-693.el7.x86_64.gz
System.map-3.10.0-693.el7.x86_64
vmlinuz-0-rescue-1cf30b938dc94f2bb08fb045c7a0734e
vmlinuz-3.10.0-693.el7.x86_64
[root@centos7-1 ~]# ls /boot/grub
splash.xpm.gz

[root@centos7-1 ~]# ls /boot/grub2
device.map  fonts  grub.cfg  grubenv  i386-pc  locale
```

Quelle version de linux utilise quel grub ?

![](.gitbook/assets/bootloader-grubdistro.jpg)

**Demonstration**

Afin de voir les différences entre GRUB Legacy et GRUB2, changeons le paramètre `timeout` dans deux systèmes (CentsOS5 and Centos7) :

### menu.lst (GRUB Legacy)

```
[root@centos5-1 ~]# cat /boot/grub/menu.lst 
# grub.conf generated by anaconda
#
# Note that you do not have to rerun grub after making changes to this file
# NOTICE:  You have a /boot partition.  This means that
#          all kernel and initrd paths are relative to /boot/, eg.
#          root (hd0,0)
#          kernel /vmlinuz-version ro root=/dev/sda2
#          initrd /initrd-version.img
#boot=/dev/sda
default=0
timeout=5
splashimage=(hd0,0)/grub/splash.xpm.gz
hiddenmenu
title CentOS (2.6.18-398.el5)
        root (hd0,0)
        kernel /vmlinuz-2.6.18-398.el5 ro root=LABEL=/ rhgb quiet
        initrd /initrd-2.6.18-398.el5.img
```

nous faisons seulement une modification dans menu.lst et enregistrons le fichier.

### grub.cfg (GRUB2)

**grub.cfg** est surchargé par certaines mises à jour de paquet de Grub 2, lorsque un noyau est ajouté ou enlevé, ou lorsque l'utilisateur lance update-grub.**Ne pas modifier grub.cfg directement!**

```
[root@centos7-1 ~]# cat /boot/grub2/grub.cfg 
#
# DO NOT EDIT THIS FILE
#
# It is automatically generated by grub2-mkconfig using templates
# from /etc/grub.d and settings from /etc/default/grub
#

### BEGIN /etc/grub.d/00_header ###
set pager=1

if [ -s $prefix/grubenv ]; then
  load_env
fi
if [ "${next_entry}" ] ; then
   set default="${next_entry}"
   set next_entry=
   save_env next_entry
   set boot_once=true
else
   set default="${saved_entry}"
fi

if [ x"${feature_menuentry_id}" = xy ]; then
  menuentry_id_option="--id"
else
  menuentry_id_option=""
fi

export menuentry_id_option

if [ "${prev_saved_entry}" ]; then
  set saved_entry="${prev_saved_entry}"
  save_env saved_entry
  set prev_saved_entry=
  save_env prev_saved_entry
  set boot_once=true
fi

function savedefault {
  if [ -z "${boot_once}" ]; then
    saved_entry="${chosen}"
    save_env saved_entry
  fi
}

function load_video {
  if [ x$feature_all_video_module = xy ]; then
    insmod all_video
  else
    insmod efi_gop
    insmod efi_uga
    insmod ieee1275_fb
    insmod vbe
    insmod vga
    insmod video_bochs
    insmod video_cirrus
  fi
}

terminal_output console
if [ x$feature_timeout_style = xy ] ; then
  set timeout_style=menu
  set timeout=5
# Fallback normal timeout code in case the timeout_style feature is
# unavailable.
else
  set timeout=5
fi
### END /etc/grub.d/00_header ###

### BEGIN /etc/grub.d/00_tuned ###
set tuned_params=""
set tuned_initrd=""
### END /etc/grub.d/00_tuned ###

### BEGIN /etc/grub.d/01_users ###
if [ -f ${prefix}/user.cfg ]; then
  source ${prefix}/user.cfg
  if [ -n "${GRUB2_PASSWORD}" ]; then
    set superusers="root"
    export superusers
    password_pbkdf2 root ${GRUB2_PASSWORD}
  fi
fi
### END /etc/grub.d/01_users ###

### BEGIN /etc/grub.d/10_linux ###
menuentry 'CentOS Linux (3.10.0-693.el7.x86_64) 7 (Core)' --class centos --class gnu-linux --class gnu --class os --unrestricted $menuentry_id_option 'gnulinux-3.10.0-693.el7.x86_64-advanced-162b8754-3820-4229-9ab4-810de8ab8558' {
    load_video
    set gfxpayload=keep
    insmod gzio
    insmod part_msdos
    insmod xfs
    set root='hd0,msdos1'
    if [ x$feature_platform_search_hint = xy ]; then
      search --no-floppy --fs-uuid --set=root --hint-bios=hd0,msdos1 --hint-efi=hd0,msdos1 --hint-baremetal=ahci0,msdos1 --hint='hd0,msdos1'  985589ee-8d6c-4dbb-9463-90576a5a1ff4
    else
      search --no-floppy --fs-uuid --set=root 985589ee-8d6c-4dbb-9463-90576a5a1ff4
    fi
    linux16 /vmlinuz-3.10.0-693.el7.x86_64 root=/dev/mapper/centos-root ro crashkernel=auto rd.lvm.lv=centos/root rd.lvm.lv=centos/swap rhgb quiet LANG=en_US.UTF-8
    initrd16 /initramfs-3.10.0-693.el7.x86_64.img
}
menuentry 'CentOS Linux (0-rescue-1cf30b938dc94f2bb08fb045c7a0734e) 7 (Core)' --class centos --class gnu-linux --class gnu --class os --unrestricted $menuentry_id_option 'gnulinux-0-rescue-1cf30b938dc94f2bb08fb045c7a0734e-advanced-162b8754-3820-4229-9ab4-810de8ab8558' {
    load_video
    insmod gzio
    insmod part_msdos
    insmod xfs
    set root='hd0,msdos1'
    if [ x$feature_platform_search_hint = xy ]; then
      search --no-floppy --fs-uuid --set=root --hint-bios=hd0,msdos1 --hint-efi=hd0,msdos1 --hint-baremetal=ahci0,msdos1 --hint='hd0,msdos1'  985589ee-8d6c-4dbb-9463-90576a5a1ff4
    else
      search --no-floppy --fs-uuid --set=root 985589ee-8d6c-4dbb-9463-90576a5a1ff4
    fi
    linux16 /vmlinuz-0-rescue-1cf30b938dc94f2bb08fb045c7a0734e root=/dev/mapper/centos-root ro crashkernel=auto rd.lvm.lv=centos/root rd.lvm.lv=centos/swap rhgb quiet
    initrd16 /initramfs-0-rescue-1cf30b938dc94f2bb08fb045c7a0734e.img
}

### END /etc/grub.d/10_linux ###

### BEGIN /etc/grub.d/20_linux_xen ###
### END /etc/grub.d/20_linux_xen ###

### BEGIN /etc/grub.d/20_ppc_terminfo ###
### END /etc/grub.d/20_ppc_terminfo ###

### BEGIN /etc/grub.d/30_os-prober ###
### END /etc/grub.d/30_os-prober ###

### BEGIN /etc/grub.d/40_custom ###
# This file provides an easy way to add custom menu entries.  Simply type the
# menu entries you want to add after this comment.  Be careful not to change
# the 'exec tail' line above.
### END /etc/grub.d/40_custom ###

### BEGIN /etc/grub.d/41_custom ###
if [ -f  ${config_directory}/custom.cfg ]; then
  source ${config_directory}/custom.cfg
elif [ -z "${config_directory}" -a -f  $prefix/custom.cfg ]; then
  source $prefix/custom.cfg;
fi
### END /etc/grub.d/41_custom ###
```

Afin de faire des modifications dans grub.cfg deux étapes sont requises.

### 1-/etc/default/grub

En premier modifier /etc/default/grub:

```
[root@centos7-1 ~]# cat /etc/default/grub
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=centos/root rd.lvm.lv=centos/swap rhgb quiet"
GRUB_DISABLE_RECOVERY="true"
```

Enregistrer les modifications dans /etc/default/grub. Les configurations sont écrite dans /boot/grub2/grub.cfg en utilisant la commande grub-mkconfig

### 2- grub-mkconfig

grub-mkconfig Génère un fichier de configuration GRUB.

```
[root@centos7-1 ~]# grub2-mkconfig 
Generating grub configuration file ...
#
# DO NOT EDIT THIS FILE
#
# It is automatically generated by grub2-mkconfig using templates
# from /etc/grub.d and settings from /etc/default/grub
#

### BEGIN /etc/grub.d/00_header ###
set pager=1

if [ -s $prefix/grubenv ]; then
  load_env
fi
if [ "${next_entry}" ] ; then
   set default="${next_entry}"
   set next_entry=
   save_env next_entry
   set boot_once=true
else
   set default="${saved_entry}"
fi

if [ x"${feature_menuentry_id}" = xy ]; then
  menuentry_id_option="--id"
else
  menuentry_id_option=""
fi

export menuentry_id_option

if [ "${prev_saved_entry}" ]; then
  set saved_entry="${prev_saved_entry}"
  save_env saved_entry
  set prev_saved_entry=
  save_env prev_saved_entry
  set boot_once=true
fi

function savedefault {
  if [ -z "${boot_once}" ]; then
    saved_entry="${chosen}"
    save_env saved_entry
  fi
}

function load_video {
  if [ x$feature_all_video_module = xy ]; then
    insmod all_video
  else
    insmod efi_gop
    insmod efi_uga
    insmod ieee1275_fb
    insmod vbe
    insmod vga
    insmod video_bochs
    insmod video_cirrus
  fi
}

terminal_output console
if [ x$feature_timeout_style = xy ] ; then
  set timeout_style=menu
  set timeout=5
# Fallback normal timeout code in case the timeout_style feature is
# unavailable.
else
  set timeout=5
fi
### END /etc/grub.d/00_header ###

### BEGIN /etc/grub.d/00_tuned ###
set tuned_params=""
set tuned_initrd=""
### END /etc/grub.d/00_tuned ###

### BEGIN /etc/grub.d/01_users ###
if [ -f ${prefix}/user.cfg ]; then
  source ${prefix}/user.cfg
  if [ -n "${GRUB2_PASSWORD}" ]; then
    set superusers="root"
    export superusers
    password_pbkdf2 root ${GRUB2_PASSWORD}
  fi
fi
### END /etc/grub.d/01_users ###

### BEGIN /etc/grub.d/10_linux ###
Found linux image: /boot/vmlinuz-3.10.0-693.el7.x86_64
Found initrd image: /boot/initramfs-3.10.0-693.el7.x86_64.img
menuentry 'CentOS Linux (3.10.0-693.el7.x86_64) 7 (Core)' --class centos --class gnu-linux --class gnu --class os --unrestricted $menuentry_id_option 'gnulinux-3.10.0-693.el7.x86_64-advanced-162b8754-3820-4229-9ab4-810de8ab8558' {
    load_video
    set gfxpayload=keep
    insmod gzio
    insmod part_msdos
    insmod xfs
    set root='hd0,msdos1'
    if [ x$feature_platform_search_hint = xy ]; then
      search --no-floppy --fs-uuid --set=root --hint-bios=hd0,msdos1 --hint-efi=hd0,msdos1 --hint-baremetal=ahci0,msdos1 --hint='hd0,msdos1'  985589ee-8d6c-4dbb-9463-90576a5a1ff4
    else
      search --no-floppy --fs-uuid --set=root 985589ee-8d6c-4dbb-9463-90576a5a1ff4
    fi
    linux16 /vmlinuz-3.10.0-693.el7.x86_64 root=/dev/mapper/centos-root ro crashkernel=auto rd.lvm.lv=centos/root rd.lvm.lv=centos/swap rhgb quiet 
    initrd16 /initramfs-3.10.0-693.el7.x86_64.img
}
Found linux image: /boot/vmlinuz-0-rescue-1cf30b938dc94f2bb08fb045c7a0734e
Found initrd image: /boot/initramfs-0-rescue-1cf30b938dc94f2bb08fb045c7a0734e.img
menuentry 'CentOS Linux (0-rescue-1cf30b938dc94f2bb08fb045c7a0734e) 7 (Core)' --class centos --class gnu-linux --class gnu --class os --unrestricted $menuentry_id_option 'gnulinux-0-rescue-1cf30b938dc94f2bb08fb045c7a0734e-advanced-162b8754-3820-4229-9ab4-810de8ab8558' {
    load_video
    insmod gzio
    insmod part_msdos
    insmod xfs
    set root='hd0,msdos1'
    if [ x$feature_platform_search_hint = xy ]; then
      search --no-floppy --fs-uuid --set=root --hint-bios=hd0,msdos1 --hint-efi=hd0,msdos1 --hint-baremetal=ahci0,msdos1 --hint='hd0,msdos1'  985589ee-8d6c-4dbb-9463-90576a5a1ff4
    else
      search --no-floppy --fs-uuid --set=root 985589ee-8d6c-4dbb-9463-90576a5a1ff4
    fi
    linux16 /vmlinuz-0-rescue-1cf30b938dc94f2bb08fb045c7a0734e root=/dev/mapper/centos-root ro crashkernel=auto rd.lvm.lv=centos/root rd.lvm.lv=centos/swap rhgb quiet 
    initrd16 /initramfs-0-rescue-1cf30b938dc94f2bb08fb045c7a0734e.img
}
if [ "x$default" = 'CentOS Linux (3.10.0-693.el7.x86_64) 7 (Core)' ]; then default='Advanced options for CentOS Linux>CentOS Linux (3.10.0-693.el7.x86_64) 7 (Core)'; fi;
### END /etc/grub.d/10_linux ###

### BEGIN /etc/grub.d/20_linux_xen ###
### END /etc/grub.d/20_linux_xen ###

### BEGIN /etc/grub.d/20_ppc_terminfo ###
### END /etc/grub.d/20_ppc_terminfo ###

### BEGIN /etc/grub.d/30_os-prober ###
### END /etc/grub.d/30_os-prober ###

### BEGIN /etc/grub.d/40_custom ###
# This file provides an easy way to add custom menu entries.  Simply type the
# menu entries you want to add after this comment.  Be careful not to change
# the 'exec tail' line above.
### END /etc/grub.d/40_custom ###

### BEGIN /etc/grub.d/41_custom ###
if [ -f  ${config_directory}/custom.cfg ]; then
  source ${config_directory}/custom.cfg
elif [ -z "${config_directory}" -a -f  $prefix/custom.cfg ]; then
  source $prefix/custom.cfg;
fi
### END /etc/grub.d/41_custom ###
done
```

Il lit les fichiers script principal de grub depuis  **/etc/grub.d** et les paramètres depuis  **/etc/default/grub** et affiche la configuration générée.

```
[root@centos7-1 ~]# ls /etc/grub.d/
00_header  01_users  20_linux_xen     30_os-prober  41_custom
00_tuned   10_linux  20_ppc_terminfo  40_custom     README
```

Options de grub2-mkconfig:

```
[root@centos7-1 ~]# grub2-mkconfig --help
Usage: grub2-mkconfig [OPTION]
Generate a grub config file

  -o, --output=FILE       output generated config to FILE [default=stdout]
  -h, --help              print this message and exit
  -v, --version           print the version information and exit

Report bugs to <bug-grub@gnu.org>.
```

Pour enregistrer la configuration généré nous utilisons l'option `-o` (il est recommandé de faire une sauvegarde du fichier `/boot/grub2/grub.cfg`, même si cela ne touche pas le Master Boot Record et modifie seulement le menu GRUB):

```
[root@centos7-1 ~]# grub2-mkconfig -o /boot/grub2/grub.cfg 
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-3.10.0-693.el7.x86_64
Found initrd image: /boot/initramfs-3.10.0-693.el7.x86_64.img
Found linux image: /boot/vmlinuz-0-rescue-1cf30b938dc94f2bb08fb045c7a0734e
Found initrd image: /boot/initramfs-0-rescue-1cf30b938dc94f2bb08fb045c7a0734e.img
done
```

Nous pouvons aussi utiliser la commande  `grub2-mkconfig > /boot/grub2/grub.cfg`,et l'étape de configuration de  GRUB2 s'arrête ici.

#### grub-update

Lorsque vous lancer la commande update-grub, GRUB combine automatiquement les paramètres du fichier /etc/default/grub, les scripts du dossier /etc/grub.d/ , e tout le reste, créant un fichier /boot/grub/grub.cfg qui est lu lors du démarrage.

### GRUB Interfaces

Il y a 3 interfaces dans GRUB qui fournisse différents niveau de fonctionnalités. Le noyau Linux peut être démarré par les utilisateurs avec l'aide de ces interfaces. Les détails de ces interfaces sont :

**1-Menu Interface**

GRUB est configuré par le programme d'installation dans l'interface de menu. C'est l'inerface disponible par défaut. Il contient une liste des système d'exploitation ou des noyau linux trié par nom. Un système d'exploitation spécifique ou un noyau peut être selectionné en utilisant les flèches du clabier et peut être démarrer en utilisant la touche entrée.

GRUB Legacy

![](.gitbook/assets/bootloader-grub1menu.jpg)

GRUB2

![](.gitbook/assets/bootloader-grub2menu.jpg)


**2-Menu Entry Editor Interface**

La touche e key (une touche pour GRUB Legacy) dans le menu de chargement du démarrage est utilisé pour accéder à l'éditeur d'entrée de menu. Toutes les commandes GRUB pour une entrée de menu particulière sont affichées et ces commandes peuvent être modifiées avant de charger le système d'exploitation.

GRUB Legacy

![](.gitbook/assets/bootloader-grub1menuent.jpg)

GRUB2

![](.gitbook/assets/bootloader-grub2menuent.jpg)

**3-Command Line Interface**

Cette inerface est la plus basique des interface de  GRUB mail il permet plus de contrôle pour l'utilisateur. En utilisant l'interface ligne de commande, toute commande peut être exécutée en la tapant et en appuyant sur entrée. Cette interface fournit aussi des fonctionnalités avancées de shel.

GRUB Legacy

![](.gitbook/assets/bootloader-grub1menucmd.jpg)

Il est possible d'avoir une invie de commande de GRUB Legacy qui s'affiche après le redémarrage d'un système pour obtenir des informations.

```
[root@centos5-1 ~]# grub
Probing devices to guess BIOS drives. This may take a long time.


    GNU GRUB  version 0.97  (640K lower / 3072K upper memory)

 [ Minimal BASH-like line editing is supported.  For the first word, TAB
   lists possible command completions.  Anywhere else TAB lists the possible
   completions of a device/filename.]
```

GRUB2

![](.gitbook/assets/bootloader-grub2menucmd.jpg)

{% hint style="info" %}
Note: La numérotation des partitions a changé dans GRUB2. La première partition est 1 dans GRUB2 plutôt que 0 (GRUB Legacy). Le premier périphérique/disque est toujours hd0 par défaut (pas de changement).
{% endhint %}

### Installation de GRUB

Normalement lorsque nous installons un système GRUB est installé pour nous Normally when we setup a system GRUB is installed for us but there are some cases which we might need to install GRUB ourselves. There are different ways to install GRUB.

If we are on old system with GRUB legacy we can run grub-install command or using setup command in GRUB shell.

```
[root@centos5-1 ~]# grub-install /dev/sdb
```

If we are on GRUB2 we can use one of GRUB2 utilities.

```
[root@centos7-1 ~]# grub2-
grub2-bios-setup           grub2-mkconfig             grub2-ofpathname
grub2-editenv              grub2-mkfont               grub2-probe
grub2-file                 grub2-mkimage              grub2-reboot
grub2-fstest               grub2-mklayout             grub2-rpm-sort
grub2-get-kernel-settings  grub2-mknetdir             grub2-script-check
grub2-glue-efi             grub2-mkpasswd-pbkdf2      grub2-set-default
grub2-install              grub2-mkrelpath            grub2-setpassword
grub2-kbdcomp              grub2-mkrescue             grub2-sparc64-setup
grub2-menulst2cfg          grub2-mkstandalone         grub2-syslinux2cfg
```

### grub2-install

grub-install installs GRUB onto a device. This includes copying GRUB images into the target directory (generally /boot/grub), and on some platforms may also include installing GRUB onto a boot sector.

```
[root@centos7-1 ~]# grub2-install --help
Usage: grub2-install [OPTION...] [OPTION] [INSTALL_DEVICE]
Install GRUB on your drive.

      --compress[=no,xz,gz,lzo]   compress GRUB files [optional]
  -d, --directory=DIR        use images and modules under DIR
                             [default=/usr/lib/grub/<platform>]
      --fonts=FONTS          install FONTS [default=unicode]
      --install-modules=MODULES   install only MODULES and their dependencies
                             [default=all]
  -k, --pubkey=FILE          embed FILE as public key for signature checking
      --locale-directory=DIR use translations under DIR
                             [default=/usr/share/locale]
      --locales=LOCALES      install only LOCALES [default=all]
      --modules=MODULES      pre-load specified modules MODULES
      --themes=THEMES        install THEMES [default=starfield]
  -v, --verbose              print verbose messages.
      --allow-floppy         make the drive also bootable as floppy (default
                             for fdX devices). May break on some BIOSes.
      --boot-directory=DIR   install GRUB images under the directory DIR/grub2
                             instead of the boot/grub2 directory
      --bootloader-id=ID     the ID of bootloader. This option is only
                             available on EFI and Macs.
      --core-compress=xz|none|auto
                             choose the compression to use for core image
      --disk-module=MODULE   disk module to use (biosdisk or native). This
                             option is only available on BIOS target.
      --efi-directory=DIR    use DIR as the EFI System Partition root.
      --force                install even if problems are detected
      --force-file-id        use identifier file even if UUID is available
      --label-bgcolor=COLOR  use COLOR for label background
      --label-color=COLOR    use COLOR for label
      --label-font=FILE      use FILE as font for label
      --macppc-directory=DIR use DIR for PPC MAC install.
      --no-bootsector        do not install bootsector
      --no-nvram             don't update the `boot-device'/`Boot*' NVRAM
                             variables. This option is only available on EFI
                             and IEEE1275 targets.
      --no-rs-codes          Do not apply any reed-solomon codes when
                             embedding core.img. This option is only available
                             on x86 BIOS targets.
      --product-version=STRING   use STRING as product version
      --recheck              delete device map if it already exists
      --removable            the installation device is removable. This option
                             is only available on EFI.
  -s, --skip-fs-probe        do not probe for filesystems in DEVICE
      --target=TARGET        install GRUB for TARGET platform
                             [default=i386-pc]; available targets: arm-efi,
                             arm-uboot, arm64-efi, i386-coreboot, i386-efi,
                             i386-ieee1275, i386-multiboot, i386-pc,
                             i386-qemu, i386-xen, ia64-efi, mips-arc,
                             mips-qemu_mips, mipsel-arc, mipsel-loongson,
                             mipsel-qemu_mips, powerpc-ieee1275,
                             sparc64-ieee1275, x86_64-efi, x86_64-xen
  -?, --help                 give this help list
      --usage                give a short usage message
  -V, --version              print program version

Mandatory or optional arguments to long options are also mandatory or optional
for any corresponding short options.

INSTALL_DEVICE must be system device filename.
grub2-install copies GRUB images into boot/grub2.  On some platforms, it may
also install GRUB into the boot sector.

Report bugs to <bug-grub@gnu.org>.
```

as an example `grub2-install /dev/sdb` will install grub on sdb device, to the master boot record of my hard disk.

How ever instaling GRUB a from running system seems cool, but most of the time there is a problem during boot process and we can not get into our system any more, so we need to reinstall GRUB from GRUB shell. Unfortunately the setup command has been removed from GRUB2 shell and it would need more efforts. We need to bring up the system in rescue mode using a live cd and then install GRUB on our hard disk using current temporary root system file:

{% hint style="danger" %}
it is just an example and do not run it because you would mess up your current system!
{% endhint %}

`grub2-install --boot-directory=/tmp/root/boot /dev/sda`



.

.

.

Sources:

[https://en.wikipedia.org/wiki/GNU_GRUB](https://en.wikipedia.org/wiki/GNU_GRUB)

[https://whatis.techtarget.com/definition/GRUB-GRand-Unified-Bootloader](https://whatis.techtarget.com/definition/GRUB-GRand-Unified-Bootloader)

[https://www.gnu.org/software/grub/](https://www.gnu.org/software/grub/)

[https://www.dedoimedo.com/computers/grub.html](https://www.dedoimedo.com/computers/grub.html)

[https://www.tutorialspoint.com/what-is-grub-in-linux](https://www.tutorialspoint.com/what-is-grub-in-linux)

[https://help.ubuntu.com/community/Grub2](https://help.ubuntu.com/community/Grub2)

[https://www.mankier.com/8/grub2-install](https://www.mankier.com/8/grub2-install)

.
