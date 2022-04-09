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

the `pgrep` command searches for processes currently running on the system, **based on a complete** or** partial process name**, or other specified attributes.

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

> Always use ps -ef command to make sure about process_name. There is different between process_name and the running program(like bash). compare pgrep -a and pgrep -af.

pgrep options:

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

> **Real Time process monitoring ?**
>
> Be creative and use combination of  other commands like 'watch'. We can use 'watch' in conjunction with ps  command to perform Real-time Process Monitoring :
>
> `watch -n 1 'ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%mem | head'`

But there is another tool for that, top.

###  top

 **top** command is used to show the Linux processes. It provides a dynamic real-time view of the running system. Usually, this command shows the summary information of the system and the list of processes or threads which are currently managed by the Linux Kernel.

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

Top output keep refreshing until you press ‘q‘.

Where,

* **PID:** Shows task’s unique process id
* **USER:** User name of owner of task.
* **PR:** Stands for priority of the task.
* **NI:** Represents a Nice Value of task. A Negative nice value implies higher priority, and positive Nice value means lower priority.
* **VIRT:** Total virtual memory used by the task.
* **RES:**It is the Resident size, the non-swapped physical memory a task has used.
* **SHR:** Represents the amount of shared memory used by a task.
* **%CPU:** Represents the CPU usage.
* **%MEM:** Shows the Memory usage of task.
* **TIME+:** CPU Time, the same as ‘TIME’, but reflecting more granularity through hundredths of a second.
* **COMMAND:**Shows the command used to launch the process.

| top command option    | description                                |
| --------------------- | ------------------------------------------ |
| top -n 10             | Exit Top Command After Specific repetition |
| top -u user1          |  Display Specific User Process             |
| Top -d seconds.tenths | It tells delay time between screen updates |
| top -h                |  Shows top command syntax                  |

| Running top command hot keys | Description                                                  |
| ---------------------------- | ------------------------------------------------------------ |
|  pressing ‘d‘                | change screen refresh interval (default 3.0 sec)             |
| Pressing ‘z‘                 | display running process in color                             |
| Pressing ‘c‘                 | display absolute path of running program                     |
| pressing ‘k‘                 | kill a process after finding PID of process(without exiting) |
| Pressing 'M'                 | sort based on memory usage                                   |
| Shift+P                      |  Sort by CPU Utilisation                                     |
|  Press ‘h‘                   | Getting top command help                                     |

### Manage processes

To manage processes in a linux machine  we can send signals signals to the process.Many Signals are defined in the linux kernels. (try `man 7 signal`)

| Signal Name | Signal Number | Description                                                                                         |
| ----------- | ------------- | --------------------------------------------------------------------------------------------------- |
| SIGHUP      | 1             | Hang up detected on controlling terminal or death of controlling process                            |
| SIGINT      | 2             | Issued if the user sends an interrupt signal (Ctrl + C)                                             |
| SIGQUIT     | 3             | Issued if the user sends a quit signal (Ctrl + D)                                                   |
| SIGKILL     | 9             | If a process gets this signal it must quit immediately and will not perform any clean-up operations |
| SIGTERM     | 15            | Software termination signal (sent by kill by default)                                               |
| SIGCOUNT    | 18            | Continue the process stopped with STOP                                                              |
| STOP        | 19            | Stop process                                                                                        |

to send signals to processes there are some commands:

### kill

 `kill` command in Linux (located in /bin/kill), is a built-in command which is used to terminate processes manually . _kill_ command sends a signal to a process which terminates the process.

```
kill {-signal | -s signal} pid 
```

please notice that:

* A user can kill all his process.
* A user can not kill another user’s process.
* A user can not kill processes System is using.
* A root user can kill System-level-process and the process of any user.

note: If the user doesn’t specify any signal which is to be sent along with kill command then **default TERM signal** is sent that terminates the process.

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

  use `kill -l` to see all signals you can send using kill.

{% hint style="danger" %}
There are two commands used to kill a process:

* kill – Kill a process by ID
* killall,pkill – Kill a process by name

killing a proccess by name could be realy dangerous, Before sending signal,  verify which process is matching the criteria using “pgrep -l”.
{% endhint %}

### killall

 `killall` is a tool for terminating running processes on your system **based on name**. _In contrast, `kill` terminates processes based on Process ID number (PID)_. Like `kill` , `killall` can also send specific system signals to processes.

```
kill {-signal | -s signal} process_name 
```

note1:the whole process_name should be defined ( ex : sleep not sle or slee).

note2: If no signal name is specified, SIGTERM is sent.

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
| killall -l                 | all signals the killall command can send                               |
| killall -q process_name    | prevent killall from complaining if specified process doesn't exist    |
| killall -u \[user-name]    | kill all processes owned by a user                                     |
| killall -o 5h              | kill all processes that have now been running for more than _5_ hour   |
| killall -y 4h              | kill all precesses  that less than 4 hours old                         |
| killall -w \[process-name] |  causes `killall` to wait until the process terminates before exiting. |

### pkill

The PKill command allows you to kill a program simply **by specifying the name.**

```
pkill [options] pattern
```

note: We don't have to define whole process_name. So it could be really dangerous!

example:

```
root@ubuntu16-1:~# pgrep firefox
62256
root@ubuntu16-1:~# pkill firefox
```

| pkill command example     | Description                                      |
| ------------------------- | ------------------------------------------------ |
| pkill -c \[process_name]  | return a count of the number of processes killed |
| pkill -U \[real_user_ID]  | kill all the processes for a particular user     |
| pkill -G \[real_group_ID] | kill all the programs in a particular group      |

### free

 **`free`** command  displays the total amount of **free space** available along with the amount of **memory used** and **swap** memory in the system, and also the** buffers** used by the kernel.

```
free [options]
```

As free displays the details of the memory related to the system , its syntax doesn’t need any arguments to be passed but it has some options!

```
root@ubuntu16-1:~# free
              total        used        free      shared  buff/cache   available
Mem:         985080      432716      135464       16724      416900      339484
Swap:       1045500      671864      373636
```

free command with no options produces the columnar output as shown above where column:

1. **total **: displays the total installed memory _(MemTotal and SwapTotal i.e present in /proc/meminfo)._
2. **used :** displays the used memory.
3. **free :** displays the unused memory.
4. **shared :** displays the memory used by tmpfs_(Shmen i.epresent in /proc/meminfo and displays zero in case not available)._
5. **buffers :** displays the memory used by kernel buffers.
6. **cached :** displays the memory used by the page cache and slabs_(Cached and Slab available in /proc/meminfo)._
7. **buffers/cache :** displays the sum of buffers and cache.

 By default the display is in kilobytes, but you can override this using `-b` for bytes, `-k` for kilobytes, `-m` for megabytes, or `-g` for gigabytes.

```
root@ubuntu16-1:~# free -h
              total        used        free      shared  buff/cache   available
Mem:           961M        423M        128M         16M        410M        330M
Swap:          1.0G        655M        365M
```

 `-t`   displays an additional line containing the total of the total, used and free columns:

```
root@ubuntu16-1:~# free -t -h
              total        used        free      shared  buff/cache   available
Mem:           961M        424M        127M         16M        410M        329M
Swap:          1.0G        655M        365M
Total:         1.9G        1.1G        492M
```

Other free command options:

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

 The `uptime` command shows you a one-line display that includes the current time, how long the system has been running, how many users are currently logged on, and the **system load averages** for the past 1, 5, and 15 minutes.

```
uptime [-options]
```

```
root@ubuntu16-1:~# uptime 
 03:37:00 up 3 days, 19:07,  1 user,  load average: 0.00, 0.00, 0.00
```

Lets try` uptime -h` to see all of `uptime` availbale options:

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

examples:

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

{% hint style="info" %}
Processes deep dive ( Beyond the scope of LPIC1)

 **Types of Processes**

1. **Parent and Child process :** The 2nd and 3rd column of the ps –f command shows process id and parent’s process id number. For each user process there’s a parent process in the system, with most of the commands having shell as their parent.
2. **Zombie and Orphan process :** After completing its execution a child process is terminated or killed and SIGCHLD updates the parent process about the termination and thus can continue the task assigned to it. But at times when the parent process is killed before the termination of the child process, the child processes becomes orphan processes, with the parent of all processes “init” process, becomes their new ppid.\
    A process which is killed but still shows its entry in the process status or the process table is called a zombie process, they are dead and are not used.
3. **Daemon process :** They are system-related background processes that often run with the permissions of root and services requests from other processes, they most of the time run in the background and wait for processes it can work along with for ex print daemon.\
    When ps –ef is executed, the process with ? in the tty field are daemon processes

**States of a Process in Linux**

* **Running** – here it’s either running (it is the current process in the system) or it’s ready to run (it’s waiting to be assigned to one of the CPUs). use ps -r command.
* **Waiting** – in this state, a process is waiting for an event to occur or for a system resource. Additionally, the kernel also differentiates between two types of waiting processes; interruptible waiting processes – can be interrupted by signals and uninterruptible waiting processes – are waiting directly on hardware conditions and cannot be interrupted by any event/signal.
* **Stopped** – in this state, a process has been stopped, usually by receiving a signal. For instance, a process that is being debugged.
* **Zombie** – here, a process is dead, it has been halted but it’s still has an entry in the process table.

**Processes state codes in ps aux or ps -ef  command:**

* `R` running or runnable (on run queue)
* `D` uninterruptible sleep (usually IO)
* `S` interruptible sleep (waiting for an event to complete)
* `Z` defunct/zombie, terminated but not reaped by its parent
* `T` stopped, either by a job control signal or because it is being traced

Some extra modifiers:

* `<` high-priority (not nice to other users)
* `N` low-priority (nice to other users)
* `L` has pages locked into memory (for real-time and custom IO)
* `s` is a session leader
* `l` is multi-threaded (using CLONE_THREAD, like NPTL pthreads do)
* `+` is in the foreground process group
{% endhint %}

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
