# 103.1. Travailler en ligne de commande

### **103.1 Travailler en ligne de commande**

**Poids**: 4

**Description:** Les candidats doivent être capable d'interragir avec shells et les commande en utilisant la ligne de commande. L'objectif s'appuie sur le shell Bash.

**Connaissances clés:**

* Utiliser des commandes shell seule et des séquences de commandes sur une ligne pour effectuer des tâches basiques avec la ligne de commande
* Utiliser et modifier l'environnement du shell, incluant la définition, le référencement et l'export de variables d'environnement
* Utiliser et modifier l'historique des commandes
* Appelé des commandes dans et en dehors d'un chemin défini

**Concepts et Utilitaires:**

* bash
* echo
* env
* export
* pwd
* set
* unset
* man
* uname
* history
* .bash_history

**Qu'est ce qu'un "Terminal?"**

C'est un programme appelé émulateur de terminal. C'est un programme qui oubre une fenêter qui vous laisse interragir avec le **shell**. Il y a de nombreux émulateurs de terminal différent que nous pouvons utiliser. La plupart des distributions Linux en fournissent plusieurs comme : gnome-terminal, konsole, xterm, **... ,** Il est aussin appelé Console.

**Qu'est ce que "le Shell"?**

De manière simple, le shell est un programme qui reçoit des commandes depuis le clavier et les donnes à effectuer au système d'exploitation. Dans le passé, c'était la seule interface disponible pour les utilisateurs sur les système de type Unix comme Linux. Aujourd'hui, nous avons des interfaces utilisateurs graphiques (GUI) en plus des interface en ligne de commande (CLI) comme le shell.

![](.gitbook/assets/commandintro-shell.jpg)

**Qu'est ce que le "bash"?**

Sur la plupart des systèmes Linux, un programme appelé **bash agit comme le programme shell**. Bash signifie **Bourne Again SHell**, une version améliorée du programme shell Unix Original, sh, écrit par Steve Bourne.

De plus, il y a d'autres programmes shell qui peuvent être installé sur un système Linux. Cela inclut : ksh, tcsh and zsh.

```
[root@centos7-1 ~]# ls -ls /bin/sh
0 lrwxrwxrwx. 1 root root 4 Oct 28  2017 /bin/sh -> bash
```

note: Historically the original /bin/sh Bourne shell would use **$** as the normal prompt and **# **for the root user prompt This made it pretty easy to tell if you were running as superuser or not.

```
[root@centos7-1 ~]# 
[root@centos7-1 ~]# su - user1
Last login: Mon Dec 10 11:44:47 EST 2018 on pts/0
[user1@centos7-1 ~]$ 
[user1@centos7-1 ~]$ su - root
Password: 
Last login: Wed Dec 12 14:34:32 EST 2018 on pts/0
[root@centos7-1 ~]# 
```

**Entrée standard, sortie standard, et erreur standard**

En général, une commande (un programme):

* Reçoit des données à traiter de l'entrée standard (standard input) ou **stdin** (par défaut: clavier).
* Renvoit els données traités sur la sortie standard (standard output) ou **stdout** (par défaut: écran).
* Si l'exécution du programme a causer des erreurs, les messages d'erreurs sont envoyé à l'erreur standard (standard error) ou  **stderr** (par défaut : écran).

![](.gitbook/assets/commandintro-InputOutput.jpg)

### echo

echo est une des plus communes et largement utilisé des commandes fournis du bash Linux et des shells C, qui utilise un langage de scripte et traite les fichiers pour affier une  ligne de texte/chaine de caractère sur la sortie standard ou dans un fichier.

La syntaxe pour echo est: `echo [option(s)] [string(s)]`

exemple : Entrée une ligne de texte et l'afficher sur la sortie standard :

```
[root@centos7-1 ~]# echo  Lets start learning Linux
Lets start learning Linux
```

La commande echo a plusieurs options. Normalement, echo ajoute un caractère de nouvelle ligne à la sortie. Utiliser l'option `-n` pour le supprimer :

```
[root@centos7-1 ~]# echo -n Lets start learning Linux
Lets start learning Linux[root@centos7-1 ~]# 
```

#### Caractères spéciaux ou méta caractères

Qu'est ce qui rend un caractère spécial ? Il a un sens derrière son sens littéral, un meta-sens, qui fait qu'on se réfère à lui comme caractère spécial ou méta-caractère comme : **``| & ; ( ) < > [ ] { } * ! ? ` ' " $ \ / # ``**

**pound sign (#)**

Tout ce qui est écrit après un signe dièse (#) est ignoré par le shell. C'est utile pour écrire des commentaire shell, mais n'a aucune influence sur l'éxécution des commandes.

```
[root@centos7-1 temp]# ls # ls /etc
zip-3.0-11.el7.x86_64.rpm  zip.cpio
```

**Antislash en fin de ligne \\**

Les lignes qui se termine par un antislash (backslash) sont continué sur la ligne suivante. Le shelle n'interprète pas le caractère de nouvelle ligne et attent la suite et l'exécution de la ligne de commande jusqu'à ce qu'une nouvelle ligne sans anti-slash soit rencontrée.

```
[root@centos7-1 ~]# echo first line second line \
> 2nd line \
> 3rd line
first line second line 2nd line 3rd line
```

**Echappement des caractère spéciaux**

Le caractère anti-slash permet l'utilisation de caractère de contrôle, sans que shell les interprèté, c'est ce qu'on appelle **l'échappement de caractère**.

```
[root@centos7-1 ~]# echo hello | by
bash: by: command not found...
[root@centos7-1 ~]# echo hello \| by
hello | by
[root@centos7-1 ~]# echo hello ; by
hello
bash: by: command not found...
[root@centos7-1 ~]# echo hello \; by
hello ; by
```

{% hint style="info" %}
 **Guillemets doubles(Double quotes) " "**

Entourer des caractères dans des guillemets doubles (`"`)  préserve la valeur litéral de chaque caractère dans les guillemets, \[_a l'exception de `$`, `` ` ``, `\`, et, lorsque l'expansion de l'historique est activé, `! `_ ._Les caractères `$` et `` ` `` garde leur sens spécial au sin des guillemets doubles`].`

```
[root@centos7-1 ~]# echo "hello | by"
hello | by 
[root@centos7-1 ~]# echo "hello ; by" 
hello ; by
```
{% endhint %}

Regardons les autres options de la commande echo:

```
       -n     do not output the trailing newline
       -e     enable interpretation of backslash escapes
       -E     disable interpretation of backslash escapes (default)
       --help display this help and exit
       --version
              output version information and exit

       If -e is in effect, the following sequences are recognized:
               \b     backspace
               \\     backslash
               \n     new line
               \r     carriage return
               \t     horizontal tab
               \v     vertical tab
```

Utiliser l'option`-e` pour activer certain **caractères echappées** avec un antislash pour qu'ils aient un sens spécial.

| Escape sequence | Function                                               |
| --------------- | ------------------------------------------------------ |
| \a              | Alert (bell)                                           |
| \b              | Backspace                                              |
| \c              | Suppress trailing newline (same function as -n option) |
| \f              | Form feed (clear the screen on a video display)        |
| \n              | New line                                               |
| \r              | Carriage return                                        |
| \t              | Horizontal tab                                         |
| \v              | vertical tab                                           |

Quelques exemples:

```
[root@centos7-1 ~]# echo -e "Lets \vstart \vlearning \vLinux"
Lets 
     start 
           learning 
                    Linux
[root@centos7-1 ~]# echo -e "Lets \tstart \tlearning \tLinux"
Lets     start     learning     Linux
```

Un des usage de la commande echo est d'obtenir la valeurs des variables avec la commande `echo $VARIABLENAME`. Nous verrons cela.

```
[root@centos7-1 ~]# echo $LD_LIBRARY_PATH

```

### **Opérateurs de contrôles**

Certains méta-caractères ou pair de méta-caractère serve aussi comme opérateurs de contrôle: **`|| && & ; ;; | ()`**

Certain de ces opérateurs de contrôle vous permete de créer des séquence ou des listes de commandes.

#### Enchainement de commandes Linux

Parfois vous voudrez lancer de multiple commande en cascade ou en simultané. Cela nous permet d'automatiser un processus qui inclu plusieurs commandes et peut ou pas dépendre du résultat de la commande précédente.

**Enchainement de commandes Linux** est la méthode de combinaisons de plusieurs commandes différentes de sorite à ce que chacune soit exécuter en cascade en s'appuyant sur l'opérateur qui les sépare. La partie importante de l'enchainement est l'opérateur que nous utilisons pour les combiner. Ces opératers détermine comment la commande s'exécute.

Voyons les opérateurs les plus utilisés pour combiner différentes commandes :

**Point-virgule(Semi-Colon)** (**;**) : Les commandes se succédant seront exécuter sans tenir compte sur statut de sortie de la commande qui le précède.

```
[root@centos7-1 ~]# ls /root/ ; ls /home/
anaconda-ks.cfg              mydb.ldif                      usr
htop-2.2.0-1.el7.x86_64.rpm  set.txt                        zip-3.0-11.el7.x86_64.rpm
initial-setup-ks.cfg         telnet-0.17-64.el7.x86_64.rpm  zip.cpio
managersou.ldif              temp
ldapadm  pabros  payam  user1  user2
```

**Et logique (Logical AND)** (**&&**) : La commande qui suit cette opérateur sera exécutée seulement si la commande précédente a été exécutée avec succès.

```
[root@centos7-1 ~]# true && ls /home/
ldapadm  pabros  payam  user1  user2
```

**Ou logique (Logical OR)** (**||**) : La commande qui suit sera exécutée seulement si celle-qui la précède a échoué.

```
[root@centos7-1 ~]# false || ls /home/
ldapadm  pabros  payam  user1  user2
```

## Variables d'environnement

Les variables d'environnement sont des variables spéciales qui sont défini dans shell et sont nécessaire aux programmes pendant leur exécution. Elles peuvent être définies par le système ou définies par l'utilisateur.

Les variables définies par le sysème sont celle qui sont crée par le système et utilisée par les programmes au niveau du système. 

Le concept complet de définir et supprimer des variables d'environnement tourne autour de certains ensemble de fichiers et quelques command et des différents shells.

Une variable d'environnement peut être de trois types :

**1. Variable d'environnement Local** : Défini pour la session courante. Ces variables d'environnement resteront uniquement dans la session courante, que ce soit une session de connexion à distance, ou une session locale du terminal. Ces variables ne sont pas définis dans des fichiers de configuration et sont créés, et suprimés en utilisant un ensemble de commande.

**2. Variable d'environnement de l'utilisateur** : Ce sont les variables qui sont définis pour un utilisateur spécifique et qui sont chargés à chaque fois qu'un utilisateur se connecte dans une session de terminal local ou dans une session de connexion à distance. Ces variables sont définis et chargés depuis des fichiers de configuration qui sont présent dans le dossier home de l'utilisateur.

**3. variables d'environnement système** : Ce sont les variables d'environnement qui sont disponible du côté du système, c'est à dire, pour tous les utilisateurs présents sur ce système. Ces variablees sont présente dans des fichiers de configuration du système dans les dossier /etc et des fichiers. Ces variables sont chargés à chaque fois que le système est démarré ou qu'une connexion a lieu qu'elle soit locale ou distante.

### env

Par éfautl, la commande "env" liste toutes variables d'environnement courante.

```
[root@centos7-1 ~]# env
XDG_VTNR=1
XDG_SESSION_ID=1
HOSTNAME=centos7-1
SHELL=/bin/bash
TERM=xterm-256color
HISTSIZE=1000
USER=root
LS_COLORS=rs=0:di=38;5;27:ln=38;5;51:mh=44;38;5;15:pi=40;38;5;11:so=38;5;13:do=38;5;5:bd=48;5;232;38;5;11:cd=48;5;232;38;5;3:or=48;5;232;38;5;9:mi=05;48;5;232;38;5;15:su=48;5;196;38;5;15:sg=48;5;11;38;5;16:ca=48;5;196;38;5;226:tw=48;5;10;38;5;16:ow=48;5;10;38;5;21:st=48;5;21;38;5;15:ex=38;5;34:*.tar=38;5;9:*.tgz=38;5;9:*.arc=38;5;9:*.arj=38;5;9:*.taz=38;5;9:*.lha=38;5;9:*.lz4=38;5;9:*.lzh=38;5;9:*.lzma=38;5;9:*.tlz=38;5;9:*.txz=38;5;9:*.tzo=38;5;9:*.t7z=38;5;9:*.zip=38;5;9:*.z=38;5;9:*.Z=38;5;9:*.dz=38;5;9:*.gz=38;5;9:*.lrz=38;5;9:*.lz=38;5;9:*.lzo=38;5;9:*.xz=38;5;9:*.bz2=38;5;9:*.bz=38;5;9:*.tbz=38;5;9:*.tbz2=38;5;9:*.tz=38;5;9:*.deb=38;5;9:*.rpm=38;5;9:*.jar=38;5;9:*.war=38;5;9:*.ear=38;5;9:*.sar=38;5;9:*.rar=38;5;9:*.alz=38;5;9:*.ace=38;5;9:*.zoo=38;5;9:*.cpio=38;5;9:*.7z=38;5;9:*.rz=38;5;9:*.cab=38;5;9:*.jpg=38;5;13:*.jpeg=38;5;13:*.gif=38;5;13:*.bmp=38;5;13:*.pbm=38;5;13:*.pgm=38;5;13:*.ppm=38;5;13:*.tga=38;5;13:*.xbm=38;5;13:*.xpm=38;5;13:*.tif=38;5;13:*.tiff=38;5;13:*.png=38;5;13:*.svg=38;5;13:*.svgz=38;5;13:*.mng=38;5;13:*.pcx=38;5;13:*.mov=38;5;13:*.mpg=38;5;13:*.mpeg=38;5;13:*.m2v=38;5;13:*.mkv=38;5;13:*.webm=38;5;13:*.ogm=38;5;13:*.mp4=38;5;13:*.m4v=38;5;13:*.mp4v=38;5;13:*.vob=38;5;13:*.qt=38;5;13:*.nuv=38;5;13:*.wmv=38;5;13:*.asf=38;5;13:*.rm=38;5;13:*.rmvb=38;5;13:*.flc=38;5;13:*.avi=38;5;13:*.fli=38;5;13:*.flv=38;5;13:*.gl=38;5;13:*.dl=38;5;13:*.xcf=38;5;13:*.xwd=38;5;13:*.yuv=38;5;13:*.cgm=38;5;13:*.emf=38;5;13:*.axv=38;5;13:*.anx=38;5;13:*.ogv=38;5;13:*.ogx=38;5;13:*.aac=38;5;45:*.au=38;5;45:*.flac=38;5;45:*.mid=38;5;45:*.midi=38;5;45:*.mka=38;5;45:*.mp3=38;5;45:*.mpc=38;5;45:*.ogg=38;5;45:*.ra=38;5;45:*.wav=38;5;45:*.axa=38;5;45:*.oga=38;5;45:*.spx=38;5;45:*.xspf=38;5;45:
MAIL=/var/spool/mail/root
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin
PWD=/root
LANG=en_US.UTF-8
HISTCONTROL=ignoredups
SHLVL=1
XDG_SEAT=seat0
HOME=/root
LOGNAME=root
XDG_DATA_DIRS=/root/.local/share/flatpak/exports/share/:/var/lib/flatpak/exports/share/:/usr/local/share/:/usr/share/
LESSOPEN=||/usr/bin/lesspipe.sh %s
_=/bin/env
OLDPWD=/etc
```

Ces variables rendent le travail avec shell plus simple. Les plus importantes sont:

* **HOSTNAME**: Le nom de votre ordinateur.
* **SHELL**: Décrit le shell qui interprète cette commande. Dans la plupart des cas, ce sera bash par défaut, mais d'autres valeurs peuvent être définies si vous préférez d'autres options.
* **TERM**: Spécifie le type de terminal à émuler lorsque vous lancer le shell. Des terminaux matériels différents peuvent être émulé pour les besoins de différentes opréation. Généralement, nous n'avons pas besoin de nous en occuper.
* **USER**: L'utilisateur actuellement connecté.
* **LS_COLORS**: Définie le code couleur qui est utilisé pour ajouté des couleurs sur les sorties de la commande LS. Elle est utilisé pour distinguer différents types de ficheirs et fournir plus d'information aux utilisateurs en un coup d'oeil.
* **MAIL**: Le chemin vers la boite mail de l'utilisateur courant.
* **PATH**: Une liste des dossier que le système vérifie lorsqu'il cherche des commandes. Lorsqu'un utilisateur tape une commande, le système vérifie ces dossier dans cet ordre pour les exécutables.

```
[root@centos7-1 ~]# echo $PATH
/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin
[root@centos7-1 ~]# which firefox
/bin/firefox
[root@centos7-1 ~]# type firefox
firefox is /bin/firefox
[root@centos7-1 ~]# whereis firefox
firefox: /usr/bin/firefox /usr/lib64/firefox /etc/firefox /usr/share/man/man1/firefox.1.gz
```

> Pour lancer d'autres scripts ou commande, nous pouvons les ajouter au  path, ou utiliser le chemin complet ou chemin relatif (. et ..)** **
>
> PATH=$PATH:/tmp/mybin

* **PWD**: Le dossier courant de travail.

> Nous utilisons la commande pwd pour voir ça:
>
> ```
> [root@centos7-1 ~]# pwd
> /root
> [root@centos7-1 ~]# cd /etc/
> [root@centos7-1 etc]# pwd
> /etc
> ```

* **LANG**: La langue courante et les paramètres de localisatio, incluant l'encodage des caractères.
* **HOME**: Le dossier home de l'utilisateur courant.
* **\_**: La dernière commande exécutée.
* **OLDPWD**: Le dossier de travail précédent. C'est gardé par shell afin de pouvoir revenir au dossier précédent en utilisant la commande `cd -.`

{% hint style="info" %}
Le code de sortie de la commande précédente est stockée dans la variable shell `$?`. Actuellement $? est un paramètre shell et non une variable, puisque l'on ne peut pas assigner de valeur à  $?.

`[root@centos7-1 ~]# touch my.txt `

`[root@centos7-1 ~]# echo $?`

` 0`
{% endhint %}

Pour voir la valeur d'une variable utiliser `echo $VARIABLENAME` :

```
[root@centos7-1 ~]# echo $MAIL
/var/spool/mail/root
[root@centos7-1 ~]# echo $USER
root
[root@centos7-1 ~]# echo $PATH
/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin
```

Pour définir des variables locales, nous créons une variable local VAR1 et nous lui assignons n'importe quel valeur( Historiquement il est recommandé d'utiliser les majuscule pour les variables mais vous être libre de faire ce que vous voulez) :

```
[root@centos7-1 ~]# VAR1="Lets Learn Linux"
[root@centos7-1 ~]# echo $VAR1
Lets Learn Linux
```

Par défaut les variables sont locales, donc qu'arrive t'il à notre variable si nous ouvrons un nouveau shell ?

```
[root@centos7-1 ~]# VAR1="Lets learn Linux"
[root@centos7-1 ~]# echo $VAR1
Lets learn Linux
[root@centos7-1 ~]# bash
[root@centos7-1 ~]# echo $VAR1
```

Il montre rien. Pourquoi. Why?

**Process enfant vs process parent (Child vs Parent process)**

Chaque processus peut être à la fois un processus parent et un processus enfant. La seule exception est le processus init, qui est toujours marqué avec le PID 1. Cependant init est le parent de tous les processus qui tourne sur un système Linux.

![](.gitbook/assets/commandintro-parentchild.jpg)

Chaque processus créé aura normalement un processus parent depuis lequel il a été créé et sera considéré comme enfant de ce processus parent.\
`echo $$` affiche un PID pour le shell courant

```
[root@centos7-1 ~]# echo $$
24370
[root@centos7-1 ~]# bash
[root@centos7-1 ~]# echo $$
24652
[root@centos7-1 ~]# exit
exit
[root@centos7-1 ~]# echo $$
24370
```

Lorsque nous créons un nouveaux processus enfant (un sous-shell dans notre exemple) une commande export assure simplement que les variables exportées dans le processus parents soient aussi disponible dans le processus enfant.

Nous pouvons terminer un shell en utilisant la commande exit ou simplement en appuyant sur les touches ctrl+d.

{% hint style="info" %}
**( )** : les parenthèses mettent réellement la commande dans un sous-terminal. Essayer : (exit)
{% endhint %}

### export

La commande export est une des commandes fournis par le bash shell, elle marque une variable d'environnement pour être exportée dans les processus enfant, pour que l'enfant hérite d'elle.

```
[root@centos7-1 ~]# VAR2="Linux is Fun"
[root@centos7-1 ~]# echo $VAR2
Linux is Fun
[root@centos7-1 ~]# export VAR2
[root@centos7-1 ~]# echo $$
24328
[root@centos7-1 ~]# bash
[root@centos7-1 ~]# echo $$
24722
[root@centos7-1 ~]# echo $VAR2
Linux is Fun
```

Syntaxe :`export [-fn] [name[=value] ...] or export -p`

La commande export est assez simple à utiliser avec une syntaxe légère puisqu'il n'y a que trois options :

```
-p    List of all names that are exported in the current shell
-n    Remove names from export list
-f    Names are exported as functions
```

Continuons notre exemple :

```
[root@centos7-1 ~]# export -p | grep -i var2
declare -x VAR2="Linux is Fun"
[root@centos7-1 ~]# export -n VAR2
[root@centos7-1 ~]# export -p | grep -i var2
[root@centos7-1 ~]#
```

### set

`set` est une commande fourni par shell (si aucune option ou argument ne lui est fourni) affiche toutes les variables, pas seulement celles d'environnement, et aussi les fonction shell, ce que vous voyez à la fin de la liste.

```
[root@centos7-1 ~]# set
ABRT_DEBUG_LOG=/dev/null
BASH=/bin/bash
BASHOPTS=checkwinsize:cmdhist:expand_aliases:extglob:extquote:force_fignore:histappend:interactive_comments:login_shell:progcomp:promptvars:sourcepath
BASH_ALIASES=()
BASH_ARGC=()
BASH_ARGV=()
BASH_CMDS=()
BASH_COMPLETION_COMPAT_DIR=/etc/bash_completion.d
BASH_LINENO=()
BASH_SOURCE=()
BASH_VERSINFO=([0]="4" [1]="2" [2]="46" [3]="2" [4]="release" [5]="x86_64-redhat-linux-gnu")
BASH_VERSION='4.2.46(2)-release'
COLUMNS=87
DIRSTACK=()
DISPLAY=:0
EUID=0
FINAL_LIST=
GLUSTER_BARRIER_OPTIONS=$'\n        {enable},\n        {disable}\n'
GLUSTER_COMMAND_TREE=$'\n{gluster [\n        \n        {volume [\n                {add-brick\n                        {__VOLNAME}\n                },\n                {barrier\n                        {__VOLNAME\n                                [ \n        {enable},\n        {disable}\n ]\n                        }\n                },\n                {clear-locks\n                        {__VOLNAME}\n                },\n                {create},\n                {delete\n                        {__VOLNAME}\n                },\n                {geo-replication\n                        [ \n        {__VOLNAME [\n                {__SLAVEURL [\n                        {create [\n                                {push-pem\n                                        {force}\n                                },\n                                {force}\n                                ]\n                        },\n                        {start {force} },\n                        {status {detail} },\n                        {config},\n                        {pause {force} },\n                        {resume {force} },\n                        {stop {force} },\n                        {delete {force} }\n                            ]\n                },\n                {status}\n                   ]\n        },\n        {status}\n ]\n                },\n                {heal\n                        {__VOLNAME}\n                },\n                {help},\n                {info\n                        {__VOLNAME}\n                },\n                {list},\n                {log\n                        {__VOLNAME}\n                },\n                {profile\n                        {__VOLNAME\n                                [ \n        {start},\n        {info [\n                {peek},\n                {incremental\n                        {peek}\n                },\n                {cumulative},\n                {clear},\n              ]\n        },\n        {stop}\n ]\n                        }\n                },\n                {quota\n                        {__VOLNAME\n                                [ \n        {enable},\n        {disable},\n        {list},\n        {remove},\n        {default-soft-limit},\n        {limit-usage},\n        {alert-time},\n        {soft-timeout},\n        {hard-timeout}\n ]\n                        }\n                },\n                {rebalance\n                        {__VOLNAME}\n                },\n                {remove-brick\n                        {__VOLNAME}\n                },\n                {replace-brick\n                        {__VOLNAME}\n                },\n                {reset\n                        {__VOLNAME\n                                [  ]\n                        }\n                },\n                {set\n                        {__VOLNAME\n                                [  ]\n                        }\n                },\n                {start\n                        {__VOLNAME\n                                {force}\n                        }\n                },\n                {statedump\n                        {__VOLNAME}\n                },\n                {status\n                        {__VOLNAME}\n                },\n                {stop\n                        {__VOLNAME\n                                {force}\n                        }\n                },\n                {sync\n                        {__HOSTNAME}\n                },\n                {top\n                        {__VOLNAME\n                                [ \n        {open\n                [  ]\n        },\n        {read\n                [  ]\n        },\n        {write\n                [  ]\n        },\n        {opendir\n                [  ]\n        },\n        {readdir\n                [  ]\n        },\n        {clear\n                [  ]\n        },\n        {read-perf\n                [  ]\n        },\n        {write-perf\n                [  ]\n        }\n ]\n                        }\n                }\n                ]\n        }\n ,\n        {peer [\n              {probe\n                      {__HOSTNAME}\n              },\n              {detach\n                      {__HOSTNAME\n                                {force}\n                      }\n              },\n              {status}\n              ]\n        },\n        {pool\n                {list}\n        },\n        {help}\n        ]\n}'
GLUSTER_GEO_REPLICATION_OPTIONS=$'\n        {__VOLNAME [\n                {__SLAVEURL [\n                        {create [\n                                {push-pem\n                                        {force}\n                                },\n                                {force}\n                                ]\n                        },\n                        {start {force} },\n                        {status {detail} },\n                        {config},\n                        {pause {force} },\n                        {resume {force} },\n                        {stop {force} },\n                        {delete {force} }\n                            ]\n                },\n                {status}\n                   ]\n        },\n        {status}\n'
GLUSTER_GEO_REPLICATION_SUBOPTIONS=$'\n'
GLUSTER_PROFILE_OPTIONS=$'\n        {start},\n        {info [\n                {peek},\n                {incremental\n                        {peek}\n                },\n                {cumulative},\n                {clear},\n              ]\n        },\n        {stop}\n'
GLUSTER_QUOTA_OPTIONS=$'\n        {enable},\n        {disable},\n        {list},\n        {remove},\n        {default-soft-limit},\n        {limit-usage},\n        {alert-time},\n        {soft-timeout},\n        {hard-timeout}\n'
GLUSTER_TOP_OPTIONS=$'\n        {open\n                [  ]\n        },\n        {read\n                [  ]\n        },\n        {write\n                [  ]\n        },\n        {opendir\n                [  ]\n        },\n        {readdir\n                [  ]\n        },\n        {clear\n                [  ]\n        },\n        {read-perf\n                [  ]\n        },\n        {write-perf\n                [  ]\n        }\n'
GLUSTER_TOP_SUBOPTIONS1=$'\n        {nfs},\n        {brick},\n        {list-cnt}\n'
GLUSTER_TOP_SUBOPTIONS2=$'\n        {bs\n                {__SIZE\n                        {count}\n                }\n        },\n        {brick},\n        {list-cnt}\n'
GLUSTER_VOLUME_OPTIONS=$'\n        {volume [\n                {add-brick\n                        {__VOLNAME}\n                },\n                {barrier\n                        {__VOLNAME\n                                [ \n        {enable},\n        {disable}\n ]\n                        }\n                },\n                {clear-locks\n                        {__VOLNAME}\n                },\n                {create},\n                {delete\n                        {__VOLNAME}\n                },\n                {geo-replication\n                        [ \n        {__VOLNAME [\n                {__SLAVEURL [\n                        {create [\n                                {push-pem\n                                        {force}\n                                },\n                                {force}\n                                ]\n                        },\n                        {start {force} },\n                        {status {detail} },\n                        {config},\n                        {pause {force} },\n                        {resume {force} },\n                        {stop {force} },\n                        {delete {force} }\n                            ]\n                },\n                {status}\n                   ]\n        },\n        {status}\n ]\n                },\n                {heal\n                        {__VOLNAME}\n                },\n                {help},\n                {info\n                        {__VOLNAME}\n                },\n                {list},\n                {log\n                        {__VOLNAME}\n                },\n                {profile\n                        {__VOLNAME\n                                [ \n        {start},\n        {info [\n                {peek},\n                {incremental\n                        {peek}\n                },\n                {cumulative},\n                {clear},\n              ]\n        },\n        {stop}\n ]\n                        }\n                },\n                {quota\n                        {__VOLNAME\n                                [ \n        {enable},\n        {disable},\n        {list},\n        {remove},\n        {default-soft-limit},\n        {limit-usage},\n        {alert-time},\n        {soft-timeout},\n        {hard-timeout}\n ]\n                        }\n                },\n                {rebalance\n                        {__VOLNAME}\n                },\n                {remove-brick\n                        {__VOLNAME}\n                },\n                {replace-brick\n                        {__VOLNAME}\n                },\n                {reset\n                        {__VOLNAME\n                                [  ]\n                        }\n                },\n                {set\n                        {__VOLNAME\n                                [  ]\n                        }\n                },\n                {start\n                        {__VOLNAME\n                                {force}\n                        }\n                },\n                {statedump\n                        {__VOLNAME}\n                },\n                {status\n                        {__VOLNAME}\n                },\n                {stop\n                        {__VOLNAME\n                                {force}\n                        }\n                },\n                {sync\n                        {__HOSTNAME}\n                },\n                {top\n                        {__VOLNAME\n                                [ \n        {open\n                [  ]\n        },\n        {read\n                [  ]\n        },\n        {write\n                [  ]\n        },\n        {opendir\n                [  ]\n        },\n        {readdir\n                [  ]\n        },\n        {clear\n                [  ]\n        },\n        {read-perf\n                [  ]\n        },\n        {write-perf\n                [  ]\n        }\n ]\n                        }\n                }\n                ]\n        }\n'
GROUPS=()
HISTCONTROL=ignoredups
HISTFILE=/root/.bash_history
HISTFILESIZE=1000
HISTSIZE=1000
HOME=/root
HOSTNAME=centos7-1
HOSTTYPE=x86_64
ID=0
IFS=$' \t\n'
LANG=en_US.UTF-8
LESSOPEN='||/usr/bin/lesspipe.sh %s'
LINES=23
LIST=
LOGNAME=root
LPATHDIR=/root/.cache/abrt
LS_COLORS='rs=0:di=38;5;27:ln=38;5;51:mh=44;38;5;15:pi=40;38;5;11:so=38;5;13:do=38;5;5:bd=48;5;232;38;5;11:cd=48;5;232;38;5;3:or=48;5;232;38;5;9:mi=05;48;5;232;38;5;15:su=48;5;196;38;5;15:sg=48;5;11;38;5;16:ca=48;5;196;38;5;226:tw=48;5;10;38;5;16:ow=48;5;10;38;5;21:st=48;5;21;38;5;15:ex=38;5;34:*.tar=38;5;9:*.tgz=38;5;9:*.arc=38;5;9:*.arj=38;5;9:*.taz=38;5;9:*.lha=38;5;9:*.lz4=38;5;9:*.lzh=38;5;9:*.lzma=38;5;9:*.tlz=38;5;9:*.txz=38;5;9:*.tzo=38;5;9:*.t7z=38;5;9:*.zip=38;5;9:*.z=38;5;9:*.Z=38;5;9:*.dz=38;5;9:*.gz=38;5;9:*.lrz=38;5;9:*.lz=38;5;9:*.lzo=38;5;9:*.xz=38;5;9:*.bz2=38;5;9:*.bz=38;5;9:*.tbz=38;5;9:*.tbz2=38;5;9:*.tz=38;5;9:*.deb=38;5;9:*.rpm=38;5;9:*.jar=38;5;9:*.war=38;5;9:*.ear=38;5;9:*.sar=38;5;9:*.rar=38;5;9:*.alz=38;5;9:*.ace=38;5;9:*.zoo=38;5;9:*.cpio=38;5;9:*.7z=38;5;9:*.rz=38;5;9:*.cab=38;5;9:*.jpg=38;5;13:*.jpeg=38;5;13:*.gif=38;5;13:*.bmp=38;5;13:*.pbm=38;5;13:*.pgm=38;5;13:*.ppm=38;5;13:*.tga=38;5;13:*.xbm=38;5;13:*.xpm=38;5;13:*.tif=38;5;13:*.tiff=38;5;13:*.png=38;5;13:*.svg=38;5;13:*.svgz=38;5;13:*.mng=38;5;13:*.pcx=38;5;13:*.mov=38;5;13:*.mpg=38;5;13:*.mpeg=38;5;13:*.m2v=38;5;13:*.mkv=38;5;13:*.webm=38;5;13:*.ogm=38;5;13:*.mp4=38;5;13:*.m4v=38;5;13:*.mp4v=38;5;13:*.vob=38;5;13:*.qt=38;5;13:*.nuv=38;5;13:*.wmv=38;5;13:*.asf=38;5;13:*.rm=38;5;13:*.rmvb=38;5;13:*.flc=38;5;13:*.avi=38;5;13:*.fli=38;5;13:*.flv=38;5;13:*.gl=38;5;13:*.dl=38;5;13:*.xcf=38;5;13:*.xwd=38;5;13:*.yuv=38;5;13:*.cgm=38;5;13:*.emf=38;5;13:*.axv=38;5;13:*.anx=38;5;13:*.ogv=38;5;13:*.ogx=38;5;13:*.aac=38;5;45:*.au=38;5;45:*.flac=38;5;45:*.mid=38;5;45:*.midi=38;5;45:*.mka=38;5;45:*.mp3=38;5;45:*.mpc=38;5;45:*.ogg=38;5;45:*.ra=38;5;45:*.wav=38;5;45:*.axa=38;5;45:*.oga=38;5;45:*.spx=38;5;45:*.xspf=38;5;45:'
MACHTYPE=x86_64-redhat-linux-gnu
MAIL=/var/spool/mail/root
MAILCHECK=60
OPTERR=1
OPTIND=1
OSTYPE=linux-gnu
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin
PIPESTATUS=([0]="0")
PPID=23050
PROMPT_COMMAND='printf "\033]0;%s@%s:%s\007" "${USER}" "${HOSTNAME%%.*}" "${PWD/#$HOME/~}"'
PS1='[\u@\h \W]\$ '
PS2='> '
PS4='+ '
PWD=/root
SHELL=/bin/bash
SHELLOPTS=braceexpand:emacs:hashall:histexpand:history:interactive-comments:monitor
SHLVL=1
SINCE=1544460722
SINCEFILE=/root/.cache/abrt/lastnotification
TERM=xterm-256color
TMPPATH=/root/.cache/abrt/lastnotification.QPDZ45se
TOP=0
UID=0
USER=root
XAUTHORITY=/root/.xauthTthXrM
XDG_DATA_DIRS=/root/.local/share/flatpak/exports/share/:/var/lib/flatpak/exports/share/:/usr/local/share/:/usr/share/
XDG_SEAT=seat0
XDG_SESSION_ID=1
XDG_VTNR=1
_=PATH
_backup_glob='@(#*#|*@(~|.@(bak|orig|rej|swp|dpkg*|rpm@(orig|new|save))))'
_xspecs=([freeamp]="!*.@(mp3|ogg|pls|m3u)" [cdiff]="!*.@(dif?(f)|?(d)patch)?(.@([gx]z|bz2|lzma))" [bibtex]="!*.aux" [rgview]="*.@(o|so|so.!(conf|*/*)|a|[rs]pm|gif|jp?(e)g|mp3|mp?(e)g|avi|asf|ogg|class)" [oowriter]="!*.@(sxw|stw|sxg|sgl|doc?([mx])|dot?([mx])|rtf|txt|htm|html|?(f)odt|ott|odm)" [chromium-browser]="!*.@(?([xX]|[sS])[hH][tT][mM]?([lL]))" [tex]="!*.@(?(la)tex|texi|dtx|ins|ltx|dbj)" [zathura]="!*.@(cb[rz7t]|djv?(u)|?(e)ps|pdf)" [netscape]="!*.@(?([xX]|[sS])[hH][tT][mM]?([lL]))" [acroread]="!*.[pf]df" [makeinfo]="!*.texi*" [kwrite]="*.@(o|so|so.!(conf|*/*)|a|[rs]pm|gif|jp?(e)g|mp3|mp?(e)g|avi|asf|ogg|class)" [gview]="*.@(o|so|so.!(conf|*/*)|a|[rs]pm|gif|jp?(e)g|mp3|mp?(e)g|avi|asf|ogg|class)" [qiv]="!*.@(gif|jp?(e)g|tif?(f)|png|p[bgp]m|bmp|x[bp]m|rle|rgb|pcx|fits|pm|svg)" [lrunzip]="!*.lrz" [bzcat]="!*.?(t)bz?(2)" [amaya]="!*.@(?([xX]|[sS])[hH][tT][mM]?([lL]))" [pdftex]="!*.@(?(la)tex|texi|dtx|ins|ltx|dbj)" [hbpp]="!*.@([Pp][Rr][Gg]|[Cc][Ll][Pp])" [rpm2cpio]="!*.[rs]pm" [view]="*.@(o|so|so.!(conf|*/*)|a|[rs]pm|gif|jp?(e)g|mp3|mp?(e)g|avi|asf|ogg|class)" [unxz]="!*.@(?(t)xz|tlz|lzma)" [ly2dvi]="!*.ly" [mozilla]="!*.@(?([xX]|[sS])[hH][tT][mM]?([lL]))" [modplugplay]="!*.@(669|abc|am[fs]|d[bs]m|dmf|far|it|mdl|m[eo]d|mid?(i)|mt[2m]|okta|p[st]m|s[3t]m|ult|umx|wav|xm)" [lzgrep]="!*.@(tlz|lzma)" [pyflakes]="!*.py" [dillo]="!*.@(?([xX]|[sS])[hH][tT][mM]?([lL]))" [aaxine]="!*@(.@(mp?(e)g|MP?(E)G|wma|avi|AVI|asf|vob|VOB|bin|dat|divx|DIVX|vcd|ps|pes|fli|flv|FLV|fxm|FXM|viv|rm|ram|yuv|mov|MOV|qt|QT|wmv|mp[234]|MP[234]|m4[pv]|M4[PV]|mkv|MKV|og[gmv]|OG[GMV]|t[ps]|T[PS]|m2t?(s)|M2T?(S)|wav|WAV|flac|FLAC|asx|ASX|mng|MNG|srt|m[eo]d|M[EO]D|s[3t]m|S[3T]M|it|IT|xm|XM)|+([0-9]).@(vdr|VDR))?(.part)" [dvipdfmx]="!*.dvi" [advi]="!*.dvi" [ggv]="!*.@(@(?(e)ps|?(E)PS|pdf|PDF)?(.gz|.GZ|.bz2|.BZ2|.Z))" [lzmore]="!*.@(tlz|lzma)" [lzless]="!*.@(tlz|lzma)" [kdvi]="!*.@(dvi|DVI)?(.@(gz|Z|bz2))" [poedit]="!*.po" [firefox]="!*.@(?([xX]|[sS])[hH][tT][mM]?([lL]))" [gv]="!*.@(@(?(e)ps|?(E)PS|pdf|PDF)?(.gz|.GZ|.bz2|.BZ2|.Z))" [madplay]="!*.mp3" [lbzcat]="!*.?(t)bz?(2)" [lilypond]="!*.ly" [gtranslator]="!*.po" [jadetex]="!*.@(?(la)tex|texi|dtx|ins|ltx|dbj)" [sxemacs]="*.@(o|so|so.!(conf|*/*)|a|[rs]pm|gif|jp?(e)g|mp3|mp?(e)g|avi|asf|ogg|class)" [epdfview]="!*.pdf" [gpdf]="!*.[pf]df" [kghostview]="!*.@(@(?(e)ps|?(E)PS|pdf|PDF)?(.gz|.GZ|.bz2|.BZ2|.Z))" [pbzcat]="!*.?(t)bz?(2)" [texi2dvi]="!*.@(?(la)tex|texi|dtx|ins|ltx|dbj)" [ps2pdf12]="!*.@(?(e)ps|pdf)" [ee]="!*.@(gif|jp?(e)g|miff|tif?(f)|pn[gm]|p[bgp]m|bmp|xpm|ico|xwd|tga|pcx)" [lzcat]="!*.@(tlz|lzma)" [lbunzip2]="!*.?(t)bz?(2)" [ps2pdf13]="!*.@(?(e)ps|pdf)" [vim]="*.@(o|so|so.!(conf|*/*)|a|[rs]pm|gif|jp?(e)g|mp3|mp?(e)g|avi|asf|ogg|class)" [ps2pdf14]="!*.@(?(e)ps|pdf)" [dvips]="!*.dvi" [lzfgrep]="!*.@(tlz|lzma)" [hbrun]="!*.[Hh][Rr][Bb]" [kbabel]="!*.po" [rview]="*.@(o|so|so.!(conf|*/*)|a|[rs]pm|gif|jp?(e)g|mp3|mp?(e)g|avi|asf|ogg|class)" [kaffeine]="!*@(.@(mp?(e)g|MP?(E)G|wma|avi|AVI|asf|vob|VOB|bin|dat|divx|DIVX|vcd|ps|pes|fli|flv|FLV|fxm|FXM|viv|rm|ram|yuv|mov|MOV|qt|QT|wmv|mp[234]|MP[234]|m4[pv]|M4[PV]|mkv|MKV|og[gmv]|OG[GMV]|t[ps]|T[PS]|m2t?(s)|M2T?(S)|wav|WAV|flac|FLAC|asx|ASX|mng|MNG|srt|m[eo]d|M[EO]D|s[3t]m|S[3T]M|it|IT|xm|XM|iso|ISO)|+([0-9]).@(vdr|VDR))?(.part)" [xv]="!*.@(gif|jp?(e)g|tif?(f)|png|p[bgp]m|bmp|x[bp]m|rle|rgb|pcx|fits|pm|?(e)ps)" [rgvim]="*.@(o|so|so.!(conf|*/*)|a|[rs]pm|gif|jp?(e)g|mp3|mp?(e)g|avi|asf|ogg|class)" [dvitype]="!*.dvi" [oodraw]="!*.@(sxd|std|sda|sdd|?(f)odg|otg)" [elinks]="!*.@(?([xX]|[sS])[hH][tT][mM]?([lL]))" [playmidi]="!*.@(mid?(i)|cmf)" [realplay]="!*.@(rm?(j)|ra?(m)|smi?(l))" [xine]="!*@(.@(mp?(e)g|MP?(E)G|wma|avi|AVI|asf|vob|VOB|bin|dat|divx|DIVX|vcd|ps|pes|fli|flv|FLV|fxm|FXM|viv|rm|ram|yuv|mov|MOV|qt|QT|wmv|mp[234]|MP[234]|m4[pv]|M4[PV]|mkv|MKV|og[gmv]|OG[GMV]|t[ps]|T[PS]|m2t?(s)|M2T?(S)|wav|WAV|flac|FLAC|asx|ASX|mng|MNG|srt|m[eo]d|M[EO]D|s[3t]m|S[3T]M|it|IT|xm|XM)|+([0-9]).@(vdr|VDR))?(.part)" [xpdf]="!*.[pf]df" [gqmpeg]="!*.@(mp3|ogg|pls|m3u)" [lzegrep]="!*.@(tlz|lzma)" [aviplay]="!*.@(avi|asf|wmv)" [latex]="!*.@(?(la)tex|texi|dtx|ins|ltx|dbj)" [rvim]="*.@(o|so|so.!(conf|*/*)|a|[rs]pm|gif|jp?(e)g|mp3|mp?(e)g|avi|asf|ogg|class)" [bunzip2]="!*.?(t)bz?(2)" [ogg123]="!*.@(ogg|m3u|flac|spx)" [ps2pdfwr]="!*.@(?(e)ps|pdf)" [znew]="*.Z" [harbour]="!*.@([Pp][Rr][Gg]|[Cc][Ll][Pp])" [lokalize]="!*.po" [kate]="*.@(o|so|so.!(conf|*/*)|a|[rs]pm|gif|jp?(e)g|mp3|mp?(e)g|avi|asf|ogg|class)" [xemacs]="*.@(o|so|so.!(conf|*/*)|a|[rs]pm|gif|jp?(e)g|mp3|mp?(e)g|avi|asf|ogg|class)" [dragon]="!*@(.@(mp?(e)g|MP?(E)G|wma|avi|AVI|asf|vob|VOB|bin|dat|divx|DIVX|vcd|ps|pes|fli|flv|FLV|fxm|FXM|viv|rm|ram|yuv|mov|MOV|qt|QT|wmv|mp[234]|MP[234]|m4[pv]|M4[PV]|mkv|MKV|og[gmv]|OG[GMV]|t[ps]|T[PS]|m2t?(s)|M2T?(S)|wav|WAV|flac|FLAC|asx|ASX|mng|MNG|srt|m[eo]d|M[EO]D|s[3t]m|S[3T]M|it|IT|xm|XM|iso|ISO)|+([0-9]).@(vdr|VDR))?(.part)" [unlzma]="!*.@(tlz|lzma)" [pdflatex]="!*.@(?(la)tex|texi|dtx|ins|ltx|dbj)" [vi]="*.@(o|so|so.!(conf|*/*)|a|[rs]pm|gif|jp?(e)g|mp3|mp?(e)g|avi|asf|ogg|class)" [mozilla-firefox]="!*.@(?([xX]|[sS])[hH][tT][mM]?([lL]))" [ooimpress]="!*.@(sxi|sti|pps?(x)|ppt?([mx])|pot?([mx])|?(f)odp|otp)" [gvim]="*.@(o|so|so.!(conf|*/*)|a|[rs]pm|gif|jp?(e)g|mp3|mp?(e)g|avi|asf|ogg|class)" [uncompress]="!*.Z" [kid3-qt]="!*.@(mp[234c]|og[ag]|@(fl|a)ac|m4[abp]|spx|tta|w?(a)v|wma|aif?(f)|asf|ape)" [xanim]="!*.@(mpg|mpeg|avi|mov|qt)" [unpigz]="!*.@(Z|[gGd]z|t[ag]z)" [portecle]="!@(*.@(ks|jks|jceks|p12|pfx|bks|ubr|gkr|cer|crt|cert|p7b|pkipath|pem|p10|csr|crl)|cacerts)" [oocalc]="!*.@(sxc|stc|xls?([bmx])|xlw|xlt?([mx])|[ct]sv|?(f)ods|ots)" [emacs]="*.@(o|so|so.!(conf|*/*)|a|[rs]pm|gif|jp?(e)g|mp3|mp?(e)g|avi|asf|ogg|class)" [fbxine]="!*@(.@(mp?(e)g|MP?(E)G|wma|avi|AVI|asf|vob|VOB|bin|dat|divx|DIVX|vcd|ps|pes|fli|flv|FLV|fxm|FXM|viv|rm|ram|yuv|mov|MOV|qt|QT|wmv|mp[234]|MP[234]|m4[pv]|M4[PV]|mkv|MKV|og[gmv]|OG[GMV]|t[ps]|T[PS]|m2t?(s)|M2T?(S)|wav|WAV|flac|FLAC|asx|ASX|mng|MNG|srt|m[eo]d|M[EO]D|s[3t]m|S[3T]M|it|IT|xm|XM)|+([0-9]).@(vdr|VDR))?(.part)" [lynx]="!*.@(?([xX]|[sS])[hH][tT][mM]?([lL]))" [ps2pdf]="!*.@(?(e)ps|pdf)" [kpdf]="!*.@(?(e)ps|pdf)" [oomath]="!*.@(sxm|smf|mml|odf)" [compress]="*.Z" [iceweasel]="!*.@(?([xX]|[sS])[hH][tT][mM]?([lL]))" [mpg321]="!*.mp3" [mpg123]="!*.mp3" [zcat]="!*.@(Z|[gGd]z|t[ag]z)" [unzip]="!*.@(zip|[ejsw]ar|exe|pk3|wsz|zargo|xpi|s[tx][cdiw]|sx[gm]|o[dt][tspgfc]|od[bm]|oxt|epub|apk|do[ct][xm]|p[op]t[mx]|xl[st][xm])" [pbunzip2]="!*.?(t)bz?(2)" [kid3]="!*.@(mp[234c]|og[ag]|@(fl|a)ac|m4[abp]|spx|tta|w?(a)v|wma|aif?(f)|asf|ape)" [pdfjadetex]="!*.@(?(la)tex|texi|dtx|ins|ltx|dbj)" [dvipdf]="!*.dvi" [gharbour]="!*.@([Pp][Rr][Gg]|[Cc][Ll][Pp])" [modplug123]="!*.@(669|abc|am[fs]|d[bs]m|dmf|far|it|mdl|m[eo]d|mid?(i)|mt[2m]|okta|p[st]m|s[3t]m|ult|umx|wav|xm)" [dvipdfm]="!*.dvi" [oobase]="!*.odb" [texi2html]="!*.texi*" [zipinfo]="!*.@(zip|[ejsw]ar|exe|pk3|wsz|zargo|xpi|s[tx][cdiw]|sx[gm]|o[dt][tspgfc]|od[bm]|oxt|epub|apk|do[ct][xm]|p[op]t[mx]|xl[st][xm])" [epiphany]="!*.@(?([xX]|[sS])[hH][tT][mM]?([lL]))" [gunzip]="!*.@(Z|[gGd]z|t[ag]z)" [google-chrome]="!*.@(?([xX]|[sS])[hH][tT][mM]?([lL]))" [okular]="!*.@(okular|@(?(e|x)ps|?(E|X)PS|[pf]df|[PF]DF|dvi|DVI|cb[rz]|CB[RZ]|djv?(u)|DJV?(U)|dvi|DVI|gif|jp?(e)g|miff|tif?(f)|pn[gm]|p[bgp]m|bmp|xpm|ico|xwd|tga|pcx|GIF|JP?(E)G|MIFF|TIF?(F)|PN[GM]|P[BGP]M|BMP|XPM|ICO|XWD|TGA|PCX|epub|EPUB|odt|ODT|fb?(2)|FB?(2)|mobi|MOBI|g3|G3|chm|CHM)?(.?(gz|GZ|bz2|BZ2)))" [slitex]="!*.@(?(la)tex|texi|dtx|ins|ltx|dbj)" [galeon]="!*.@(?([xX]|[sS])[hH][tT][mM]?([lL]))" [bzme]="!*.@(zip|z|gz|tgz)" [xfig]="!*.fig" [xzcat]="!*.@(?(t)xz|tlz|lzma)" [timidity]="!*.@(mid?(i)|rmi|rcp|[gr]36|g18|mod|xm|it|x3m|s[3t]m|kar)" [dviselect]="!*.dvi" [xdvi]="!*.@(dvi|DVI)?(.@(gz|Z|bz2))" )
colors=/root/.dircolors
flatpak_dirs=/root/.local/share/flatpak/exports/share/:/var/lib/flatpak/exports/share/
__HOSTNAME () 
{ 
    local zero=0;
    local ret=0;
    local cur_word="$2";
    if [ "$1" == "X" ]; then
        return;
    else
        if [ "$1" == "match" ]; then
            return 0;
        else
            if [ "$1" == "complete" ]; then
                COMPREPLY=($(compgen -A hostname -- $cur_word));
            fi;
        fi;
    fi;
    return 0
}
...
<output has been truncated>
```

> **set vs env**
>
> Version courte: `set` peut voir les variables locale du shell, `env` ne peut pas.
>
> Comme nous l'avons mentionnés, les shell peuvent avoir des variables de 2 types : locales, qui sont uniquement accessible dans le shell courante, et variables d'environnement (exportées), qui sont passé à chaque programme exécutés.
>
> Puisque `set` est une commande native du shell, il est aussi possible de voir des variables local à shell (incluant des fonctions shell). `env` d'un autre côté est un exécutable indépendant; il peut seulement voir les variables que shell lui a passer, ou les variables d'environnement

Dans un équivalent de bash comme n'importe quel shell Bourne-like, `set` est la commande pour définir des options (les paramètres de configuration shell comme -f, -C, -o noclobber...) et les paramètres de position ($1, $2...).

```
set [--abefhkmnptuvxBCEHPT] [-o option-name] [argument …]
set [+abefhkmnptuvxBCEHPT] [+o option-name] [argument …]
```

 Utiliser les signes  - et + pour activer ou désactiver les options. Essayer : `help set | less`  .

Pour voir les options courantes du shell, utiliser `echo $-` :

```
[root@centos7-1 ~]# echo $-
himuBH
```

 Par exemple `-u` traite les variables non définies comme une erreur lors de leur remplacement :

```
root@ubuntu16-1:~# echo $-
himBH
root@ubuntu16-1:~# echo $VAR1

root@ubuntu16-1:~# set -u
root@ubuntu16-1:~# echo $-
himuBH
root@ubuntu16-1:~# echo $VAR1
-su: VAR1: unbound variable
root@ubuntu16-1:~# set +u
root@ubuntu16-1:~# echo $-
himBH
```

Nous pouvons utiliser `set -o` sans autre option pour obtenir l'état courant des options shell :

```
 [root@centos7-1 ~]# set -o
allexport          off
braceexpand        on
emacs              on
errexit            off
errtrace           off
functrace          off
hashall            on
histexpand         on
history            on
ignoreeof          off
interactive-comments    on
keyword            off
monitor            on
noclobber          off
noexec             off
noglob             off
nolog              off
notify             off
nounset            off
onecmd             off
physical           off
pipefail           off
posix              off
privileged         off
verbose            off
vi                 off
xtrace             off
```

`set +o` désactive les options spécifiés et `set -o` les actives. Par exemple, désactivons et réactivons `History Storing` avec l'option= `-o` :

```
[root@centos7-1 ~]# set +o history
[root@centos7-1 ~]# set -o | grep history
history            off
[root@centos7-1 ~]# set -o history
[root@centos7-1 ~]# set -o | grep history
history            on
```

Une autre options importante que vous devez connaitre pour l'examene est `noclobber`, L'option `noclobber` vous empêche de surcharger des fichiers existants avec l'opérateur > (Expliqué dans le prochain cours) .

`set` ne sert pas à définir des variable. Ne faite pas l'erreur!

```
[root@centos7-1 ~]# set VAR4="My Forth Variable"
[root@centos7-1 ~]# echo $VAR4

[root@centos7-1 ~]#
```

`Set` a de nombreuses options qui sont utilisés pour changer le comportement de bash.

### unset

La commande `unset` est utilisé pour supprimé des variable d'environnement local temporairement:

```
[root@centos7-1 ~]# VAR5="Linux Linux Linux"
[root@centos7-1 ~]# echo $VAR5
Linux Linux Linux
[root@centos7-1 ~]# unset VAR5
[root@centos7-1 ~]# echo $VAR5
bash: VAR5: unbound variable
[root@centos7-1 ~]# unset PATH
[root@centos7-1 ~]# ls
bash: ls: No such file or directory
```

{% hint style="success" %}
`set` ne sert pas à définir, mais `unset` supprime les variables.
{% endhint %}

{% hint style="info" %}
Nous pouvons aussi utiliser  `env VARNAME=VALUE` et `env -u VARNAME` pour définir et supprimer une variable.
{% endhint %}

## Comprendre l'historique de Bash

L'historique de toutes les commands qui ont été exécutée par tous les utilisateur sont stockés. History est maintenu à la fois dans la RAM et dans un fichier _.bash_history._  History qui est maintenu dans la ram, peut être manipulé avec la commande `history` et les variables d'environnement. Il est important de noter que l'historique contenu dans la RAM est écrit seulement après la déconnexion d'un utilisateur de sa session.

### history

`history` montre le contenu courant de la liste de l'historique de Bash dans la mémoire pour la session courante.

```
[root@centos7-1 ~]# history 
    1  dhclient -r
    2  dhclient
    3  seastatus
    4  sestatus 
    5  vim /etc/sysconfig/selinux 
    6  setenforce 0
    7  sestatus 
    8  reboot
    9  cd /var/lib/ldap/
   10  ls
   11  ls -la
   12  ls -lrth
   13  ping 8.8.8.8
   14  dhclient -r
   15  shcli
   16  dhclient
   17  ping 8.8.8.8
   18  sestatus 
   19  yum install openldap openldap-servers openldap-clients.x86_64
   20  systemctl status slapd.service
<output has been trancuated>
```

Combien de lignes sont stockée dans la version RAM de la liste d'historique ? C'est défini via la variable HISTSIZE.

* **HISTSIZE** : Cette variable contient le nombre maximum de ligne qui peuvent être contenu dans la version en ram de l'historique.

```
[root@centos7-1 ~]# env | grep -i histsize
HISTSIZE=1000
```

Et cela est maintenant 1000 lignes! pour voir seulement 5 lignes par exemple utiliser `history 5` .

**Répéter la commande précédente rapidement (3 méthodes)**

1. Utiliser la **flèche vers le haut** pour voir la commande précédente et appuyez sur entrée pour l'exécuter.
2. **Taper !! et appuyer sur entrée** depuis la ligne de commande.(ou **!!number** )
3. **Appuyez sur Ctrl+P** affichera la commande précédente, puis presser entrée pour l'exécuter

**Rechercher dans l'historique (3 méthodes)**

1. **Control+R:** Appuyez sur Control+R et taper le mot-clé.
2. **!string** Fait référence à la commande la plus récente qui commence avec la chaine de caractère (string).
3. **!?string?** Fait référence à la commande la plus récente qui contient la chaine de caractère (string) (le ? final est optionnel).

**Nettoyer tous les historiques précédents :**Utiliser l'option `-c` de la commande `history`.

### **\~/.bash_history**

Lorsqu'un utilisateur ferme son shell, Bash enregistre son historique sur le disque en ajoutant le contenu des entrée dans son **~/.bash_history** qui est contrôlé par quelques variables:

* **HISTFILE** Cette variable contient la localisation du fichier d'historique. Lorsque bash est déconnecté le contenu de la commande history sera écrit dans ce fichier.
* **HISTFILESIZE** La variable contient le nombre maximum de ligne qui peuvent être dans le fichier d'historique, les plus vieilles commandes qui vont au delà de ce maximum seront supprimés.

     il agit comme  HISTSIZE.
* **HISTCONTROL** Cette variable contient des instructions pour ce qui doit être ignoré lors de l'ajout dans le fichier d'historique. Il a 4 paramètres :

```
Setting Name    Setting Effect
Ignorespace    When this setting is used, any line that begins with one or more spaces will not be added to the history.
Ignoredups    When this setting is used lines that match the previous entry in the history are not saved.
Ignoreboth    When this setting is used it implies the conditions of both ignoredups and ignorespace.
Not Defined    If HISTCONTROL is not defined no conditions will be applied before the line is entered into the history
```

Pour voir le fichier caché **.bash_history** nous devons utiliser `ls` avec l'option `-a`:

```
[root@centos7-1 ~]# ls -la | grep  .bash_history
-rw-------.  1 root root   9779 Dec  9 14:29 .bash_history
```

Quid des autres utilisateurs ?

```
[root@centos7-1 ~]# find /home/ -iname .bash_history
/home/payam/.bash_history
/home/ldapadm/.bash_history
/home/user1/.bash_history
/home/user2/.bash_history
```

En tant qu'instance on nous laisse trouver ce qu'à fait user1:

```
[root@centos7-1 ~]# cat /home/user1/.bash_history
ls
exit
```

Il/elle n'a rien fait :). **.bash_history** par défaut garde 500 ou 1000 commande qui ont été utilisé. Nous pouvons donc remonter à quelque chose qui a été fait il y a longtemps. `history -c` n'efface pas ça et il reste après la deconnexion de l'utilisateur, donc si vous n'aimez pas ça, vous devez le supprimer.

### uname

La commande `uname` sans option permet d'afficher des information sur le système :

```
[root@centos7-1 ~]# uname
Linux
```

Sa syntaxe est comme `uname [OPTION] ...` . Par exemple `-a, --all` affiche toutes les informations :

```
[root@centos7-1 ~]# uname -a
Linux centos7-1 3.10.0-693.el7.x86_64 #1 SMP Tue Aug 22 21:09:27 UTC 2017 x86_64 x86_64 x86_64 GNU/Linux
```

Si l'option `-a` (--all) est spécifié, les informations sont affichés avec l'ordre suivant des options individuelles:

```
-s, --kernel-name    Print the kernel name.
-n, --nodename    Print the network node hostname.
-r, --kernel-release    Print the kernel release.
-v, --kernel-version    Print the kernel version.
-m, --machine    Print the machine hardware name.
-p, --processor    Print the processor type, or "unknown".
-i, --hardware-platform    Print the hardware platform, or "unknown".
-o, --operating-system    Print the operating system.
--help    Display a help message, and exit.
--version    Display version information, and exit.
```

Les informations sur le processeurs (-p)  et sur la plateforme matériel sont omises si elle ne sont pas connues.

### man

Dans les systèmes d'exploitation Unix-like, une page man (en entier une page de manuel) est une documentation pour un programme/outil/utilitaire basé sur le terminal (connu sous le nom de commande). Il contient:

* **le nom de la commande**
* **la syntaxe pour l'utiliser**
* **une description**
* **options disponible**
* **auteur**
* **licence**
* **Commandes liés et ... .**

Pour lire une page de manuel d'une commande Unix, un utilisateur peut taper :

```
man <command_name>
```

Par exemple essayer `man man` :

![](.gitbook/assets/commandintro-manman.jpg)

Par défaut, man utilise un programme de pagination du terminal comme **more** ou **less** pour afficher sa sortie.

Le manuel est généralement écoupé en huit ou neuf sections numérotées, organisées comme suit :

1. **Programme exécutable ou commande shell**
2. **Appel système** (fonctions fournis par le noyau)
3. **Appel aux bibliothèque** (fonctions venant des bibliothèques)
4. **Fichiers spéciaux** (souven trouvé dans /dev)
5. **Format des fichiers et conventions** exemple /etc/passwd
6. **Jeux**
7. **Divers**(incluant les macro paquets et les convention), exemple. man(7), groff(7)
8. **Commande d'administration système** (généralement seulement pour root)
9. **Routines du noyau** \[Non standard]

Pour demander à man d'afficher la page d'une section spécifique utiliser `man [section-num] [command/tool name]` :

```
man 1 echo
```

man cherche la page désiré du manuel dans le cache d'index de la base de données. Il n'a donc pas besoin de se rappeler des numéros de section pour les entrées de manuel.

#### mandb

mandb est utilisé pour initalisé ou mettre à jour manuellement le cache d'index de la base de données\
qui sont généralement maintenus par man. Le cache contient les informations\
pertinente sur l'état courante du système de page de manuel et les \
informations stockés dans celle-ci sont utilisé par l'utilitaire man-db pour\
améliorer leur vitesse et fonctionnalité.

La vérification de la consistance du cache peut ralentir sur les systèpe avec de nombreuses pages de manuel installées, donc mandb n'est pas exécuté par défaut, et les administrateurs système pourrait vouloir faire tourner mandb toutes les semaines pour garder le cache de base de données propre.

```
[root@centos7-1 ~]# mandb
Purging old database entries in /usr/share/man...
mandb: warning: /usr/share/man/man8/fsck.fat.8.manpage-fix.gz: ignoring bogus filename
Processing manual pages under /usr/share/man...
Purging old database entries in /usr/share/man/hu...
Processing manual pages under /usr/share/man/hu...
Purging old database entries in /usr/share/man/de...
Processing manual pages under /usr/share/man/de...
Purging old database entries in /usr/share/man/ja...

...

0 man subdirectories contained newer manual pages.
0 manual pages were added.
0 stray cats were added.
0 old database entries were purged.
```

**Options les plus utiles de la commande man:**

`-f, --whatis` Affiche une description courte de la page de manuel

```
[root@centos7-1 ~]# man -f ls
ls (1)               - list directory contents
ls (1p)              - list directory contents
```

`-w, --where, --location` N'affiche pas les pages de manuels, mais affiche les fichiers sources

```
[root@centos7-1 ~]# man -w ls
/usr/share/man/man1/ls.1.gz
```

`-k, --apropos` Equivalent à apropos. Recherche la description courte de la page de manuel pour les mots clés et affiche les correspondances.

```
[root@centos7-1 ~]# man -k echo
echo (1)             - display a line of text
echo (1p)            - write arguments to standard output
fcping (8)           - Fibre Channel Ping (ELS ECHO) tool
l2ping (1)           - Send L2CAP echo request and receive answer
lessecho (1)         - expand metacharacters
pam_echo (8)         - PAM module for printing text messages
ping (8)             - send ICMP ECHO_REQUEST to network hosts
ping6 (8)            - send ICMP ECHO_REQUEST to network hosts
```

### apropos

La commande `apropos` est utiliser pour rechercher et afficher une description courte des pages de manuel d'une commande comme suit :

```
[root@centos7-1 ~]# apropos echo
echo (1)             - display a line of text
echo (1p)            - write arguments to standard output
fcping (8)           - Fibre Channel Ping (ELS ECHO) tool
l2ping (1)           - Send L2CAP echo request and receive answer
lessecho (1)         - expand metacharacters
pam_echo (8)         - PAM module for printing text messages
ping (8)             - send ICMP ECHO_REQUEST to network hosts
ping6 (8)            - send ICMP ECHO_REQUEST to network hosts
```

C'est terminé!


## Exercices 

### Exercices guidés

* Utilisez le système man pour déterminer comment indiquer à apropos de fournir une commande brève afin qu’il affiche seulement un bref message d’utilisation et qu’il quitte ensuite.
<details>
  <summary>Réponse</summary>
    Lancez man apropos et faites défiler la section “Options” jusqu’au paragraphe --usage.
</details>

* Utilisez le système man pour déterminer quelle licence est attribuée à la commande grep.
<details>
  <summary>Réponse</summary>
    Lancez man grep et descendez jusqu’à la section “Copyright” du document. Notez que le programme utilise un copyright de la Free Software Foundation.
</details>

* Utilisez la commande export pour ajouter un nouveau répertoire à votre PATH (ceci ne survivra pas à un redémarrage).
<details>
  <summary>Réponse</summary>
    Vous pouvez ajouter temporairement un nouveau répertoire (par exemple un répertoire appelé myfiles qui se trouve dans votre répertoire personnel) à votre PATH en utilisant export PATH="/home/yourname/myfiles:$PATH". Créez un script simple dans le répertoire myfiles/, rendez-le exécutable, et essayez de le lancer depuis un autre répertoire. Ces commandes partent du principe que vous êtes dans votre répertoire personnel qui contient un répertoire appelé myfiles.

    $ touch myfiles/myscript.sh
    $ echo '#!/bin/bash' >> myfiles/myscript.sh
    $ echo 'echo Hello' >> myfiles/myscript.sh
    $ chmod +x myfiles/myscript.sh
    $ myscript.sh
    Hello
</details>

* Utilisez la commande unset pour supprimer la variable PATH. Essayez de lancer une commande en utilisant sudo (comme sudo cat /etc/shadow). Que s’est-il passé ? Pourquoi ? (Quittez votre shell pour rétablir le bon fonctionnement des choses.)
<details>
  <summary>Réponse</summary>
    En tapant unset PATH, vous supprimerez les paramètres actuels du PATH. Toute tentative d’invoquer un binaire sans son chemin complet va échouer. Pour cette raison, la tentative d’exécuter une commande en utilisant sudo (qui est lui-même un programme binaire situé dans /usr/bin/sudo) va échouer — à moins que vous ne spécifiez l’emplacement absolu, comme dans : /usr/bin/sudo /bin/cat /etc/shadow. Vous pouvez réinitialiser votre PATH en utilisant export ou en quittant simplement le shell.
</details>



### Exercices d'approfondissement

* Identifiez l’architecture matérielle et la version du noyau Linux utilisées sur votre ordinateur dans un format d’affichage facile à lire.
<details>
  <summary>Réponse</summary>
    Lancez man uname, lisez la section “Description”, et identifiez les options de commande qui vous permettront d’obtenir les résultats précis que vous souhaitez. Notez que -v vous fournira la version du noyau et -i la plateforme matérielle.

    $ man uname
    $ uname -v
    $ uname -i
</detail>

* Affichez les vingt dernières lignes de la base de données dynamique history et du fichier .bash_history et comparez les deux résultats.
<details>
  <summary>Réponse</summary>
    $ history 20
    $ tail -n 20 .bash_history
</details>

* Utilisez l’outil apropos pour identifier la page man où vous trouverez la commande dont vous aurez besoin pour afficher la taille d’un périphérique bloc matériel connecté en octets plutôt qu’en mégaoctets ou en gigaoctets.
<details>
  <summary>Réponse</summary>
    Une façon de procéder consiste à lancer apropos avec la chaîne block, lire le résultat, noter que lsblk affiche les périphériques de type bloc (et constitue donc l’outil le plus adapté à nos besoins), lancer man lsblk, faire défiler la section “Description” et noter que -b affiche la taille du périphérique en octets. Il ne reste qu’à lancer lsblk -b pour voir ce qui en ressort.

    $ apropos block
    $ man lsblk
    $ lsblk -b
</details>

* Faites des recherches sur Internet pour trouver et étudier la liste complète des caractères spéciaux.
<details>
  <summary>Réponse</summary>
    Voici la liste : & ; | * ? " ' [ ] ( ) $ < > { } # / \ ! ~.
</details>

* Essayez d’exécuter des commandes en utilisant des chaînes composées de caractères spéciaux et en utilisant différentes méthodes d’échappement. Est-ce que ces méthodes se comportent différemment ?
<details>
  <summary>Réponse</summary>
    L’échappement à l’aide des caractères " préservera les valeurs spéciales du signe dollar, de la quote inversée et de la barre oblique inversée. L’échappement à l’aide d’un caractère ', par contre, rendra tous les caractères littéralement.

    $ echo "$mynewvar"
    goodbye
    $ echo '$mynewvar'
    $mynewvar
</details>

.

.

.

sources:

[http://linuxcommand.org/lc3\_lts0010.php](http://linuxcommand.org/lc3\_lts0010.php)

[https://stackoverflow.com/questions/6697753/difference-between-single-and-double-quotes-in-bash](https://stackoverflow.com/questions/6697753/difference-between-single-and-double-quotes-in-bash)

[https://www.w3resource.com/linux-system-administration/control-operators.php](https://www.w3resource.com/linux-system-administration/control-operators.php)

[https://www.tecmint.com/set-unset-environment-variables-in-linux/](https://www.tecmint.com/set-unset-environment-variables-in-linux/)

[https://unix.stackexchange.com/questions/291729/why-is-the-default-symbol-for-a-user-shell-and-the-default-symbol-for-a-root](https://unix.stackexchange.com/questions/291729/why-is-the-default-symbol-for-a-user-shell-and-the-default-symbol-for-a-root)

[http://labor-liber.org/en/gnu-linux/introduction/index.php?diapo=input_output](http://labor-liber.org/en/gnu-linux/introduction/index.php?diapo=input_output)

[http://www.lostsaloon.com/technology/how-to-chain-commands-in-linux-command-line-with-examples/](http://www.lostsaloon.com/technology/how-to-chain-commands-in-linux-command-line-with-examples/)

[https://www.tecmint.com/chaining-operators-in-linux-with-practical-examples/](https://www.tecmint.com/chaining-operators-in-linux-with-practical-examples/)

[https://www.tecmint.com/echo-command-in-linux/](https://www.tecmint.com/echo-command-in-linux/)

[https://developer.ibm.com/tutorials/l-lpic1-103-1/](https://legacy.gitbook.com/book/borosan/lpic1-exam-guide/edit#)

[https://ss64.com/bash/syntax-quoting.html](https://ss64.com/bash/syntax-quoting.html)

[https://linuxconfig.org/learning-linux-commands-export](https://linuxconfig.org/learning-linux-commands-export)

[https://www.digitalocean.com/community/tutorials/how-to-read-and-set-environmental-and-shell-variables-on-a-linux-vps](https://www.digitalocean.com/community/tutorials/how-to-read-and-set-environmental-and-shell-variables-on-a-linux-vps)

[http://www.symkat.com/understanding-bash-history](http://www.symkat.com/understanding-bash-history)

[https://www.tecmint.com/history-command-examples/](https://www.tecmint.com/history-command-examples/)

[https://www.thegeekstuff.com/2008/08/15-examples-to-master-linux-command-line-history](https://www.thegeekstuff.com/2008/08/15-examples-to-master-linux-command-line-history)

[https://www.computerhope.com/unix/uuname.htm](https://www.computerhope.com/unix/uuname.htm)

[https://en.wikipedia.org/wiki/Man_page](https://en.wikipedia.org/wiki/Man_page)

[https://www.tecmint.com/view-colored-man-pages-in-linux/](https://www.tecmint.com/view-colored-man-pages-in-linux/)

[https://www.techonthenet.com/linux/commands/man.php](https://www.techonthenet.com/linux/commands/man.php)

[http://man7.org/linux/man-pages/man8/mandb.8.html](http://man7.org/linux/man-pages/man8/mandb.8.html)

[https://bash.cyberciti.biz/guide/Shopt](https://bash.cyberciti.biz/guide/Shopt)

.
