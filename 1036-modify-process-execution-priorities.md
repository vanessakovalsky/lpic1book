# 103.6. Modification des priorités des processus

**Poid:** 2

**Description:** Les candidats doivent être en mesure de gérer les priorités des processus. .

**Connaissances clés:**

* Connaissance de la priorité par défaut affectée à un nouveau processus.
* Exécution de programme avec une priorité plus haute ou plus basse que celle par défaut.
* Changement de la priorité d'un processus en cours d'exécution.

**Concepts et Utilitaires:**

* nice
* ps
* renice
* top

Linux, comme la plupart des systèmes d'exploitation modernes, peut lancer plusieurs processus. Il le fat en partageant le CPU et les autres ressources parmi les processus. Si un des processus utilise 100 pourcent du CPU, alors les autres pourrait ne plus répondre. Nous allons présenter la manière dont Linux assigne les priorités pour les tâches.(Nous avons déjà parlé des commandes ps et top dans la partie précédente)

### nice

 Dans Linux nous pouvons définir des lignes directrices à suivre pour le CPU lorsqu'il est utilisé par toutes les tâches. Ces lignes directrices sont appelées _**niceness**_ ou _**nice value.**_

```
top - 03:15:57 up 3 days, 20:17,  1 user,  load average: 0.03, 0.01, 0.00
Tasks: 235 total,   1 running, 171 sleeping,   0 stopped,   0 zombie
%Cpu(s):  2.1 us,  1.4 sy,  0.0 ni, 96.2 id,  0.3 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :   985080 total,   116124 free,   436040 used,   432916 buff/cache
KiB Swap:  1045500 total,   374404 free,   671096 used.   335984 avail Mem 

   PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND     
   949 root      20   0  496000  30668  15864 S  2.0  3.1  11:17.57 Xorg        
 55222 user1     20   0  667544  18964  12132 S  1.3  1.9   0:22.76 gnome-term+ 
  2112 user1     20   0 2346400  53212  24204 S  0.7  5.4  36:38.03 compiz      
  1916 user1     20   0  477868   5304   3464 S  0.3  0.5   0:07.53 ibus-ui-gt+ 
  1948 user1     20   0  188388   1384   1108 S  0.3  0.1   0:18.18 ibus-engin+ 
 55210 root      20   0    6536    600    572 S  0.3  0.1   0:19.60 ping        
 64405 root      20   0   41920   3760   3040 R  0.3  0.4   0:00.13 top         
     1 root      20   0  185244   3996   2500 S  0.0  0.4   0:08.10 systemd 
```

**Colonne NI** Représente la Nive value d'une tâche. L'échelle de priorité de Linux va de -20 à 19. Plus le nombre est bas, plus la prioriété est élevée pour une tâche. Si la valeur de priorité est un nombre haut comme 19 la tache sera défini comme ayant la priorité la plus basse et le CPU la traitera dès qu'il pourra. La valeur par défaut de priorité est **zero**.

![](.gitbook/assets/modifyprocess-nice.jpg)

Les différentes distibutions d'OS peuvent avoir des valeur par défaut différente pour les nouveaux processus. La méthode la plus simple pour déterminer la valeur par défaut est de lancer la commande nice sans argument. Par défaut nice renvoit simplement la valeur courant de priorité

```
root@ubuntu16-1:~# nice
0
```

#### Déterminer la valeur de priorité d'un processus en cours <a href="determining-the-niceness-value-of-a-current-process" id="determining-the-niceness-value-of-a-current-process"></a>

La valeur de priorité d'un processus en cours est aussi assez simple à trouver puisqu'elle est visible dans la commande ps avec le format long :

```
root@ubuntu16-1:~# sleep 11111 &
[1] 65128
root@ubuntu16-1:~# sleep 22222 &
[2] 65129
root@ubuntu16-1:~# ps -fl
F S UID         PID   PPID  C PRI  NI ADDR SZ WCHAN  STIME TTY          TIME CMD
4 S root      55643  55624  0  80   0 - 13594 wait   Oct21 pts/17   00:00:00 su -
4 S root      55644  55643  0  80   0 -  5725 wait   Oct21 pts/17   00:00:00 -su
4 S root      65014  55644  0  80   0 - 13594 wait   04:36 pts/17   00:00:00 su - user1
4 S root      65097  65015  0  80   0 - 13594 wait   04:45 pts/17   00:00:00 su - root
4 S root      65098  65097  0  80   0 -  5691 wait   04:45 pts/17   00:00:00 -su
0 S root      65128  65098  0  80   0 -  1822 hrtime 04:48 pts/17   00:00:00 sleep 11111
0 S root      65129  65098  0  80   0 -  1822 hrtime 04:48 pts/17   00:00:00 sleep 22222
0 R root      65133  65098  0  80   0 -  9341 -      04:49 pts/17   00:00:00 ps -fl
```

> `-f` affiche une liste au format complet et  `-l `est pour le format long.

#### Modifier la valeur de priorité d'un nouveau processus <a href="changing-the-nice-value-of-a-new-process" id="changing-the-nice-value-of-a-new-process"></a>

Modifier la valeur de priorité d'un nouveau processus est plutôt simple. La commande nice elle-même lance la commande souhaité avec le niveau souhaité de prioritéChanging.(Notez que nous sommes connecté comme utilisateur et pas comme administrateur ici)

```
user1@ubuntu16-1:~$ sleep 11111 &
[1] 65043
user1@ubuntu16-1:~$ nice -n 10 sleep 22222 &
[2] 65046
user1@ubuntu16-1:~$ nice -n 19 sleep 33333 &
[3] 65047
user1@ubuntu16-1:~$ nice -n -10 sleep 44444 &
[4] 65048
user1@ubuntu16-1:~$ nice: cannot set niceness: Permission denied
```

note: L'utilisateur root est la seule personne a pouvori démarrer une application avec une priorité plus haute (inférieur à zéro), mais n'importe qui peut démarrer une application avec une priorité plus basse (supérieur à zéro).

Si vous essayer de lancer une application avec une priorité plus haute sans les droits d'administration, cela créera une erreur et l'application démarrera avec la priorité zéro.

```
user1@ubuntu16-1:~$ ps -fl
F S UID         PID   PPID  C PRI  NI ADDR SZ WCHAN  STIME TTY          TIME CMD
0 S user1     55624  55222  0  80   0 -  5669 wait   Oct21 pts/17   00:00:00 bash
4 S user1     65015  65014  0  80   0 -  5677 wait   04:36 pts/17   00:00:00 -su
0 S user1     65043  65015  0  80   0 -  1822 hrtime 04:36 pts/17   00:00:00 sleep 11111
0 S user1     65046  65015  0  90  10 -  1822 hrtime 04:37 pts/17   00:00:00 sleep 22222
0 S user1     65047  65015  0  99  19 -  1822 hrtime 04:37 pts/17   00:00:00 sleep 33333
0 S user1     65048  65015  0  80   0 -  1822 hrtime 04:37 pts/17   00:00:00 sleep 44444
0 R user1     65085  65015  0  80   0 -  9341 -      04:40 pts/17   00:00:00 ps -fl
```

Essayons la dernière commande en utilisant root :

```
user1@ubuntu16-1:~$ su - root
Password: 
root@ubuntu16-1:~# nice -n -20 sleep 55555 &
[1] 65112
root@ubuntu16-1:~# ps -fl
F S UID         PID   PPID  C PRI  NI ADDR SZ WCHAN  STIME TTY          TIME CMD
4 S root      55643  55624  0  80   0 - 13594 wait   Oct21 pts/17   00:00:00 su -
4 S root      55644  55643  0  80   0 -  5725 wait   Oct21 pts/17   00:00:00 -su
4 S root      65014  55644  0  80   0 - 13594 wait   04:36 pts/17   00:00:00 su - user1
4 S root      65097  65015  0  80   0 - 13594 wait   04:45 pts/17   00:00:00 su - root
4 S root      65098  65097  0  80   0 -  5691 wait   04:45 pts/17   00:00:00 -su
4 S root      65112  65098  0  60 -20 -  1822 hrtime 04:45 pts/17   00:00:00 sleep 55555
0 R root      65113  65098  0  80   0 -  9341 -      04:45 pts/17   00:00:00 ps -fl
```

Il est bon de savoir que la commande nice a différentes syntaxes:

```
$ nice -n niceness-value [command args] 
OU
$ nice -niceness-value [command args] 	#it’s confusing for negative values
OU
$ nice --adjustment=niceness-value [command args]
```

| Exemple de commande nice  | Description      |
| --------------------- | ---------------- |
| nice --20 application | Priorité la plus haute |
| nice --15 application | Vraiment haut        |
| nice -10 application  | Moyen bas       |
| nice -19 application  | Le plus bas     |

#### Modifer la valeur de priorité d'un processus en cours <a href="changing-the-nice-value-of-a-running-process" id="changing-the-nice-value-of-a-running-process"></a>

### renice

Pour modifier la priorité d'un processus en cours vers une valeur négative nous utiliserons la commande `renice `.

```
renice value PID
```

Il est important de noter que changer la valeur de priorité d'un processus vers une valeur négative nécessite des droits d'administrations. Donner une priorité plus haute à un processus peut avoir des effets destructeur sur un système.

```
user1@ubuntu16-1:~$ sleep 88888 &
[1] 67534
user1@ubuntu16-1:~$ ps -alf
F S UID         PID   PPID  C PRI  NI ADDR SZ WCHAN  STIME TTY          TIME CMD
4 S root      55643  55624  0  80   0 - 13594 -      Oct22 pts/17   00:00:00 su -
4 S root      55644  55643  0  80   0 -  5725 -      Oct22 pts/17   00:00:00 -su
4 S root      65014  55644  0  80   0 - 13594 -      Oct22 pts/17   00:00:00 su - user1
4 S user1     65015  65014  0  80   0 -  5677 wait   Oct22 pts/17   00:00:00 -su
0 S user1     67534  65015  0  80   0 -  1822 hrtime 05:59 pts/17   00:00:00 sleep 88888
0 R user1     67535  65015  0  80   0 -  9341 -      05:59 pts/17   00:00:00 ps -alf
user1@ubuntu16-1:~$ renice 10 67534
67534 (process ID) old priority 0, new priority 10
user1@ubuntu16-1:~$ ps -alf
F S UID         PID   PPID  C PRI  NI ADDR SZ WCHAN  STIME TTY          TIME CMD
4 S root      55643  55624  0  80   0 - 13594 -      Oct22 pts/17   00:00:00 su -
4 S root      55644  55643  0  80   0 -  5725 -      Oct22 pts/17   00:00:00 -su
4 S root      65014  55644  0  80   0 - 13594 -      Oct22 pts/17   00:00:00 su - user1
4 S user1     65015  65014  0  80   0 -  5677 wait   Oct22 pts/17   00:00:00 -su
0 S user1     67534  65015  0  90  10 -  1822 hrtime 05:59 pts/17   00:00:00 sleep 88888
0 R user1     67539  65015  0  80   0 -  9341 -      05:59 pts/17   00:00:00 ps -alf
user1@ubuntu16-1:~$ renice 5 67534
renice: failed to set priority for 67534 (process ID): Permission denied
```

> L'utilisateur peut uniquement descendre le niveau de priorité.

```
user1@ubuntu16-1:~$ su - root
Password: 
root@ubuntu16-1:~# renice -1 67534
67534 (process ID) old priority 10, new priority -1
root@ubuntu16-1:~# ps -alf
F S UID         PID   PPID  C PRI  NI ADDR SZ WCHAN  STIME TTY          TIME CMD
4 S root      55643  55624  0  80   0 - 13594 wait   Oct22 pts/17   00:00:00 su -
4 S root      55644  55643  0  80   0 -  5725 wait   Oct22 pts/17   00:00:00 -su
4 S root      65014  55644  0  80   0 - 13594 wait   Oct22 pts/17   00:00:00 su - user1
4 S user1     65015  65014  0  80   0 -  5677 wait   Oct22 pts/17   00:00:00 -su
0 S user1     67534  65015  0  79  -1 -  1822 hrtime 05:59 pts/17   00:00:00 sleep 88888
4 S root      67541  65015  0  80   0 - 13594 wait   06:00 pts/17   00:00:00 su - root
4 S root      67542  67541  0  80   0 -  5691 wait   06:00 pts/17   00:00:00 -su
4 R root      67558  67542  0  80   0 -  9341 -      06:00 pts/17   00:00:00 ps -alf
```

Nous pouvons aussi utiliser l'option `-p` avant de donner le PID, mais ce n'est pas nécessaire.

| Exemple de commande renice  | Description   |
| ---------------------- | ---------------- |
| renice -20 -p PID      | Priorité la plus haute |
| renice -15 -p PID      | Priorité très haute        |
| renice 10 -p PID       | Moyen bas       |
| renice 19 -p PID       | La plus basse           |

.

.

.

[https://developer.ibm.com/tutorials/l-lpic1-103-6/](https://developer.ibm.com/tutorials/l-lpic1-103-6/)

[https://www.nixtutor.com/linux/changing-priority-on-linux-processes/](https://www.nixtutor.com/linux/changing-priority-on-linux-processes/)

[https://bencane.com/2013/09/09/setting-process-cpu-priority-with-nice-and-renice/](https://bencane.com/2013/09/09/setting-process-cpu-priority-with-nice-and-renice/)

[https://www.tecmint.com/set-linux-process-priority-using-nice-and-renice-commands/](https://www.tecmint.com/set-linux-process-priority-using-nice-and-renice-commands/)

.
