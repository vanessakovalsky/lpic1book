# 103.5. Création, contrôle et interruption des processus

**Poids:** 4

**Description:** Les candidats doivent être en mesure d'effectuer une gestion élémentaire des processu

**Connaissances clés:**

* Exécution de tâches au premier plan et en arrière plan.
* Indiquer à un programme qu'il doit continuer à s'exécuter après la déconnexion.
* Contrôle des processus actifs.
* Sélection et tri des processus à afficher.
* Envoi de signaux aux processus. 

**Concepts et Utilitaires:**

* &
* bg
* fg
* jobs
* kill
* nohup
* ps
* top
* free
* uptime
* pgrep
* pkill
* killall
* watch
* screen
* tmux

Dans cette section nous parlerons des tâches et des processus. D'ailleurs qu'est ce que c'est une tâche (job) ?

Si vous lancer des commande depuis le shelle, c'est une tâche. 

```
root@ubuntu16-1:~# ls
client.conf  files-backup            openvpn.key  unzip_6.0-20ubuntu1_amd64.deb
dead.letter  jcal_0.4.1-2_amd64.deb  test-space   zip_3.0-11_amd64.deb
```

Une tâche doit être exécuté et quitter juste après la fin et lancre pour un temps court seulement mais certaines tâches sont lancé pour une longue période.

```
root@ubuntu16-1:~# sleep 3600

```

Cela va dormir pour 3600 secondes, c'est une heure! Donc vous pouvez attendre pour une heure (en considérant que vous ne pouvez plus utiliser votre terminal) ou vous pouvez lancer une tâche en arrière plan (background).

### Tache de premier plan et d'arrière-plan (&)

 Une **tache est un processus que le shell gère**. Chaque tâche est assigné à un ID de tâche séquentiel. Puisqu'une tâche est un processus, chaque tâche est associé à un PID. Il y a deux types de statut de tâche:

1\. **Premier plan(Foreground)**: Lorsque vous entrez une commande dans une fenêtre de terminal, la commande occupe la fenêtre du terminal jusqu'à ce qu'elle soit complète. C'est une tâche en premier plan.\
 2\. **Arrière-plan(Background)**: Lorsque vous entrez le symbole esperluette (**&**) a la fin d'une ligne de commande, la commande tourne sans occuper votre fenêtre de terminal. L'invite de commande du shell (shell prompt) est affiché immédiatement après que vous ayez appuer sur Entrée. Voici un exemple de tâche en arrière-plan.

```
user1@ubuntu16-1:~$ sleep 5 &
[1] 68481
user1@ubuntu16-1:~$ 
[1]+  Done                    sleep 5
user1@ubuntu16-1:~$
```

> Par défaut, vous serez notifié lorsque la tâche se termine la prochaine fois que vous appuierez sur la touche Entrée. Essayer `set -b` pour être notifié immédiatement.

**Commande de contrôle des tâches**

La commande de contrôle des tâche nous permet de mettre les tâches en premier plan ou à l'arrière plan, et de démarrer ou d'arrêter les tâches. Ce tableau décrit les commandes de contrôles des tâchesThe table describes the job control commands.

| Command   | Description                                                                    |
| --------- | ------------------------------------------------------------------------------ |
| jobs      | Liste toutes les tâches                                                        |
| bg %n     | Positionnel la tâche courante ou spécifié en arrière plan, où n est l'ID de la tâche   |
| fg %n     | Ramène la tâche courante ou spécifié au premier plan, où n est l'ID de la tâche |
| Control-Z | Arrête la tâche en premier plan et la met à l'arrière plan comme une tâche arrêté      |
| Control-C | Ctrl+C tue le processus                                                       |

> Si aucun id de tâche n'est précisé pour bg et fg, il prendra la tâche la plus récente

### jobs

```
root@ubuntu16-1:~# jobs
[1]+  Running                 sleep 1000 &
root@ubuntu16-1:~# sleep 2000 &
[2] 54832
root@ubuntu16-1:~# jobs
[1]-  Running                 sleep 1000 &
[2]+  Running                 sleep 2000 &
root@ubuntu16-1:~# sleep 3000
^Z
[3]+  Stopped                 sleep 3000
root@ubuntu16-1:~# jobs
[1]   Running                 sleep 1000 &
[2]-  Running                 sleep 2000 &
[3]+  Stopped                 sleep 3000
```

### bg

```
root@ubuntu16-1:~# bg
[3]+ sleep 3000 &
root@ubuntu16-1:~# jobs
[1]   Running                 sleep 1000 &
[2]-  Running                 sleep 2000 &
[3]+  Running                 sleep 3000 &
```

Nous pouvons utiliser la commande `fg %3` et cela aura le même résultat. 

### fg

```
root@ubuntu16-1:~# jobs
[1]   Running                 sleep 1000 &
[2]-  Running                 sleep 2000 &
[3]+  Running                 sleep 3000 &
root@ubuntu16-1:~# fg %2
sleep 2000
^C
root@ubuntu16-1:~# jobs
[1]-  Running                 sleep 1000 &
[3]+  Running                 sleep 3000 &
root@ubuntu16-1:~# 
```

Nous mettons une tâche au premier plan et utilisons Ctrl + C pour tuer cette tâche. 

> Nous pouvons désactiver et activer les fonctionnalité de controle des tâches de shell avec la commande `set +m and set -m`.

Les tâches sont liés au shell qui les a lancé, donc elles sont tués lorsque le termina est fermé ou déconnecté. Parfois nous avons besoin de nous assurer que la tâche qui tourne n'est pas attaché au terminal courant. C'est la que nohup entre en jeu.

### Dire à un programme de continuer après la déconnexion

### nohup

`Nohup` signifie **no hangup**, et cela veut dire que même si le shell parent est déconnecté la tâche continuera. La sortie de la commande **nohup** sera écrite dans le fichier **nohup.out** si aucun nom de fichier pour une redirection dans la commande **nohup**.

```
nohup command [command-argument ...]
```

Utiliser nohup avec des commandes:

```
root@ubuntu16-1:~/test-space# nohup ping  8.8.8.8 &
[2] 55210
root@ubuntu16-1:~/test-space# nohup: ignoring input and appending output to 'nohup.out'

root@ubuntu16-1:~/test-space# ls 
nohup.out
root@ubuntu16-1:~/test-space# head -3 nohup.out 
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=128 time=97.2 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=128 time=74.4 ms
```

Maintenant même si nous fermons le terminal et l'ouvrons de nouveau, ping continue de tourner.

```
root@ubuntu16-1:~/test-space# jobs
root@ubuntu16-1:~/test-space# ps aux | grep ping | grep -v grep
root      55210  0.0  0.0   6536   756 ?        S    02:32   0:00 ping 8.8.8.8
root@ubuntu16-1:~/test-space# 
```

> Noter que nous ne pouvons plus utiliser les commandes `fg`, `bg` sur cette tâche particulière désormais.

 Lorsque nous lançons la commande nohup sans ‘**&’** alors il renvoit l'invite de commande du shell immédiatement après avoir lancer cette commande particulière à l'arrière plan.

> Nous utilisons généralement nohup avec la redirection de sortie `nohup bash script.sh > myresult.txt 2>&1`

{% hint style="info" %}

### screen

 La commande **screen** dans Linux permet de lancer et de voir plusieurs sessions shell depuis une unique session _ssh_. 

Lorsqu'un processus est démarré avec ‘screen’, le processus peut être détaché de la session et peut être réattacher à la session plus tard. Lorsque la session est détaché, le processus qui a été originellement démarré depuis le scree est encore en cours et géré par le scree lui même. Le processus peut être réattaché à la session plus tard, et les terminaux sont encore là, tel qu'ils ont été laissé. (Vous aurez peut être besoin de l'installer)

#### Démarrer screen pour la première fois

Utiliser simplement la commande screen  :

```
root@ubuntu16-1:~# screen
```

```
Screen version 4.03.01 (GNU) 28-Jun-15

Copyright (c) 2010 Juergen Weigert, Sadrul Habib Chowdhury
Copyright (c) 2008, 2009 Juergen Weigert, Michael Schroeder, Micah Cowan,
Sadrul Habib Chowdhury
Copyright (c) 1993-2002, 2003, 2005, 2006, 2007 Juergen Weigert, Michael
Schroeder
Copyright (c) 1987 Oliver Laumann

This program is free software; you can redistribute it and/or modify it under
the terms of the GNU General Public License as published by the Free Software
Foundation; either version 3, or (at your option) any later version.

This program is distributed in the hope that it will be useful, but WITHOUT
ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS
FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with
this program (see the file COPYING); if not, see http://www.gnu.org/licenses/,
or contact Free Software Foundation, Inc., 51 Franklin Street, Fifth Floor,

                  [Press Space for next page; Return to end.]
```

Utiliser` screen -s Session_Name` pour démarrer une session nommée session.now . Laisser une commande tournée dans screen:

```
root@ubuntu16-1:~# sleep 1111

```

> Afin de créer un nouveau écran à l'intérieur de l'ecran courant (écran nested) appuyez sur les touches  **Ctrl-a** + **c**

#### Détacher le screen

Un des avantages de screen est que vous pouvez le détacher. Puis, vous pouvez le restaurer sans rien perdre de ce que vous avez fait sur cet écran. Utiliser  **Ctrl-a + d **to detach**:**

```
root@ubuntu16-1:~# sleep 1111

```

```
root@ubuntu16-1:~# screen
[detached from 56798.pts-4.ubuntu16-1]
root@ubuntu16-1:~# 
```

 **-d**  est aussi utilisé pour détacher une session screen de sorte à ce qu'elle puisse être réattacher plus tard.

**Lister les screens**

 **screen  -ls** est utilisé pour afficher les screens actuellement ouvert en incluant ceux qui tourne à l'arrière plan :

```
root@ubuntu16-1:~# screen -list
There is a screen on:
	56798.pts-4.ubuntu16-1	(10/14/2019 04:15:00 AM)	(Detached)
1 Socket in /var/run/screen/S-root.
```

**Réattacher à un screen**

 **-r** Est utilisé pour réattacher une session screen qui a précédemment été détaché :

```
root@ubuntu16-1:~# screen -r 56798
```

```
root@ubuntu16-1:~# sleep 1111
root@ubuntu16-1:~# 

```

> Nous utilisons les commandes `screen -dr <Screen-ID>`. Cela signifie détache le screen spécifié en premier puis réatache le. 

#### Changer de screens

Lorsque vous faites des screen imbriqué, vous pouvez passer de l'un à l'autre en utilisant la commande **Ctrl-a +n**. Cela vous emmenera au prochain screen. Lorsque vous avez besoin d'aller au screen précédent, utiliser les touches **Ctrl-a** +**p**.

#### Terminer une session screen

Utiliser  “**Ctrl-A**” et “**K**” pour tuer le screen.


### Suivre les processus actifs

#### Qu'est ce qu'un processus?

Un programme est une série d'instruction qui dit à l'ordinateur ce qu'il doit faire. Lorsque nous lançons un programme, ces instructions sont copié en mémoire et de l'espace est alloué pour les variables et d'autres éléments nécessaire pour son exécution. Cette instance qui fonctionne d'un programme est appelé processus, et c'est un processus que nous pouvons gérer

Chaque processus reçoit un **PID**. PID signifie  **process identifier** et c'est un nombre unique qui identifie chaque processus en fonction sur le système d'exploitation.

Les processus peuvent être catégorisés en :

* **Processus Parent** – ce sont les processus qui créent d'autres processus pendant leur durée d'exécution.
* **Processus Enfant** – ces processus sont crées par d'autres processus durant leur durée d'exécution.

De plus **PPID** signifie **Parent Process ID**. Noter que:

* note1: Un PID peut être utilisé de nouveau pour un nouveau processus si toutes les combinaisons possibles sont utilisées.
* note2: A aucun moment, deux processus existe en même temps avec le même PID sur le système car c'est le PID que Unix utilise pour tracer chaque processus.

> Si nous utilisons la commande `jobs` avec l'option `-l`, elle montrera aussi les ID de processus.  

### ps

ps (Process status) peut être utilisé pour voir / lister tous les processus en cour et leurs PID abec d'autre informations qui dépendent des différentes options.

```
ps [options]
```

 ps lit les informations de processus depuis les fichiers virtuels dans le système de fichier **/proc**.  Dans sa forme la plus smimple, lorsque nous l'utilisons sans option, `ps` affiche quatre colonne d'information avec au minimum deux processus qui tourne dans le terminal courant, le shell lui-même et le processus qui tourne dans le shell lorsque la commande est invoqué.

```
root@ubuntu16-1:~# ps
   PID TTY          TIME CMD
 57301 pts/18   00:00:00 bash
 59076 pts/18   00:00:00 ps
```

 Où,\
 **PID –** l'ID unique du processus\
 **TTY –** le type du terminal dans lequel l'utilisateur est connecté\
 **TIME –** quantité de CPU en minutes et seconds que le processus a utilisé\
 **CMD –** nom de la commande qui a lancé le processus.

>  **Note –** Parfois, lorsque nous exécutons la commande **ps**, elle montre TIME as 00:00:00. Ce n'est rien d'autre que le total de emps d'utilisation du CPU pour chaque processus et 00:00:00 indique qu'aucun temps de CPU n'a été donné par le noyau jusqu'à maintenant. Dans l'exemple précédent, nous avons trouvé que, pour bash aucun temps de CPU n'a été donné. C'est parce que bash est juste le processus parent pour les différents processus qui ont besoin de bash pour leur exécution et bash lui-même n'a pas utilise de temps de CPU jusqu'à maintenant.

Généralement lorsque nous utilisons la commande `ps` nous ajoutons des paramètres comme `-a`, `-u` et` -x`. Où 

* `a` = montre les processus pour tous les utilisateurs
* `u` = montrer l'utilisateur qui a lancé le processus / son propriétaire
* `x` = montre aussi les processus qui ne sont pas attaché à un terminal

```
root@ubuntu16-1:~# ps -aux | head -10
USER        PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root          1  0.0  0.3 185244  3856 ?        Ss   Oct11   0:07 /sbin/init auto noprompt
root          2  0.0  0.0      0     0 ?        S    Oct11   0:00 [kthreadd]
root          4  0.0  0.0      0     0 ?        I<   Oct11   0:00 [kworker/0:0H]
root          6  0.0  0.0      0     0 ?        I<   Oct11   0:00 [mm_percpu_wq]
root          7  0.0  0.0      0     0 ?        S    Oct11   0:12 [ksoftirqd/0]
root          8  0.0  0.0      0     0 ?        I    Oct11   0:19 [rcu_sched]
root          9  0.0  0.0      0     0 ?        I    Oct11   0:00 [rcu_bh]
root         10  0.0  0.0      0     0 ?        S    Oct11   0:00 [migration/0]
root         11  0.0  0.0      0     0 ?        S    Oct11   0:01 [watchdog/0]
```

Où les colonnes sont :

*   **USER –** Spécifie l'utilisateur qui a exécuté le programme.

     **PID:** ID du processus, montrer le numéro d'identification du processus.

    **CPU%**: Le % de processeurs utilisé par ce processus.

    **MEME%**: Le % de mémoire utilisé par ce processus.

    **VSZ:** La taille virtuelle en kbytes.

    **RSS:** En opposition à la taille virtuelle, celle-ci montre la mémoire réelle utilisé par le processus.

    **TTY:** Identifie le terminal depuis lequel le processus a été exécuté.

    **STATE:** Montre les informations sur l'état du processus comme sa priorité, en lançant “man ps” vous verrez la signification des codes.

    **START:** Montre quand le processus à démarrer.

    **TIME:** Montre le temps d'occupation du processeur du programme.

    **C0MMAND:** Montre la commande utilisé pour lancer le processus.

> Nous pouvons aussi utiliser `ps -ef` au lieux de `ps aux` . Il n'y a **aucune différence** dans la sortie car le sens est le même. La **différence entre ps -ef and ps aux** est dû à des différents historiques **entre** les systèmes POSIX et BSD. Au début, POSIX accepté l'option -**ef** alors que BSD acceptait seulement la forme **aux**. Tous les deux listes tous les processus de tous les utilisateurs. De fait `-e` et `ax` sont complètement équivalent.

| Options pour la commande ps | Description                                   |
| ---------------------- | --------------------------------------------- |
| ps -T                  | Voir les processus associé à un terminal    |
| ps -x                  | Voir tous les processus dont vous êtes le propriétaire |
| ps -o  column_name     | Voir les processus en fonction du format définit par l'utilisateur |

Il est aussi possible d'utiliser l'option --sort pour trier la sortie en s'appuyant sur différents champs (+ pour ascendant & - pour descendant). `ps -eo  pid,ppid,cmd,%mem,%cpu --sort=-%mem | head -10` . Avec l'option `-o` ou `–format`, ps permet de construre un format de sorti personnalisé.

|  **Commande de séléction des processus**   | Description                             |
| --------------------------------- | --------------------------------------- |
| ps -C command_name                | Selectionner le processus par le nom de la commande. |
| ps p process_id                   | Voir le processus par son ID de processus.|
| ps -u user_name/ID                | Selectionner par nom d'utilisateur ou  ID  |
| ps -g group_name , ps -G group_id | Selectionner par nom de groupe ou ID              |
| ps -t pst/0                       | Afficher les processus par  TTY                |

Nous connaissons déjà la commande grep dans Linux, qui cherche un modle et affiche le texte correspondant dans la sortie. Et si le besoin est d'appliquer ce type de traitement opur récupérer les informations sélectionner sur un processus qui tourne actuellement sur le système ?

### pgrep

La commande `pgrep` recherche dans les processus qui tournent actuellement sur le système, **en se basant sur le nom complet ou partiel du processus**, ou d'autres attributs spécifiés.

```
pgrep [options] pattern
```

```
root@ubuntu16-1:~# sleep 1000 &
[1] 60647
root@ubuntu16-1:~# sleep 2000 &
[2] 60648
root@ubuntu16-1:~# pgrep sleep
60647
60648
```

> Utiliser toujours la commande `ps -ef` pour vérifier le nom du processus. Il y a une différente entre le process_name et le programme qui l'exécute (comme bash). Comparez `pgrep -a` et `pgrep -af`.

Les options de pgrep:

```
-d, --delimiter <string>  specify output delimiter
 -l, --list-name           list PID and process name
 -a, --list-full           list PID and full command line
 -v, --inverse             negates the matching
 -w, --lightweight         list all TID
 -c, --count               count of matching processes
 -f, --full                use full process name to match
 -g, --pgroup <PGID,...>   match listed process group IDs
 -G, --group <GID,...>     match real group IDs
 -n, --newest              select most recently started
 -o, --oldest              select least recently started
 -P, --parent <PPID,...>   match only child processes of the given parent
 -s, --session <SID,...>   match session IDs
 -t, --terminal <tty,...>  match by controlling terminal
 -u, --euid <ID,...>       match by effective IDs
 -U, --uid <ID,...>        match by real IDs
 -x, --exact               match exactly with the command name
 -F, --pidfile <file>      read PIDs from file
 -L, --logpidfile          fail if PID file is not locked
 --ns <PID>                match the processes that belong to the same
                           namespace as <pid>
 --nslist <ns,...>         list which namespaces will be considered for
                           the --ns option.
                           Available namespaces: ipc, mnt, net, pid, user, uts

 -h, --help     display this help and exit
 -V, --version  output version information and exit
```

> **Surveillance des processus en temps réel ?**
>
> En étant créatif et en utilisant des combinaisons avec d'autres commandes comme 'watch'. Nous pouvons utiliser 'watch' en conjonction de la commande ps pour faire du suivi de processus en temps réel  :
>
> `watch -n 1 'ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%mem | head'`

Mais il y a un autre outil pour ça, top.

###  top

 La commande **top** est utilisé pour voir les processus de Linux. Elle fournit une vue dynamique en temps réel du système en cours d'exécution. Généralement, cette commande montre les informations résumé du système et la liste des processus ou threads qui sont gérés par le noyau Linux.

```
root@ubuntu16-1:~# top
```

```
Tasks: 237 total,   1 running, 174 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.8 us,  0.4 sy,  0.0 ni, 98.7 id,  0.1 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :   985080 total,   167700 free,   501856 used,   315524 buff/cache
KiB Swap:  1045500 total,   448216 free,   597284 used.   256968 avail Mem 

   PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND    
 59562 root      20   0   41952   3604   2920 R 12.5  0.4   0:00.02 top        
     1 root      20   0  185244   3856   2244 S  0.0  0.4   0:07.76 systemd    
     2 root      20   0       0      0      0 S  0.0  0.0   0:00.01 kthreadd   
     4 root       0 -20       0      0      0 I  0.0  0.0   0:00.00 kworker/0:+
     6 root       0 -20       0      0      0 I  0.0  0.0   0:00.00 mm_percpu_+
     7 root      20   0       0      0      0 S  0.0  0.0   0:12.88 ksoftirqd/0
     8 root      20   0       0      0      0 I  0.0  0.0   0:19.92 rcu_sched  
     9 root      20   0       0      0      0 I  0.0  0.0   0:00.00 rcu_bh     
    10 root      rt   0       0      0      0 S  0.0  0.0   0:00.00 migration/0
```

Top continue à se mettre à jour jusqu'à ce que vous appuyez sur la touche ‘q‘.

Pù,

* **PID:** Montre l'id unique du processus
* **USER:** Nom de l'utilisateur propriétaire de la tâche.
* **PR:** Montre la priorité de la tâche.
* **NI:** Représente une Nice Value pour la tâche. Une nice value négative implique parfois une priorité plus haute et une nice value positive signifie une priorité plus basse.
* **VIRT:** Mémoire virtuelle totale utilisé par la tâche.
* **RES:** C'est la Resident size, la mémoire physique non-swapped que la tâche a utilisé.
* **SHR:** Représente la quanité de mémoire partagé utilisé par une tâche.
* **%CPU:** Représente l'utilisation du CPU.
* **%MEM:** Montre l'utilisation de la mémoire de la tâche.
* **TIME+:** Le temps de CPU, le même que ‘TIME’, mais avec plus de précisions avec les centièmes de secondes.
* **COMMAND:** Montre la commande utilisé pour lancer le processus.

| top command option    | description                                |
| --------------------- | ------------------------------------------ |
| top -n 10             | Sortie de la commande Top après une répétition n |
| top -u user1          | Affiche les processus d'un utilisateur spécifique |
| Top -d seconds.tenths | Donne un délai entre deux mise à jour de l'écran |
| top -h                | Montre la syntaxe de la commande top             |

| Running top command hot keys | Description                                                  |
| ---------------------------- | ------------------------------------------------------------ |
|  pressing ‘d‘                | Modifie l'intervale de rafraichissement de l'écran (default 3.0 sec)             |
| Pressing ‘z‘                 | Affiche les processus en cours en couleur                    |
| Pressing ‘c‘                 | Affiche les chemins complet des programmes en cours          |
| pressing ‘k‘                 | Tue un processus après avoir trouver le PID de ce processus (sans quitter) |
| Pressing 'M'                 | Tri basé sur l'utilisation de la mémoire                     |
| Shift+P                      | Tri par utilisation du CPU                                   |
|  Press ‘h‘                   | Obetnir l'aide de la commande top                            |

### Gérer les processus

Pour gérer les processus dans une machine linux nous pouvons envoyer des signaux aux processus. De nombreux signaux sont définis dans les noyaux Linux. (Essayez `man 7 signal`)

| Signal Name | Signal Number | Description                                                                                         |
| ----------- | ------------- | --------------------------------------------------------------------------------------------------- |
| SIGHUP      | 1             | Suspension détécté sur le terminal qui contrôle ou mort du processus qui le controle                |
| SIGINT      | 2             | Déclenché si l'utilisateur envoit un signal d'interruption (Ctrl + C)                               |
| SIGQUIT     | 3             | Déclenché si l'utilisateur envoit un signal d'arrêt (Ctrl + D)                                      |
| SIGKILL     | 9             | Si un processus reçoit ce signal il doit s'arrêter immédiatement et ne fera aucune opération de nettoyage |
| SIGTERM     | 15            | Signal de fin du logiciel (envoyé par défaut par kill)                                               |
| SIGCOUNT    | 18            | Continuer le processus arrêté avec STOP                                                              |
| STOP        | 19            | Arrêter un processsus                                                                                |

Pour envoyer des signaux au processus voici quelques commandes :

### kill

 La commande `kill` sous Linux (qui se trouve dans /bin/kill), est une commande fourni qui est utilisé pour terminer un processus manuellement. La commande _kill_ envoie un signal à un processus qui termine le processus.

```
kill {-signal | -s signal} pid 
```

Veuillez noter que:

* Un utilisateur peut tuer tous ses processus.
* Un utilisateur ne peut pas tuer les processus d'un autre utilisateur.
* Un utilisateur ne peut pas tuer les processus que le sytème utilise.
* Un utilisateur administrateur (root) peut tuer les processus de niveau système et les processus de n'importe quel utilisateur.

Note: Si l'utilisateur ne spécifie aucun signal à envoyé avec la commande kill,  **par défaut, le signal TERM** est envoyé pour terminer le processus.

```
root@ubuntu16-1:~# sleep 1000 &
[1] 61080
root@ubuntu16-1:~# sleep 2000 &
[2] 61081
root@ubuntu16-1:~# jobs -l
[1]- 61080 Running                 sleep 1000 &
[2]+ 61081 Running                 sleep 2000 &
root@ubuntu16-1:~# ps -ef | grep sleep
root      61080  57301  0 02:55 pts/18   00:00:00 sleep 1000
root      61081  57301  0 02:55 pts/18   00:00:00 sleep 2000
root      61085  57301  0 02:56 pts/18   00:00:00 grep --color=auto sleep
root@ubuntu16-1:~# kill 61080
root@ubuntu16-1:~# ps -ef | grep sleep
root      61081  57301  0 02:55 pts/18   00:00:00 sleep 2000
root      61089  57301  0 02:56 pts/18   00:00:00 grep --color=auto sleep
[1]-  Terminated              sleep 1000
root@ubuntu16-1:~# ps -ef | grep sleep
root      61081   1722  0 02:55 ?        00:00:00 sleep 2000
root      61096  55644  0 02:58 pts/17   00:00:00 grep --color=auto sleep
root@ubuntu16-1:~# kill -9 61081
root@ubuntu16-1:~# ps -ef | grep sleep
root      61100  55644  0 02:58 pts/17   00:00:00 grep --color=auto sleep
```

  Utiliser `kill -l` pour voir tous les finaux que vous pouvez envoyer en utilisant kill.

{% hint style="danger" %}
Il y a deux commandes utilisé pour tuer un processus :

* kill – Tue un processus par son ID
* killall,pkill – Tue un processus par son nom

Tuer un processus par nom peut être réellement dangereux. Avant d'envoyer le signal, vérifier quel proecessus correspond au critère en utilisant “pgrep -l”.
{% endhint %}

### killall

 `killall` est un outil pour terminer les processus en cours d'exécution sur votre système **basé sur leur nom**. _Par contraste, `kill` termine les processus en se basant sur leur Process ID number (PID)_. Comme `kill` , `killall` peut aussi envoyé des signaux spécifique du système au processus.

```
kill {-signal | -s signal} process_name 
```

note1: Le nom complet du processus doit être défini ( ex : sleep et pas sle ou slee).

note2:  Si aucun signal n'est spécifié, SIGTERM est envoyé.

```
root@ubuntu16-1:~# sleep 1000 &
[1] 61836
root@ubuntu16-1:~# sleep 2000 &
[2] 61837
root@ubuntu16-1:~# ps -ef | grep sleep | grep -v grep
root      61836  55644  0 03:46 pts/17   00:00:00 sleep 1000
root      61837  55644  0 03:46 pts/17   00:00:00 sleep 2000
root@ubuntu16-1:~# pkill sleep
[1]-  Terminated              sleep 1000
[2]+  Terminated              sleep 2000
root@ubuntu16-1:~# ps -ef | grep sleep | grep -v grep
```

| killall command example    | Description                                                            |
| -------------------------- | ---------------------------------------------------------------------- |
| killall -l                 | tous les signaux que la commande killall peut envoyé                   |
| killall -q process_name    | empeche killall de se plaindre si le processus spécifié n'existe pas   |
| killall -u \[user-name]    | Tue tous les processus d'un utilisateur                                |
| killall -o 5h              | Tue tous les processus qui sont en cours d'exécution depuis plus de  _5_ heures   |
| killall -y 4h              | Tue tous les processus qui sont agés de moins de 4 heures              |
| killall -w \[process-name] | Demande à `killall` d'attendre jusqu'à ce que le processus termine avant de sortir. |

### pkill

La commande PKill vous permet de tuer un programme simplement **en spécifiant son nom.**

```
pkill [options] pattern
```

note: Nous n'avons pas besoin de définir le nom complet du processus. Cela peut donc être très dangereux!

exemple:

```
root@ubuntu16-1:~# pgrep firefox
62256
root@ubuntu16-1:~# pkill firefox
```

| Exemple de commande pkill | Description                                      |
| ------------------------- | ------------------------------------------------ |
| pkill -c \[process_name]  | Renvoit le nombre de processus tués              |
| pkill -U \[real_user_ID]  | Tue tous les processus pour un utilisateur spécifique |
| pkill -G \[real_group_ID] | Tue tous les programe d'un groupe particulier    |

### free

 La commande **`free`** affiche la quantité total d'**espace libre** disponible avec la quantité de **mémoire utilisé** et la mémoire **swap** dans le système, et aussi les **tampons(buffers)** utilisés par le noyau.

```
free [options]
```

Comme free affiche les détails de la mémoire lié au système, sa syntaxe n'a pas besoin d'argument mais il a quelques options!

```
root@ubuntu16-1:~# free
              total        used        free      shared  buff/cache   available
Mem:         985080      432716      135464       16724      416900      339484
Swap:       1045500      671864      373636
```

La commande free sans option produit la sortie en colonne comme montré ci-dessus où les colonnes sont :

1. **total**: Affiche la mémoire totale installé _(MemTotal et SwapTotal i.e présente dans /proc/meminfo)._
2. **used :** Affiche la mémoire utilisé.
3. **free :**  Affiche la mémoire inutilisée.
4. **shared :** Affiche la mémoire utilisé par tmpfs_(Shmen i.e présente dans /proc/meminfo et affiche zero dans le cas ou elle n'est pas disponible)._
5. **buffers :** Affiche la mémoire utilisé par les tampons du noyau.
6. **cached :** Affiche la mémoire utilisé par le cache de page et slabs_(Cached et Slab disponible dans /proc/meminfo)._
7. **buffers/cache :** Affiche la comme des tampons et du cache.

 Par défaut, l'affichage est en kilobytes, mais vous pouvez le surcharger en utilisant `-b` pour bytes, `-k` pour kilobytes, `-m` pour megabytes, ou `-g` pour gigabytes.

```
root@ubuntu16-1:~# free -h
              total        used        free      shared  buff/cache   available
Mem:           961M        423M        128M         16M        410M        330M
Swap:          1.0G        655M        365M
```

 `-t` affiche une ligne aditionnele qui contient le total du total, les colonnes utilisé et libres:

```
root@ubuntu16-1:~# free -t -h
              total        used        free      shared  buff/cache   available
Mem:           961M        424M        127M         16M        410M        329M
Swap:          1.0G        655M        365M
Total:         1.9G        1.1G        492M
```

Autre options pour la commande free:

```
-h, --human         show human-readable output
     --si            use powers of 1000 not 1024
 -l, --lohi          show detailed low and high memory statistics
 -t, --total         show total for RAM + swap
 -s N, --seconds N   repeat printing every N seconds
 -c N, --count N     repeat printing N times, then exit
 -w, --wide          wide output

     --help     display this help and exit
 -V, --version  output version information and exit
```

### uptime

 La commande `uptime` montre un affichage en une ligne qui inclut la date et l'heure courante, depuis quand le système est lancé, combien d'utilisateurs sont actuellement connecté, et command shows you a one-line display that includes the current time, how long the system has been running, how many users are currently logged on, et la **moyenne de charge système** pour les 1, 5, et 15 minutes précédente.

```
uptime [-options]
```

```
root@ubuntu16-1:~# uptime 
 03:37:00 up 3 days, 19:07,  1 user,  load average: 0.00, 0.00, 0.00
```

Essayons` uptime -h` pour voir toutes les options disponible de `uptime`:

```
Usage:
 uptime [options]

Options:
 -p, --pretty   show uptime in pretty format
 -h, --help     display this help and exit
 -s, --since    system up since
 -V, --version  output version information and exit

For more details see uptime(1).
```

Exemples:

```
root@ubuntu16-1:~# uptime -p
up 3 days, 19 hours, 16 minutes
root@ubuntu16-1:~# uptime -s
2019-10-17 08:29:08
root@ubuntu16-1:~# uptime -V
uptime from procps-ng 3.3.10
```

.

.

.

.

[https://www.thegeekdiary.com/understanding-the-job-control-commands-in-linux-bg-fg-and-ctrlz/](https://www.thegeekdiary.com/understanding-the-job-control-commands-in-linux-bg-fg-and-ctrlz/)

[https://superuser.com/questions/662431/what-exactly-determines-if-a-backgrounded-job-is-killed-when-the-shell-is-exited](https://superuser.com/questions/662431/what-exactly-determines-if-a-backgrounded-job-is-killed-when-the-shell-is-exited)

[https://linuxhint.com/nohup_command_linux/](https://linuxhint.com/nohup_command_linux/)

[https://www.tecmint.com/run-linux-command-process-in-background-detach-process/](https://www.tecmint.com/run-linux-command-process-in-background-detach-process/)

[https://ryanstutorials.net/linuxtutorial/processes.php](https://ryanstutorials.net/linuxtutorial/processes.php)

[https://www.cyberciti.biz/faq/unix-linux-disown-command-examples-usage-syntax/](https://www.cyberciti.biz/faq/unix-linux-disown-command-examples-usage-syntax/)

[https://linuxize.com/post/ps-command-in-linux/](https://linuxize.com/post/ps-command-in-linux/)

[https://www.computerhope.com/jargon/p/pid.htm](https://www.computerhope.com/jargon/p/pid.htm)

[https://www.tecmint.com/linux-process-management/](https://www.tecmint.com/linux-process-management/)

[https://www.geeksforgeeks.org/processes-in-linuxunix/](https://www.geeksforgeeks.org/processes-in-linuxunix/)

[https://www.geeksforgeeks.org/ps-command-in-linux-with-examples/](https://www.geeksforgeeks.org/ps-command-in-linux-with-examples/)

****[https://linuxhint.com/ps_command_linux-2/](https://linuxhint.com/ps_command_linux-2/)

[https://www.quora.com/What-is-the-difference-between-ps-elf-and-ps-aux-in-Linux](https://www.quora.com/What-is-the-difference-between-ps-elf-and-ps-aux-in-Linux)

[https://www.geeksforgeeks.org/top-command-in-linux-with-examples/](https://www.geeksforgeeks.org/top-command-in-linux-with-examples/)

[https://www.tecmint.com/12-top-command-examples-in-linux/](https://www.tecmint.com/12-top-command-examples-in-linux/)

[https://www.tutorialspoint.com/unix/unix-signals-traps.htm](https://www.tutorialspoint.com/unix/unix-signals-traps.htm)

[https://www.geeksforgeeks.org/kill-command-in-linux-with-examples/](https://www.geeksforgeeks.org/kill-command-in-linux-with-examples/)

[https://www.linux.com/tutorials/how-kill-process-command-line/](https://www.linux.com/tutorials/how-kill-process-command-line/)

[https://www.linode.com/docs/tools-reference/tools/use-killall-and-kill-to-stop-processes-on-linux/](https://www.linode.com/docs/tools-reference/tools/use-killall-and-kill-to-stop-processes-on-linux/)

[https://www.geeksforgeeks.org/free-command-linux-examples/](https://www.geeksforgeeks.org/free-command-linux-examples/)

[https://www.geeksforgeeks.org/linux-uptime-command-with-examples/](https://www.geeksforgeeks.org/linux-uptime-command-with-examples/)

[https://linuxhint.com/load_average_linux/](https://linuxhint.com/load_average_linux/)

.
