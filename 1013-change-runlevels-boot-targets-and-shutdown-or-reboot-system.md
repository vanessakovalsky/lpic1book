# 101.3. Changer le niveau d'exécution / les cibles de démarrage et arrêter ou redémarrer le système

## **101.3 Changer le niveau d'exécution / les cibles de démarrage et arrêter ou redémarrer le système**

**Poids:**3

**Description:** Les candidats devrait être capble de gérer le niveau d'exécution SysVinit ou la cible de démarrage de systemd sur le système. Cet objectif inclut le changement pour un mode utilisateur unique, l'arrêt ou le redémarrage du système. Les candidats devrait être capable d'alerter les utilisateurs avant de modifier les niveaux d'éxecution et les cibles de démarrage et d'arrêter les processus proprement. Cet objectif inclue aussi le paramètrage du niveau d'exécution par défaut de  SysVinit ou de la cible de démarrage de systemd. Il inclut également une sensibilisation sur Upstart en tant qu'alternative à SysVinit ou systemd.

**Connaissances clés:**

* Définir le niveau d'exécution par défaut et la cible de démarrage
* Changer entre niveau d'exécution / cible de démarrage incluant le mode utilisateur unique
* Arrêt et redémarrage depuis la ligne de commande
* Prévenir les utilisateurs avant de modifier le niveau d'exécution / les cibles de démarrage ou tout autre évènement majeur du système
* Arrêter proprement un processus

**Concepts et Utilitaires:**

* /etc/inittab
* shutdown
* init
* /etc/init.d/
* telinit
* systemd
* systemctl
* /etc/systemd/
* /usr/lib/systemd/
* wall

Dans la leçon précédente nous avons expliqué la séquence de démarrage du système dans Linux, puis nous avons introduit Sysv, Upstart et systemd comme gestionnaires de services différents dans le monde linux. Dans cette leçon nous apprendront comment prendre le contrôle des services de notre système Linux en utilisant ces gestionnaires de service.

Commençons avec SysV. SysV n'est rien de plus que de nombreux scripts exécutable qui sont lancer après le processus init. Comment pouvons nous définir quel service doit être lancé lorsque l'ordinateur démarre ? Ravi que vous le demandiez. Il semble que des fichiers de configurations volumineux soient nécessiare, c'est ici que les  **runlevels** (niveau d'éxecution) entrent en jeu.

### runlevels

Un niveau d'exécution est un des mode que les systèmes d'exploitation Unix utilisent. Chaque niveau d'exécution a un certain nombre de services arrêtés ou démarrés, donnant le contrôle à l'utilisatuer sur le comportement de la machine. Les runlevels évite d'avoir des fichiers volumineux à éditer à la main.

Par convention, **seven** niveaux d'exécution existent, numéroté de 0 à 6. Et il y a des différentes entre les systèmes de type Debian et les système de type RedHat:

| Runlevel | Debian               | RedHat                       |
| -------- | -------------------- | ---------------------------- |
| **0**    | **Halt**             | **Halt**                     |
| **1**    | **Single User Mode** | **Single User Mode**         |
| **2**    | Full,Multi-User,GUI  | Multi-User, No Net           |
| **3**    | Nothing              | Multi-User, with Net, No GUI |
| **4**    | **Nothing**          | **Not used**                 |
| **5**    | Nothing              | Full,Multi-User,GUI          |
| **6**    | **Reboot**           | **Reboot**                   |

CentsOS 5 a été la dernière version a utilisé SysV et depuis CentOS 7 Systemd est utilisé.

### runlevel

Pour trouver le niveau courant et le précéden niveau d'exécution, la commande runlevel est utilisé :

```
[root@centos5-1 ~]# runlevel
N 5
```

Dans la sortie ci-dessus, la lettre ‘N’ indique quel niveau d'exécution n'a pas été modifié depuis que le système a démarré. Et 5 est le niveau d'exécution courant.

Maintenant que nous savons ce que sont les niveaux d'exécution, comment passer de l'un à l'autre ? il suffit de dire à  'init' quel niveau d'exécution nous voulons.

## telinit

telinit est utilisé pour changer le niveau d'exécution système de SysV.

```
telinit  RUNLEVEL
```

Essayons sur une machine CentOS5:

```
[root@centos5-1 ~]# runlevel
N 5
[root@centos5-1 ~]# telinit 3
```

et le système passe au niveau d'exécution 3 puis revenons au niveau d'exécution 5:

```
[root@centos5-1 ~]# telinit 5
```

## init

init (en tant que commande) est un processus de contrôle de l'initialisation comme telinit.

```
[root@centos5-1 ~]# init 3
```

{% hint style="info" %}
#### telinit vs init

telinit est un outil plus petit qui informe init lorsque cela est nécessaire de changer le niveau d'exécution. Donc nous pouvons utiliser "telinit" pour "dire à init" que nous avons besoin de changer de niveau d'exécution. telinit est actuellement lié à la commande init et il est posible d'utiliser la commande init à la place mais cela n'est pas recommandé.

```
[root@centos5-1 ~]# ls -l /sbin/telinit 
lrwxrwxrwx 1 root root 4 Aug 26 23:20 /sbin/telinit -> init
```
{% endhint %}

Il y a différente manière de modifier le niveau d'exécution. Pour rendre la modification permanent, nous pouvons éditer **/etc/inittab** et modifier le niveau par défaut avec celui que nous avons vu ci-dessus.

### /etc/inttab

Après que le noyau Linux a démarré, le programme init lit le fichier /etc/inittab pour déterminer le  **comportement de chaque niveau d'exécution**. Sauf si l'utilisateur spécifie une autre valeur comme paramètre de démarrage du noyau, le system essayera d'entrer le niveau d'exécution par défaut.(CentOs5)

```
[root@centos5-1 ~]# cat /etc/inittab 
#
# inittab       This file describes how the INIT process should set up
#               the system in a certain run-level.
#
# Author:       Miquel van Smoorenburg, <miquels@drinkel.nl.mugnet.org>
#               Modified for RHS Linux by Marc Ewing and Donnie Barnes
#

# Default runlevel. The runlevels used by RHS are:
#   0 - halt (Do NOT set initdefault to this)
#   1 - Single user mode
#   2 - Multiuser, without NFS (The same as 3, if you do not have networking)
#   3 - Full multiuser mode
#   4 - unused
#   5 - X11
#   6 - reboot (Do NOT set initdefault to this)
# 
id:5:initdefault:

# System initialization.
si::sysinit:/etc/rc.d/rc.sysinit

l0:0:wait:/etc/rc.d/rc 0
l1:1:wait:/etc/rc.d/rc 1
l2:2:wait:/etc/rc.d/rc 2
l3:3:wait:/etc/rc.d/rc 3
l4:4:wait:/etc/rc.d/rc 4
l5:5:wait:/etc/rc.d/rc 5
l6:6:wait:/etc/rc.d/rc 6

# Trap CTRL-ALT-DELETE
ca::ctrlaltdel:/sbin/shutdown -t3 -r now

# When our UPS tells us power has failed, assume we have a few minutes
# of power left.  Schedule a shutdown for 2 minutes from now.
# This does, of course, assume you have powerd installed and your
# UPS connected and working correctly.  
pf::powerfail:/sbin/shutdown -f -h +2 "Power Failure; System Shutting Down"

# If power was restored before the shutdown kicked in, cancel it.
pr:12345:powerokwait:/sbin/shutdown -c "Power Restored; Shutdown Cancelled"


# Run gettys in standard runlevels
1:2345:respawn:/sbin/mingetty tty1
2:2345:respawn:/sbin/mingetty tty2
3:2345:respawn:/sbin/mingetty tty3
4:2345:respawn:/sbin/mingetty tty4
5:2345:respawn:/sbin/mingetty tty5
6:2345:respawn:/sbin/mingetty tty6

# Run xdm in runlevel 5
x:5:respawn:/etc/X11/prefdm -nodaemon
```

Le **niveau d'exécution par défaut** est déterminé depuis l'entrée `id:`dans **/etc/inittab**. Comment les niveaux d'exécution sont définis par défaut et comment ils sont configurés dépendent en partie de la distribution particulière que vous exécuter.

Le format de chaque ligne du fichier inittab file est le suivant:

`id:runlevel:action:process`

Voici une description de ces champs :

* **id (identification code)** – consiste en une séquence de un à quatre caractères qui identifie sa fonction.
* **runlevels** – liste les niveaux d'exécution sur lesquels l'entrée s'applique.
* **action** – code spécifique dans ce champ dit à init comment traiter le processus. Les valeurs possibles incluent: initdefault, sysinit, boot, bootwait, wait, et respawn.
* **process** – définit la commande ou le script à exécuter.

Voyons maintenant comment SysV implémente le concept de niveau d'exécution.

### /etc/init.d

/etc/init.d contient des script utilisé par l'outil init de  System V  (SysVinit).

```
[root@centos5-1 ~]# ls  /etc/init.d/
acpid               hidd           netconsole       rpcidmapd
anacron             httpd          netfs            rpcsvcgssd
atd                 ip6tables      netplugd         saslauthd
auditd              ipmi           network          sendmail
autofs              iptables       NetworkManager   single
avahi-daemon        irda           nfs              smartd
avahi-dnsconfd      irqbalance     nfslock          sshd
bluetooth           iscsi          nscd             svnserve
conman              iscsid         ntpd             syslog
cpuspeed            killall        oddjobd          vmware-tools
crond               krb524         pand             vmware-tools-thinprint
cups                kudzu          pcscd            vncserver
cups-config-daemon  ldap           portmap          wdaemon
dnsmasq             lvm2-monitor   psacct           wpa_supplicant
dund                mcstrans       rawdevices       xfs
firstboot           mdmonitor      rdisc            ypbind
functions           mdmpd          readahead_early  yum-updatesd
gpm                 messagebus     readahead_later
haldaemon           microcode_ctl  restorecond
halt                multipathd     rpcgssd
```

Comme nous disions, dans SysV, le programme init program est le premier processus qui est lancé et par conséquence certains services d'infrastructures sont démarrés. Les fichiers dans /etc/init.d sont des scripts shell qui réponde aux commandes  start, stop, restart, et (lorsque c'est supporté) pour gérer un service particulier. Mais comment SysV détermine quel services dans  /etc/init.d devrait être démarré ou arrété en fonction du niveau d'exécution ? Faisons un schéma :

![](.gitbook/assets/customizsysv-SysVScripts.jpg)

### /etc/rc.d/

SysV utilises des groupes. **Les scripts de chaque niveau d'exécution sont regroupés et placés dans  /etc/rc{runlevel}.d/** où runlevel est le niveau d'exécution. 

Comme de nombreux services doivent existés dans différents niveaux d'exécution, les fichiers réels des scripts sont stockés dans  /etc/init.d et /etc/rc{runlevel}.d/ pointe seulement sur ceux dont il a besoin.

```
[root@centos5-1 etc]# ls | grep rc.
rc0.d
rc1.d
rc2.d
rc3.d
rc4.d
rc5.d
rc6.d
rc.d
rc.local
rc.sysinit
```

Lets take a look at rc.5 for example:

```
[root@centos5-1 etc]# ls -l rc5.d/
total 288
lrwxrwxrwx 1 root root 17 Aug 26 23:21 K01dnsmasq -> ../init.d/dnsmasq
lrwxrwxrwx 1 root root 24 Aug 26 23:21 K02avahi-dnsconfd -> ../init.d/avahi-dnsconfd
lrwxrwxrwx 1 root root 24 Aug 26 23:23 K02NetworkManager -> ../init.d/NetworkManager
lrwxrwxrwx 1 root root 17 Aug 26 23:23 K02oddjobd -> ../init.d/oddjobd
lrwxrwxrwx 1 root root 16 Aug 26 23:20 K05conman -> ../init.d/conman
lrwxrwxrwx 1 root root 19 Aug 26 23:21 K05saslauthd -> ../init.d/saslauthd
lrwxrwxrwx 1 root root 17 Aug 26 23:20 K05wdaemon -> ../init.d/wdaemon
lrwxrwxrwx 1 root root 16 Aug 26 23:20 K10psacct -> ../init.d/psacct
lrwxrwxrwx 1 root root 15 Aug 26 23:21 K15httpd -> ../init.d/httpd
lrwxrwxrwx 1 root root 18 Aug 26 23:22 K15svnserve -> ../init.d/svnserve
lrwxrwxrwx 1 root root 13 Aug 26 23:23 K20nfs -> ../init.d/nfs
lrwxrwxrwx 1 root root 14 Aug 26 23:23 K24irda -> ../init.d/irda
<output has been truncated>
lrwxrwxrwx 1 root root 23 Aug 26 23:20 S00microcode_ctl -> ../init.d/microcode_ctl
lrwxrwxrwx 1 root root 22 Aug 26 23:28 S03vmware-tools -> ../init.d/vmware-tools
lrwxrwxrwx 1 root root 25 Aug 26 23:20 S04readahead_early -> ../init.d/readahead_early
lrwxrwxrwx 1 root root 15 Aug 26 23:23 S05kudzu -> ../init.d/kudzu
lrwxrwxrwx 1 root root 16 Aug 26 23:20 S07iscsid -> ../init.d/iscsid
lrwxrwxrwx 1 root root 19 Aug 26 23:19 S08ip6tables -> ../init.d/ip6tables
lrwxrwxrwx 1 root root 18 Aug 26 23:19 S08iptables -> ../init.d/iptables
lrwxrwxrwx 1 root root 18 Aug 26 23:21 S08mcstrans -> ../init.d/mcstrans
lrwxrwxrwx 1 root root 17 Aug 26 23:21 S10network -> ../init.d/network
lrwxrwxrwx 1 root root 16 Aug 26 23:20 S11auditd -> ../init.d/auditd
lrwxrwxrwx 1 root root 21 Aug 26 23:21 S12restorecond -> ../init.d/restorecond
lrwxrwxrwx 1 root root 16 Aug 26 23:21 S12syslog -> ../init.d/syslog
lrwxrwxrwx 1 root root 18 Aug 26 23:19 S13cpuspeed -> ../init.d/cpuspeed
lrwxrwxrwx 1 root root 20 Aug 26 23:21 S13irqbalance -> ../init.d/irqbalance
lrwxrwxrwx 1 root root 15 Aug 26 23:20 S13iscsi -> ../init.d/iscsi
lrwxrwxrwx 1 root root 17 Aug 26 23:21 S13portmap -> ../init.d/portmap
lrwxrwxrwx 1 root root 17 Aug 26 23:23 S14nfslock -> ../init.d/nfslock
lrwxrwxrwx 1 root root 19 Aug 26 23:21 S15mdmonitor -> ../init.d/mdmonitor
<output has been truncated>
```

Chaque script dans chaque niveau d'exécution est lancé avec les fonction de démarrage ou d'arrêt en fonction du niveau d'exécution qui est lancé..** S** signifie script de **démarrage(starting)** et **K** montre que c'est un script pour **arrêter(killing)** . La séquence des actions est définit par les numéros. Essayer la commande cat pour voir ce qu'il y a à l'intérieur :

```
[root@centos5-1 rc5.d]# cat S55sshd
#!/bin/bash
#
# Init file for OpenSSH server daemon
#
# chkconfig: 2345 55 25
# description: OpenSSH server daemon
#
# processname: sshd
# config: /etc/ssh/ssh_host_key
# config: /etc/ssh/ssh_host_key.pub
# config: /etc/ssh/ssh_random_seed
# config: /etc/ssh/sshd_config
# pidfile: /var/run/sshd.pid

# source function library
. /etc/rc.d/init.d/functions

# pull in sysconfig settings
[ -f /etc/sysconfig/sshd ] && . /etc/sysconfig/sshd

RETVAL=0
prog="sshd"

# Some functions to make the below more readable
KEYGEN=/usr/bin/ssh-keygen
SSHD=/usr/sbin/sshd
RSA1_KEY=/etc/ssh/ssh_host_key
RSA_KEY=/etc/ssh/ssh_host_rsa_key
DSA_KEY=/etc/ssh/ssh_host_dsa_key
PID_FILE=/var/run/sshd.pid

runlevel=$(set -- $(runlevel); eval "echo \$$#" )

do_rsa1_keygen() {
        if [ ! -s $RSA1_KEY ]; then
                echo -n $"Generating SSH1 RSA host key: "
                rm -f $RSA1_KEY
                if $KEYGEN -q -t rsa1 -f $RSA1_KEY -C '' -N '' >&/dev/null; then
                        chmod 600 $RSA1_KEY
                        chmod 644 $RSA1_KEY.pub
                        if [ -x /sbin/restorecon ]; then
                            /sbin/restorecon $RSA1_KEY.pub
                        fi
                        success $"RSA1 key generation"
                        echo
                else
                        failure $"RSA1 key generation"
                        echo
                        exit 1
                fi
        fi
}

do_rsa_keygen() {
        if [ ! -s $RSA_KEY ]; then
                echo -n $"Generating SSH2 RSA host key: "
                rm -f $RSA_KEY
                if $KEYGEN -q -t rsa -f $RSA_KEY -C '' -N '' >&/dev/null; then
                        chmod 600 $RSA_KEY
                        chmod 644 $RSA_KEY.pub
                        if [ -x /sbin/restorecon ]; then
                            /sbin/restorecon $RSA_KEY.pub
                        fi
                        success $"RSA key generation"
                        echo
                else
                        failure $"RSA key generation"
                        echo
                        exit 1
                fi
        fi
}

do_dsa_keygen() {
        if [ ! -s $DSA_KEY ]; then
                echo -n $"Generating SSH2 DSA host key: "
                rm -f $DSA_KEY
                if $KEYGEN -q -t dsa -f $DSA_KEY -C '' -N '' >&/dev/null; then
                        chmod 600 $DSA_KEY
                        chmod 644 $DSA_KEY.pub
                        if [ -x /sbin/restorecon ]; then
                            /sbin/restorecon $DSA_KEY.pub
                        fi
                        success $"DSA key generation"
                        echo
                else
                        failure $"DSA key generation"
                        echo
                        exit 1
                fi
        fi
}

do_restart_sanity_check()
{
        $SSHD -t
        RETVAL=$?
        if [ ! "$RETVAL" = 0 ]; then
                failure $"Configuration file or keys are invalid"
                echo
        fi
}

start()
{
        # Create keys if necessary
        do_rsa1_keygen
        do_rsa_keygen
        do_dsa_keygen

        cp -af /etc/localtime /var/empty/sshd/etc

        echo -n $"Starting $prog: "
        $SSHD $OPTIONS && success || failure
        RETVAL=$?
        [ "$RETVAL" = 0 ] && touch /var/lock/subsys/sshd
        echo
}

stop()
{
        echo -n $"Stopping $prog: "
        if [ -n "`pidfileofproc $SSHD`" ] ; then
            killproc $SSHD
        else
            failure $"Stopping $prog"
        fi
        RETVAL=$?
        # if we are in halt or reboot runlevel kill all running sessions
        # so the TCP connections are closed cleanly
        if [ "x$runlevel" = x0 -o "x$runlevel" = x6 ] ; then
            killall $prog 2>/dev/null
        fi
        [ "$RETVAL" = 0 ] && rm -f /var/lock/subsys/sshd
        echo
}

reload()
{
        echo -n $"Reloading $prog: "
        if [ -n "`pidfileofproc $SSHD`" ] ; then
            killproc $SSHD -HUP
        else
            failure $"Reloading $prog"
        fi
        RETVAL=$?
        echo
}

case "$1" in
        start)
                start
                ;;
        stop)
                stop
                ;;
        restart)
                stop
                start
                ;;
        reload)
                reload
                ;;
        condrestart)
                if [ -f /var/lock/subsys/sshd ] ; then
                        do_restart_sanity_check
                        if [ "$RETVAL" = 0 ] ; then
                                stop
                                # avoid race
                                sleep 3
                                start
                        fi
                fi
                ;;
        status)
                status -p $PID_FILE openssh-daemon
                RETVAL=$?
                ;;
        *)
                echo $"Usage: $0 {start|stop|restart|reload|condrestart|status}"
                RETVAL=1
esac
exit $RETVAL
```

Et tous ces espaces sont des espaces que SysV utilise pour gérer ces scripts et ces niveaux d'exécution. Dans le système Sysv nous pouvons gérer les services avec la commande `service` et la commande `chkconfig` pour définir comment et quand les services sont démarrer. Nous en parlerons plus tard.

{% hint style="info" %}
**rc.local**

Qu'en est t'il de  /etc/rc.local ? Ce fichier se lance après tous les autres scripts de niveau d'init se soient exécutée, donc il est plus sûr de mettre des commandes avec lesquels vous avez des problèmes dans ce fichier. C'est aussi un bon ecdroit pour mettre les script de "debugage". **Mais n'oubliez pas rc.local ne devrait pas fonctionner correctement dans Upstart et Systemd.** Tester le, faites vos recherches et faites la configuration requise avant de l'utiliser dans les environnement de production.
{% endhint %}

Revenons à notre sujet, et voyons l'équivalent des niveaux d'exécution dans systemd. Bien que /etc/inittab existe encore dans les systèmes Systemd il ne fait pas partie de la configuration, nous avons quelque chose appelé "Cibles".

### Les cibles systemd 

Comme les niveaux d'exécution il y a des modes dans les systèmes systemd avec lesquels notre système peut s'exécuter, les niveaux d'exécution de systemd sont appelés "**cibles(targetsç**". Les "**cibles**" **sont décrites comme une collection de services**. Voyons les équivalences:** **

```
   ┌─────────┬───────────────────┐
   │Runlevel │ Target            │
   ├─────────┼───────────────────┤
   │0        │ poweroff.target   │
   ├─────────┼───────────────────┤
   │1        │ rescue.target     │
   ├─────────┼───────────────────┤
   │2, 3, 4  │ multi-user.target │
   ├─────────┼───────────────────┤
   │5        │ graphical.target  │
   ├─────────┼───────────────────┤
   │6        │ reboot.target     │
   └─────────┴───────────────────┘
```

Afin de naviguer entre les cibles de démarrage (Boot Targets) nous utilisons l'outil systemctl.

### systemctl

Systemctl est un utilitaire de systemd qui est responsable du controle du système systemd et de la gestion des services. Il peut faire beaucoup de chose mais ce dont nous avons besoin ici est de changer les cibles de demarrage.

Pour voir la cible de démarrage courante utiliser la commande `systemctl get-default`:

```
[root@centos7-1 ~]# systemctl get-default 
graphical.target
```

Pour changer la cible utiliser la commande `systemctl isolate xxxxx.target` , par exemple:

```
[root@centos7-1 ~]# systemctl isolate rescue.target
```

```
[root@centos7-1 ~]# systemctl isolate multi-user.target
```

Pour définir la cible par défaut, lancer la commande `systemctl set-default xxxxxx.target` :

```
[root@centos7-1 ~]# systemctl set-default graphical.target 
Removed symlink /etc/systemd/system/default.target.
Created symlink from /etc/systemd/system/default.target to /usr/lib/systemd/system/graphical.target.
```

Mais comment systemd connait ce qu'il doit faire et comment faire les choses ? ? Nous avons quelque chose appelé les "**Unit**". **Le concept de "Unit Files" remplaces les scripts d'init de SysV pour les services.**

### systemd unit files

Il y a différents types de fichiers unit et la meilleur moyen de décrire un fichier unit est  **'c'est une chose qui doit être démarré'**.Oui c'est une chose car il y a différents type de fichiers unit. **Chaque fichier unit est un fichier texte simple qui décrit une unit, ce qu'il fait, ce qu'il a besoin de lancer avant ou après, et d'autres détails**

Les fichiers unit peuvent être stocké dans quelques endroits différents sur votre système. systemd cherche pour les fichiers unit dans cet ordre :

1. **/etc/systemd/system:**dossier qui stocke les fichiers unit qui étende un service. Ce dossier aura la préséance sur parmi les fichiers unit positionnés n'importe où ailleurs sur le système.
2. **/run/systemd/system:**dossier qui est celui utilisé pendant l'exécution pour les fichiers unit.
3. **/usr/lib/systemd/system:**dossier qui est le dossier par défaut où les fichier unit sont installés par les paquets. Les fichiers unit du dossier par défaut ne doivent pas être modifié.

**Les fichier Unit lu en premier écrase ceux qui sont lu après.** Regardons cela de plus près :

### 1./etc/systemd/system

```
[root@centos7-1 ~]# ls -l /etc/systemd/system
total 4
drwxr-xr-x. 2 root root   31 Oct 28  2017 basic.target.wants
drwxr-xr-x. 2 root root   31 Oct 28  2017 bluetooth.target.wants
lrwxrwxrwx. 1 root root   41 Oct 28  2017 dbus-org.bluez.service -> /usr/lib/systemd/system/bluetooth.service
lrwxrwxrwx. 1 root root   41 Oct 28  2017 dbus-org.fedoraproject.FirewallD1.service -> /usr/lib/systemd/system/firewalld.service
lrwxrwxrwx. 1 root root   44 Oct 28  2017 dbus-org.freedesktop.Avahi.service -> /usr/lib/systemd/system/avahi-daemon.service
lrwxrwxrwx. 1 root root   44 Oct 28  2017 dbus-org.freedesktop.ModemManager1.service -> /usr/lib/systemd/system/ModemManager.service
lrwxrwxrwx. 1 root root   46 Oct 28  2017 dbus-org.freedesktop.NetworkManager.service -> /usr/lib/systemd/system/NetworkManager.service
lrwxrwxrwx. 1 root root   57 Oct 28  2017 dbus-org.freedesktop.nm-dispatcher.service -> /usr/lib/systemd/system/NetworkManager-dispatcher.service
lrwxrwxrwx. 1 root root   36 Oct 28  2017 default.target -> /lib/systemd/system/graphical.target
drwxr-xr-x. 2 root root   87 Oct 28  2017 default.target.wants
drwxr-xr-x. 2 root root   38 Oct 28  2017 dev-virtio\x2dports-org.qemu.guest_agent.0.device.wants
lrwxrwxrwx. 1 root root   35 Oct 28  2017 display-manager.service -> /usr/lib/systemd/system/gdm.service
drwxr-xr-x. 2 root root   32 Oct 28  2017 getty.target.wants
drwxr-xr-x. 2 root root   65 Oct 28  2017 graphical.target.wants
drwxr-xr-x. 2 root root 4096 Oct 28  2017 multi-user.target.wants
drwxr-xr-x. 2 root root   26 Oct 28  2017 printer.target.wants
drwxr-xr-x. 2 root root   31 Oct 28  2017 remote-fs.target.wants
drwxr-xr-x. 2 root root  188 Oct 28  2017 sockets.target.wants
drwxr-xr-x. 2 root root   36 Oct 28  2017 spice-vdagentd.target.wants
drwxr-xr-x. 2 root root   26 Oct 28  2017 sssd.service.d
drwxr-xr-x. 2 root root  169 Oct 28  2017 sysinit.target.wants
drwxr-xr-x. 2 root root   44 Oct 28  2017 system-update.target.wants
drwxr-xr-x. 2 root root   29 Oct 28  2017 vmtoolsd.service.requires
```

### 2./run/systemd/system 

```
[root@centos7-1 ~]# ls -l /run/systemd/system/
total 8
-rw-r--r-- 1 root root  17 Jan  2  2019 session-1.scope
drwxr-xr-x 2 root root 160 Jan  2  2019 session-1.scope.d
-rw-r--r-- 1 root root  17 Jan  2  2019 user-1000.slice
drwxr-xr-x 2 root root 120 Jan  2  2019 user-1000.slice.d
```

### 3./usr/lib/systemd/system

```
[root@centos7-1 ~]# ls  /usr/lib/systemd/system

<output has been trancuated>


cryptsetup-pre.target                    runlevel0.target
cryptsetup.target                        runlevel1.target
ctrl-alt-del.target                      runlevel1.target.wants
cups-browsed.service                     runlevel2.target
cups.path                                runlevel2.target.wants
cups.service                             runlevel3.target
cups.socket                              runlevel3.target.wants
dbus-org.freedesktop.hostname1.service   runlevel4.target
dbus-org.freedesktop.import1.service     runlevel4.target.wants
dbus-org.freedesktop.locale1.service     runlevel5.target
dbus-org.freedesktop.login1.service      runlevel5.target.wants
dbus-org.freedesktop.machine1.service    runlevel6.target

<output has been trancuated>

nfs-rquotad.service                      umount.target
nfs-secure.service                       upower.service
nfs-server.service                       usb_modeswitch@.service
nfs.service                              usbmuxd.service
nfs-utils.service                        user.slice
nss-lookup.target                        var-lib-nfs-rpc_pipefs.mount
nss-user-lookup.target                   vgauthd.service
ntpdate.service                          virt-guest-shutdown.target
ntpd.service                             virtlockd.service
numad.service                            virtlockd.socket
oddjobd.service                          virtlogd.service
packagekit-offline-update.service        virtlogd.socket
packagekit.service                       vmtoolsd.service
paths.target                             wacom-inputattach@.service
plymouth-halt.service                    wpa_supplicant.service
plymouth-kexec.service                   zram.service
plymouth-poweroff.service
```

Suivons un des fichiers unit de cible. Essayez `ls -al *.target` pour voir tous les fichiers cibles. Comme une instance  _default.target_:

```
[root@centos7-1 ~]# ls -al /usr/lib/systemd/system/default.target
lrwxrwxrwx. 1 root root 16 Oct 28  2017 /usr/lib/systemd/system/default.target -> graphical.target
```

Voyons pour la cible _graphical.target_:

```
[root@centos7-1 ~]# cat  /usr/lib/systemd/system/graphical.target
#  This file is part of systemd.
#
#  systemd is free software; you can redistribute it and/or modify it
#  under the terms of the GNU Lesser General Public License as published by
#  the Free Software Foundation; either version 2.1 of the License, or
#  (at your option) any later version.

[Unit]
Description=Graphical Interface
Documentation=man:systemd.special(7)
Requires=multi-user.target
Wants=display-manager.service
Conflicts=rescue.service rescue.target
After=multi-user.target rescue.service rescue.target display-manager.service
AllowIsolate=yes
```

Et ce qui est requis pour _multi-user.target_ :

```
[root@centos7-1 ~]# cat  /usr/lib/systemd/system/multi-user.target
#  This file is part of systemd.
#
#  systemd is free software; you can redistribute it and/or modify it
#  under the terms of the GNU Lesser General Public License as published by
#  the Free Software Foundation; either version 2.1 of the License, or
#  (at your option) any later version.

[Unit]
Description=Multi-User System
Documentation=man:systemd.special(7)
Requires=basic.target
Conflicts=rescue.service rescue.target
After=basic.target rescue.service rescue.target
AllowIsolate=yes
```

et ceux qui ont besoin de _basic.target _:

```
[root@centos7-1 ~]# cat  /usr/lib/systemd/system/basic.target
#  This file is part of systemd.
#
#  systemd is free software; you can redistribute it and/or modify it
#  under the terms of the GNU Lesser General Public License as published by
#  the Free Software Foundation; either version 2.1 of the License, or
#  (at your option) any later version.

[Unit]
Description=Basic System
Documentation=man:systemd.special(7)

Requires=sysinit.target
After=sysinit.target
Wants=sockets.target timers.target paths.target slices.target
After=sockets.target paths.target slices.target
```

Enfin _sysinit.target_ :

```
[root@centos7-1 ~]# cat  /usr/lib/systemd/system/sysinit.target
#  This file is part of systemd.
#
#  systemd is free software; you can redistribute it and/or modify it
#  under the terms of the GNU Lesser General Public License as published by
#  the Free Software Foundation; either version 2.1 of the License, or
#  (at your option) any later version.

[Unit]
Description=System Initialization
Documentation=man:systemd.special(7)
Conflicts=emergency.service emergency.target
Wants=local-fs.target swap.target
After=local-fs.target swap.target emergency.service emergency.target
```

Et _sysint.target_ n'a besoin de rien. Il semble que nous avions atteint le niveau le plus haut de cible de notre arbre.

Utiliser les fichiers unit avec le concepts de Cibles rend Systemd plus flexible en comparaison avec SysV.

## wall

Certaines fois plusieurs utilisateurs sont connecter sur un serveur, et nous avons besoin de leur dire que nous allons redémarrer le serveur pour des tâches de maintenance. Bien sur, la **manière correcte est de les informer tous **ceux qui sont connecté à propos de cette activité de maintenance.

wall (un abreviation pour write to all) est un utilitaire en ligne de commande Unix qui affiche un fichier ou une entrée standard à tous les utilisateurs connectés. Il est typiquement utilisé par root pour envoer un message en cas d'arrêt juste avant l'arrêt
.(Ubuntu16)

```
wall [-n] [-t timeout] [-g group] [message | file]
```

```
OPTIONS         
       -n, --nobanner
              Suppress the banner.

       -t, --timeout timeout
              Abandon the write attempt to the terminals after timeout
              seconds.  This timeout must be a positive integer.  The
              default value is 300 seconds, which is a legacy from the time
              when people ran terminals over modem lines.

       -g, --group group
              Limit printing message to members of group defined as a group
              argument.  The argument can be group name or GID.

       -V, --version
              Display version information and exit.

       -h, --help
              Display help text and exit.
```

Essayons d'envoyer un message "we are going down" :

```
[root@centos5-1 ~]# wall "we are going down"

Broadcast message from root@centos6-1 (pts/1) (Sun Nov 11 00:46:58 2018):

we are going down
```

et voici ce que **user1** recevra:

```
[user1@centos5-1 ~]$ 
Broadcast message from root@centos6-1 (pts/1) (Sun Nov 11 00:46:58 2018):

we are going down
```

Les options pourraient ne pas fonctionner dans certaines distributions anciennes.

## shutdown

shutdown - Arrêt, arrêt complet ou redémarrage de la machine

```
shutdown [OPTIONS...] [TIME] [WALL...]
```

```
OPTIONS         
       The following options are understood:

       --help
           Print a short help text and exit.

       -H, --halt
           Halt the machine.

       -P, --poweroff
           Power-off the machine (the default).

       -r, --reboot
           Reboot the machine.

       -h
           Equivalent to --poweroff, unless --halt is specified.

       -k
           Do not halt, power-off, reboot, just write wall message.

       --no-wall
           Do not send wall message before halt, power-off, reboot.

       -c
           Cancel a pending shutdown. This may be used cancel the effect of
           an invocation of shutdown with a time argument that is not "+0"
           or "now".
       -t sec
           Tell init to wait sec seconds between sending processes the warning 
           and the kill signal, before changing to another runlevel.
[TIME]
           The time argument specifies when to perform the shutdown operation.

[WALL]        
           A message to be sent to all users, along with the standard shutdown notification.
```

shutdown fait son travail en signalant le processus init, en lui demandant de changer de niveau d'exécution. Conformément au titre précédent, le niveau d'exécution 0 est utilisé pour arrêter le système, le niveau d'exécution 6 est utilisé pour redémarrer le système, et le niveau d'exécution 1 est utilisé pour mettre le système dans un état où les tâches d'administrations peuvent être faite (mode utilisateur unique) .

```
root@ubuntu16-1:~# shutdown -r -t 15 +5 "Server will restart in 5 minutes. Please save your work."
Shutdown scheduled for Sun 2018-11-11 03:19:56 PST, use 'shutdown -c' to cancel.
root@ubuntu16-1:~# shutdown -c
```

Nous pouvons spécifier une chaine temps (qui est généralement “now” ou “hh:mm” pour heures/minutes):

```
root@ubuntu16-1:~# shutdown -r 10:10 "Server will be rebooted at 10:10am"
Shutdown scheduled for Tue 2018-11-13 10:10:00 PST, use 'shutdown -c' to cancel.
root@ubuntu16-1:~# shutdown -c
```

####

{% hint style="info" %}
#### halt vs poweroff ! it's a bit historical

* halt a été utilisé avant  ACPI (Advanced Configuration and Power Interface)qui s'occupe pour nous aujourd'hui d'éteindre le courant. Il arrêtait le système et affichait un message  "it's ok to power off now". Il faut revenir au temps où les boutons était physique avec on/off, plutôt que ceux les boutons contrôlé par ACPI que l'on connait aujourd'hui.
* poweroff, arrêtait naturellement le système et appelait la fonction ACPI power off.

De nos jour hat est suffisament intelligent pour automatiquement appeler poweroff si ACPI est activé. En fait, ils sont fonctionnellement équivalent maintenant..
{% endhint %}

### reboot

La commande reboot peut être utilisé pour arrêter ou redémarrer linux.

```
[root@centos7-1 ~]# reboot --help
reboot [OPTIONS...] [ARG]

Reboot the system.

     --help      Show this help
     --halt      Halt the machine
  -p --poweroff  Switch off the machine
     --reboot    Reboot the machine
  -f --force     Force immediate halt/power-off/reboot
  -w --wtmp-only Don't halt/power-off/reboot, just write wtmp record
  -d --no-wtmp   Don't write wtmp record
     --no-wall   Don't send wall message before halt/power-off/reboot
```

Pour redémarrer linux il suffit d'appeler la commande reboot directement sans aucune option.

```
[root@centos7-1 ~]# reboot
```

Cela effectuera un arrêt propre et un redémarrage de la machine. C'est ce qui se passe lorsque l'on clique sur Restart dans le menu.

L'option `-f` forcera le redémarrage de la machine. C'est similaire à appuyer sur le bouton power de l'ordinateur. Il n'y a pas d'arrêt, le système rédémarre instantannément.

### halt

La prochaine commande est la commande halt. Elle peut éteindre un système mais a aussi d'autres options :

```
[root@centos7-1 ~]# halt --help
halt [OPTIONS...]

Halt the system.

     --help      Show this help
     --halt      Halt the machine
  -p --poweroff  Switch off the machine
     --reboot    Reboot the machine
  -f --force     Force immediate halt/power-off/reboot
  -w --wtmp-only Don't halt/power-off/reboot, just write wtmp record
  -d --no-wtmp   Don't write wtmp record
     --no-wall   Don't send wall message before halt/power-off/reboot
```

La commande halt a aussi une option force mais essayer de ne pas l'utiliser, car cela pourrait mettre votre système dans un état inconsistant.

### poweroff

Il y a une autre commande identique à la commande halt. Elle fait la même chose et prend les mêmes options.

```
[root@centos7-1 ~]# poweroff --help
poweroff [OPTIONS...]

Power off the system.

     --help      Show this help
     --halt      Halt the machine
  -p --poweroff  Switch off the machine
     --reboot    Reboot the machine
  -f --force     Force immediate halt/power-off/reboot
  -w --wtmp-only Don't halt/power-off/reboot, just write wtmp record
  -d --no-wtmp   Don't write wtmp record
     --no-wall   Don't send wall message before halt/power-off/reboot
```

Et nous avons terminé.

.

.

.



Sources:

[https://www.linuxjournal.com/article/1274](https://www.linuxjournal.com/article/1274)

[https://en.wikipedia.org/wiki/Runlevel](https://en.wikipedia.org/wiki/Runlevel)

[https://www.geeksforgeeks.org/run-levels-linux/](https://www.geeksforgeeks.org/run-levels-linux/)

[https://www.ostechnix.com/check-runlevel-linux/](https://www.ostechnix.com/check-runlevel-linux/)

[https://geek-university.com/linux/etc-inittab/](https://geek-university.com/linux/etc-inittab/)``[https://developer.ibm.com/tutorials/l-lpic1-101-3/](https://developer.ibm.com/tutorials/l-lpic1-101-3/)

[https://www.liquidweb.com/kb/linux-runlevels-explained/](https://www.liquidweb.com/kb/linux-runlevels-explained/)

[https://linux.die.net/man/8/telinit](https://linux.die.net/man/8/telinit)

[https://unix.stackexchange.com/questions/434560/what-differences-it-will-make-if-i-use-telinit-6-instead-of-reboot-command](https://unix.stackexchange.com/questions/434560/what-differences-it-will-make-if-i-use-telinit-6-instead-of-reboot-command)

[https://askubuntu.com/questions/5039/what-is-the-difference-between-etc-init-and-etc-init-d](https://askubuntu.com/questions/5039/what-is-the-difference-between-etc-init-and-etc-init-d)

[https://www.systutorials.com/239880/change-systemd-boot-target-linux/](https://www.systutorials.com/239880/change-systemd-boot-target-linux/)

[https://www.tecmint.com/change-runlevels-targets-in-systemd/](https://www.tecmint.com/change-runlevels-targets-in-systemd/)

[https://fedoramagazine.org/systemd-getting-a-grip-on-units/](https://fedoramagazine.org/systemd-getting-a-grip-on-units/)

[https://www.linode.com/docs/quick-answers/linux-essentials/what-is-systemd/](https://www.linode.com/docs/quick-answers/linux-essentials/what-is-systemd/)

[https://www.howtoforge.com/linux-wall-command/](https://www.howtoforge.com/linux-wall-command/)

[https://en.wikipedia.org/wiki/Wall\_(Unix)](https://en.wikipedia.org/wiki/Wall_\(Unix\))

[http://man7.org/linux/man-pages/man1/wall.1.html](http://man7.org/linux/man-pages/man1/wall.1.html)

[http://man7.org/linux/man-pages/man8/shutdown.8.html](https://legacy.gitbook.com/book/borosan/lpic1-exam-guide/edit#)

[https://www.computerhope.com/unix/ushutdow.htm](https://www.computerhope.com/unix/ushutdow.htm)

[https://unix.stackexchange.com/questions/42572/is-halt-the-same-as-shutdown-h-and-poweroff-the-same-as-shutdown-p/42581](https://unix.stackexchange.com/questions/42572/is-halt-the-same-as-shutdown-h-and-poweroff-the-same-as-shutdown-p/42581)

[https://www.tecmint.com/shutdown-poweroff-halt-and-reboot-commands-in-linux/](https://www.tecmint.com/shutdown-poweroff-halt-and-reboot-commands-in-linux/)

[https://www.binarytides.com/linux-command-shutdown-reboot-restart-system/](https://www.binarytides.com/linux-command-shutdown-reboot-restart-system/)
