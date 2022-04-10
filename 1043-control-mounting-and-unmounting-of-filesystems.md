# 104.3. Montage et démontage des systèmes de fichiers

**Poids:** 3

**Description:** Les candidats doivent être en mesure de configurer le montage d'un système de fichiers. .

**Connaissances clés :**

* Montage et démontage manuel des systèmes de fichiers.
* Configuration du montage des systèmes de fichiers au démarrage du système.
* Configuration des options de montage des systèmes de fichiers.
* Utilisation des étiquettes et UUID pour l'identification et le montage des systèmes de fichier
* Connaissance de base des unités de montage systemd mount units (systemd mount units).

**Concepts et Utilitaires :**

* /etc/fstab
* /media/
* mount
* umount
* blkid
* lsblk

Dans les leçons précédente nous avons appris comment partionner des disques durs et comment formater les systèmes de fichiers sur ces partitions. Dans cette stion nous allons apprendre comment monter et démonter des systèmes de fichier, afin de pouvoir y accéder, stocker et retrouver des fichiers depuis le disque dur.

### Le système de fichier de Linux <a href="linux-filesystems" id="linux-filesystems"></a>

Le système de fichiers de Linux est un grand arbre ayant pour racine /, et nous avons des systèmes de fichiers sur des périphériques différents et des partitions.  Comment résoudre cette incompatibilité apparente ?

 Le système de fichier racince (/) est monté comme partie du processus d'initialisation. Chaque autre système de fichier que vous créer n'est pas utilisable sur le système Linux tant qu'il n'est pas _mounté_ sur un _point de montage_.

## Montage et démontage

 La commande **mount** est utilisé pour monter le système de fichier trouvé sur un périphérique dans la structure du grand arbre(le système de fichier de **Linux**) ayant pour racine ‘**/**‘. Il exsite une autre commande **umount** qui peut être utilisé pour détacher ces périphérique de l'arbre.

### mount

```
mount -t type device location
```

> Généralement l'endroit que nous spécificions est le point de montage. Le point de montage doit existé sinon le périphérique ne peut pas être monté.

Avec l'option `-t` nous pouvons spécifier le type de système de fichier, cependant il peut être omis car la commande mount est assez intelligente pour déterminer le type de système de fichier!:

Créons un dossier appelé "mydisk" comme point de montage et montons /dev/sdb1 dessus:

```
root@ubuntu16-1:/mnt# pwd
/mnt

root@ubuntu16-1:/mnt# mkdir mydisk

root@ubuntu16-1:/mnt# mount -t ext4 /dev/sdb1 /mnt/mydisk

root@ubuntu16-1:/mnt# cd mydisk/

root@ubuntu16-1:~/mydisk# ls
file1.txt  lost+found

root@ubuntu16-1:/mnt/mydisk# cat file1.txt 
 this text file is on my hard disk!

```

{% hint style="info" %}
Lorsque vous monter un système de fichier dans un dossier existant, les fichiers sur le système de fichier que vous monter deviennent les fichiers et sous dossier du point de montage. Si le dossier dupoint de montage contient déjà des fichiers ou des sous-dossier, il ne sont pas perdu, mais ils ne sont plus visible jusqu'à ce que le système de fichiers monté soit démonté, et alors il redeviendront visibles. C'est une bonne idée pour éviter ces problèmes d'utiliser uniquement des dossiers vides comme point de montage.
{% endhint %}

La commande mount sans argument affiche tous les montages sur le système :

```
root@ubuntu16-1:~# mount
sysfs on /sys type sysfs (rw,nosuid,nodev,noexec,relatime)
proc on /proc type proc (rw,nosuid,nodev,noexec,relatime)
udev on /dev type devtmpfs (rw,nosuid,relatime,size=462796k,nr_inodes=115699,mode=755)
devpts on /dev/pts type devpts (rw,nosuid,noexec,relatime,gid=5,mode=620,ptmxmode=000)
tmpfs on /run type tmpfs (rw,nosuid,noexec,relatime,size=98508k,mode=755)
/dev/sda1 on / type ext4 (rw,relatime,errors=remount-ro,data=ordered)
securityfs on /sys/kernel/security type securityfs (rw,nosuid,nodev,noexec,relatime)
tmpfs on /dev/shm type tmpfs (rw,nosuid,nodev)
tmpfs on /run/lock type tmpfs (rw,nosuid,nodev,noexec,relatime,size=5120k)
tmpfs on /sys/fs/cgroup type tmpfs (ro,nosuid,nodev,noexec,mode=755)
cgroup on /sys/fs/cgroup/systemd type cgroup (rw,nosuid,nodev,noexec,relatime,xattr,release_agent=/lib/systemd/systemd-cgroups-agent,name=systemd)
pstore on /sys/fs/pstore type pstore (rw,nosuid,nodev,noexec,relatime)
cgroup on /sys/fs/cgroup/pids type cgroup (rw,nosuid,nodev,noexec,relatime,pids)
cgroup on /sys/fs/cgroup/freezer type cgroup (rw,nosuid,nodev,noexec,relatime,freezer)
cgroup on /sys/fs/cgroup/cpu,cpuacct type cgroup (rw,nosuid,nodev,noexec,relatime,cpu,cpuacct)
cgroup on /sys/fs/cgroup/net_cls,net_prio type cgroup (rw,nosuid,nodev,noexec,relatime,net_cls,net_prio)
cgroup on /sys/fs/cgroup/blkio type cgroup (rw,nosuid,nodev,noexec,relatime,blkio)
cgroup on /sys/fs/cgroup/devices type cgroup (rw,nosuid,nodev,noexec,relatime,devices)
cgroup on /sys/fs/cgroup/hugetlb type cgroup (rw,nosuid,nodev,noexec,relatime,hugetlb)
cgroup on /sys/fs/cgroup/rdma type cgroup (rw,nosuid,nodev,noexec,relatime,rdma)
cgroup on /sys/fs/cgroup/perf_event type cgroup (rw,nosuid,nodev,noexec,relatime,perf_event)
cgroup on /sys/fs/cgroup/cpuset type cgroup (rw,nosuid,nodev,noexec,relatime,cpuset)
cgroup on /sys/fs/cgroup/memory type cgroup (rw,nosuid,nodev,noexec,relatime,memory)
systemd-1 on /proc/sys/fs/binfmt_misc type autofs (rw,relatime,fd=28,pgrp=1,timeout=0,minproto=5,maxproto=5,direct,pipe_ino=19700)
hugetlbfs on /dev/hugepages type hugetlbfs (rw,relatime,pagesize=2M)
mqueue on /dev/mqueue type mqueue (rw,relatime)
debugfs on /sys/kernel/debug type debugfs (rw,relatime)
configfs on /sys/kernel/config type configfs (rw,relatime)
fusectl on /sys/fs/fuse/connections type fusectl (rw,relatime)
binfmt_misc on /proc/sys/fs/binfmt_misc type binfmt_misc (rw,relatime)
vmware-vmblock on /run/vmblock-fuse type fuse.vmware-vmblock (rw,nosuid,nodev,relatime,user_id=0,group_id=0,default_permissions,allow_other)
tmpfs on /run/user/1001 type tmpfs (rw,nosuid,nodev,relatime,size=98508k,mode=700,uid=1001,gid=1001)
gvfsd-fuse on /run/user/1001/gvfs type fuse.gvfsd-fuse (rw,nosuid,nodev,relatime,user_id=1001,group_id=1001)
/dev/sr0 on /media/user1/Fedora-WS-Live-28-1-1 type iso9660 (ro,nosuid,nodev,relatime,nojoliet,check=s,map=n,blocksize=2048,uid=1001,gid=1001,dmode=500,fmode=400,uhelper=udisks2)
/dev/sdb1 on /mnt/mydisk type ext4 (rw,relatime,data=ordered)
```

#### Options de mount 

 La commande `mount` a plusieurs options qui surcharge son comportement par défaut. Par exemple, nous pouvons monter un système de fichier en lecture seule en spécifiant `-o ro`. Si le système de fichiers est déjà monté, ajouter `remount:`

```
root@ubuntu16-1:/mnt# mount -o remount,ro /dev/sdb1 /mnt/mydisk/
root@ubuntu16-1:/mnt# cd /mnt/mydisk/
root@ubuntu16-1:/mnt/mydisk# ls
file1.txt  lost+found
root@ubuntu16-1:/mnt/mydisk# touch file2
touch: cannot touch 'file2': Read-only file system
```

> `notes:`

> * Utiliser des virgules pour séparer les multiples options, comme `remount` et `ro`.
> * Lorsque vous remonté un système de fichier déjà montée, il suffit de spécifier soit le point de montage soit le nom du périphérique. Il n'est pas nécessaire de spécifier les deux.
> * Vous ne pouvez pas monter un système de fichier en lecture seul comme système sur lequel on peut lire et écrire. Les médias qui ne peuvent être modifié, comme les CD-ROM, seront automatiquement monté en lecture seule.
> * Pour remonter un périphérique sur lequel on peut ecrire, spécifier `-o remount,rw`

| mount command examples                 | description                                                                |
| -------------------------------------- | -------------------------------------------------------------------------- |
| mount -l                               | Ajoute les labels ext2, ext3 et XFS sur la sortie du montage.                     |
| mount -r /dev/sdb1 /mydisk             | Monte le système de fichier en lecture seule. Comme -o ro                             |
| mount -r /dev/sdb1 /mydisk             | Monte le système de fichier en lecture/écriture. C'est le cas par défaut. Comme avec -o rw |
| mount -L mydata /mydisk/               | Monte la partition qui a le label spécifié                           |
| mount -U uuid /mydisk                  | Monte la partition qui a l'uuid spécifié                            |
| mount -t iso9660 -o ro /dev/cdrom /mnt | Monte un CD-ROM(juste dans le cas où le système ne l'est pas fait)                            |
| mount -o loop /mydisk.iso /mnt         | Monte une image iso                                                         |

 ` mount -B /mydisk /mysecondplace` : Après être monter dans dossier le point de montage peut être modifié. Nous fournisson le point de montage courant et le nouveau point de montage dans une ligne avec le paramètre `-B`. Cela ne supprime pas l'ancien montage, mais ajoute seulement le nouveau dossier comme montage.

> Tous les montage que nous avons créé ne sont pas permanent et seront supprimé lors du redémarrage du système. Afin de les rendre permanent nous devons modifier le fichier /etc/fstab. 

### umount

Une fois que le système de fichiers est monté, nous pouvons utiliser la commande umount (sans “n”) pour démonter le système de fichiers. Nous pouvons démonter le système de fichier en utilisant umount avec le périphérique ou le point de montage.

```
umount device
OU
umount mount-point
```

>  Noter qu'un système de fichier ne peut pas être démonté lorsqu'il est occupé - par exemple, lorsqu'il y a des fichiers ouverts sur celui-ci, ou lorsque certains processus travaillent dans ces dossier, ou bien lorsqu'un fichier swap sur lui est utilisé.

 Par exemple, démontons /dev/sdb1 :

```
umount /dev/sdb1
```

### /etc/fstab

Dans les sections précédentes à propos des chargeurs de démarrage nous acons appris que, dans les deux cas, GRUB et LILO  root= parameter dit au chargeur de démarrage quelle système de fichier doit être monté comme racine. Pour GRUB2, c'est le paramètre `setroot`. Une fois que le système de fichiers racine est monté, le processus d'initialisation lance `mount` avec l'option `-a` pour monter automatiquement un ensemble de systèmes de fichiers. Cet esemble est déifnit dans le fichier /etc/fstab.

Fstab est le tableau du système d'exploitation des systèmes de fichiers:

```
root@ubuntu16-1:~# cat /etc/fstab 

# /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a
# device; this may be used with UUID= as a more robust way to name devices
# that works even if disks are added and removed. See fstab(5).
#
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
# / was on /dev/sda1 during installation
UUID=142a64e5-96f3-4789-9c91-1dc1570057b7 /               ext4    errors=remount-ro 0       1
# swap was on /dev/sda5 during installation
UUID=b4801c8b-ca75-4548-8697-182d1b6d895c none            swap    sw              0       0
/dev/fd0        /media/floppy0  auto    rw,user,noauto,exec,utf8 0       0
```

*  **file system :** Cema peut être un nom de périphérique commme /dev/sda1, ou un label (LABEL=) ou UUID (UUID=). 

> > Nous pouvons aussi monter des périphéique en utilisant leur nom, labels, ou uuid. Lequel est le meilleur ? N'importe qui peut définir les labels qu'il souhaite. Les nom de périphérique peuent être changer par exemple si vous attacher deux clés USB, donc la meilleur manière est d'utiliser l'uuid pour le montage(utiliser la commande `blkid `).

*  **mount point :** point de montage. Pour l'espace swap, cette valeur doit être ‘none’ ou ‘swap’.
*  **type :** Précise le type de système de fichier. Cela peut être **ext2,3,4, reiserfs,swap, vfat and ntfs,ISO9660,auto**,etc.
* **option** : Précise les options de montage. Précise `defaults` si vous voulez les options par défaut de montage.

{% hint style="info" %}
* sync/async - Tous les I/O du système de fichiers doivent être faite de manière (a)synchrone.
* auto - Le système de fichier peut être monté automatiquement (au démarrage, ou lorsque l'option `-a` est passé à mount). Ce n'est pas nécessaire car c'est l'action par défaut de `mount -a`.
* noauto - Le système de fichier ne sera pas monter automatiquement lors du démarrage ou lorsque l'on passe l'option `-a` à la commande mount. Vous devrez explicitement monté le système de fichier.
* dev/nodev - Interprète / N'interrète pas les caractères ou blocs spéciaux du périphériques sur le système de fichier.
* exec / noexec - Permet/ Empêche l'exécution de binaires depuis le système de fichier.
* suid/nosuid - Permet/ Empêche l'opération de suid, et sgid bits.
* ro - Monter en lecture seule.
* rw - Monter en lecture/écriture read-write.
* user - Permet n'importe quel utilisateur à monter le système de fichiers. Cela implique automatiquement noexec, nosuid,nodev sauf si elles sont surchargés.
* nouser - Permet seulement à root de monter le système de fichier. C'est aussi un paramètre par défaut.
* defaults - Utiliser les paramères par défaut. Equivalent à rw, suid, dev, exec, auto, nouser, async.
* \_netdev - C'est un périphérique réseau, le monter après que le réseau soit connecté. Valide seulement sur les systèmes de fichier de type nfs.
{% endhint %}

> note1: Les systèmes de fichiers montés par l'utilisateu ont par défaut l'option noexec sauf si c'est spécifié après user.
>
> note2: noatime désative l'enregistrement de l'heure d'accès. Cela peut améliorer les performances.

* **dump:** Précise si la commande dump doit être considérer pour les sauvegarde des système de fichier ext2 ou ext3. Une valeur de 0 dit à dump d'ignorer le système de fichier.
* **pass:** Les valeurs différentes de 0 précise l'ordre de vérification des systèmes de fichier lors du démarrage (en se basant sur l'interval de vérification et le compteur de montage, essayez `tune2fs -l` ).

Ajouttons /dev/sdb1 au fichier /etc/fstab en utilisant son UUID et nous aurons accès même après un redémarrage :

```
root@ubuntu16-1:~# blkid
/dev/sdb1: LABEL="mydata" UUID="03c28ca3-daa3-49c2-9bc4-b083c3b0957b" TYPE="ext4" PARTUUID="4d71bc84-01"
/dev/sda1: LABEL="myroot" UUID="142a64e5-96f3-4789-9c91-1dc1570057b7" TYPE="ext4" PARTUUID="101c66bb-01"
/dev/sda5: UUID="b4801c8b-ca75-4548-8697-182d1b6d895c" TYPE="swap" PARTUUID="101c66bb-05"
/dev/sr0: UUID="2018-04-25-06-43-09-00" LABEL="Fedora-WS-Live-28-1-1" TYPE="iso9660" PTUUID="3a663a44" PTTYPE="dos"

root@ubuntu16-1:~# vim /etc/fstab 
```

```
# /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a
# device; this may be used with UUID= as a more robust way to name devices
# that works even if disks are added and removed. See fstab(5).
#
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
# / was on /dev/sda1 during installation
UUID=142a64e5-96f3-4789-9c91-1dc1570057b7 /               ext4    errors=remount-ro 0       1
# swap was on /dev/sda5 during installation
UUID=b4801c8b-ca75-4548-8697-182d1b6d895c none            swap    sw              0       0
/dev/fd0        /media/floppy0  auto    rw,user,noauto,exec,utf8 0       0
### added by root! 
UUID=03c28ca3-daa3-49c2-9bc4-b083c3b0957b /mnt/mydisk  auto default   0 0

```

Si quelque chose ne vas pas dans /etc/fstab , cela peut entraîner un échec du système et le système peut arrêter de démarrer! Donc essayer `mount -a` afin d'essayer les montage des items de fstab avant de redémarrer le système et vérifier les erreurs.

```
root@ubuntu16-1:~# mount -a
mount: wrong fs type, bad option, bad superblock on /dev/sdb1,
       missing codepage or helper program, or other error

       In some cases useful info is found in syslog - try
       dmesg | tail or so.
```

Oups! c'est `defaults` et non `default`. Essayez d'éditer de nouveau cette ligne dans fstab:

```
### added by root! 
UUID=03c28ca3-daa3-49c2-9bc4-b083c3b0957b /mnt/mydisk  auto defaults   0 0
```

Et nous avons terminé :

```
root@ubuntu16-1:~# mount -a
root@ubuntu16-1:~# cd /mnt/mydisk/
root@ubuntu16-1:/mnt/mydisk# ls
file1.txt  file2.txt  lost+found
```

Les utilisateurs peuvent seulement monter et démonter les choses qu'ils sont autorisés dans /etc/fstab!

.

.

.

[https://developer.ibm.com/tutorials/l-lpic1-104-3/](https://developer.ibm.com/tutorials/l-lpic1-104-3/)

[https://www.geeksforgeeks.org/mount-command-in-linux-with-examples/](https://www.geeksforgeeks.org/mount-command-in-linux-with-examples/)

[https://www.sanfoundry.com/mount-command-usage-example/](https://www.sanfoundry.com/mount-command-usage-example/)

[https://www.poftut.com/linux-mount-command-tutorial-examples/](https://www.poftut.com/linux-mount-command-tutorial-examples/)

[https://www.thegeekdiary.com/how-to-mount-and-umount-a-file-system-in-linux/](https://www.thegeekdiary.com/how-to-mount-and-umount-a-file-system-in-linux/)

[https://help.ubuntu.com/community/Fstab](https://help.ubuntu.com/community/Fstab)

[https://linoxide.com/file-system/understanding-each-entry-of-linux-fstab-etcfstab-file/](https://linoxide.com/file-system/understanding-each-entry-of-linux-fstab-etcfstab-file/)

.
