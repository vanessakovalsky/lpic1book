# 103.4. Utilisation des flux, des tubes et des redirections

**Weight:** 4

**Description:** Les candidats doivent être en mesure de rediriger des flux et de les associer pour traiter efficacement des données textuelles. Ces tâches incluent les redirections de l'entrée standard, de la sortie standard et de l'erreur standard, la redirection de la sortie d'une commande vers l'entrée d'une autre, l'utilisation de la sortie d'une commande comme paramètres pour une autre commande et l'envoi de la sortie à la fois sur la sortie standard et dans un fichier. .

**Connaissances clés:**

* Redirection de l'entrée standard, de la sortie standard et de l'erreur standard.
* Connexion de la sortie d'une commande à l'entrée d'une autre commande.
* Utilisation de la sortie d'une commande comme paramètres d'une autre commande.
* Envoi simultané du résultat d'une commande vers la sortie standard et vers un fichier. 

**Concepts et Utilitaires:**

* tee
* xargs

Nous avons parler ds bases de l'enchainement et de la rédirection dans leçon précédente et celle-ci nous plongera plus en avant dans les techniques de Linux pour rediriger les flux IO standard.

### Entrée, sortie et flux <a href="input-output-and-streams" id="input-output-and-streams"></a>

En informatique, un flux est quelque chose qui peut transférer des données. Les flux de données, comme les courant marins ont deux fins. Ils ont une source et une destination. 

Dans un shell Linux, comme bash, on reçoit des entrée et on envoit des sortie comme séquequence ou flux de caractères. Les flux de caractères viennent ou vont dans un fichier, un clavier, une fenêtre d'affichage ou d'autres périphériques d'entrée/sortie (I/O) . 

stdin, stdout, et stderr sont les trois flux standard, il sont établi lorsqu'une commande Linux est exécutée. 

![](.gitbook/assets/usestreams-streams.jpg)

Les shell Linux utilise ces trois **flux** I/O standard, chacun d'eux est associé avec un **descripteur de fichier**:

* **stdout** est le flux standard de sortie, qui affiche la sortie des commandes(**descripteur de fichier** **1**)
* **stderr** est le flux standard des erreur, qui affiche les sortie d'erreurs depuis les commandes (**descripteur de fichier** **2**)
* **stdin** est le flux standard d'entrée, qui fournit les commandes saisies(**descripteur de fichier** **0**)

 Nous pouvons donc dire qu'il y a deux flux de sorties, `stdout` et `stderr`, et un flux d'entrée, `stdin`

**Qu'est ce qu'un descripteur de fichier?**

Les flux sont gérés comme des fichiers. Nous pouvons lire le texte depuis un fichier, et nous pouvons écrire du texte dans un fichier. Ces deux actions implique un flux de données. Donc le concept de gestion d'un flux de données en tant que fichier n'est pas si surprenant.

Chaque fichier associé avec un processus est associé à un nombre unique pour l'identifié.

```
STDIN - /proc/<processID>/fd/0
STDOUT - /proc/<processID>/fd/1
STDERR - /proc/<processID>/fd/2
```

 Cela est connu comme le **descripteur de fichier**. Lorsqu'une action doit être faite sur un fichier, le descripeur de fichier est utilisé pour identifié le fichier. Ces valeurs sont toujours utilisés pour `stdin`, `stdout,` et `stderr`:

* 0: stdin 
* 1: stdout 
* 2: stderr

Pour nous rendre la vie plus facile, le système a crée des raccourcis pour nous :

```
 root@ubuntu16-1:~/test-space/myfiles# ls -al /dev/std*
lrwxrwxrwx 1 root root 15 Dec  1  2018 /dev/stderr -> /proc/self/fd/2
lrwxrwxrwx 1 root root 15 Dec  1  2018 /dev/stdin -> /proc/self/fd/0
lrwxrwxrwx 1 root root 15 Dec  1  2018 /dev/stdout -> /proc/self/fd/1
```

### Rediriger les IO standards <a href="redirecting-standard-io" id="redirecting-standard-io"></a>

 Bien que le modèle pour les entrées et sorties standard est une série de flux de caractères depuis et vers un terminal, nous pouvons vouloir préparer les données d'entrée dans un fichier, ou enregistrer la sortie ou les erreurs dans un fichier. C'est là où **.** entre en jeu.

#### Rediriger la sortie <a href="redirecting-output" id="redirecting-output"></a>

Il y a deux manière de rediriger la sortie dans un fichier :

_**n**_ **>** redirige la sortie depuis le descripteur de fichier n vers un fichier. Nous devons avoir les droits d'écrire dans le fichier. Si le fichier n'existe pas, il est crée. S'il existe, son contenu existant est perdu sans avertissement.

_**n**_ **>>** redirige aussi la sortie depuis le descripteur de dichier n vers un fichier. Là aussi, nous devons avoir les droits d'écrire dans le fichier. Si le fichier n'existe pas, il est créé. S'il existe, la sortie est ajouté au fichier existant.

>  Le _n_ dans n> ou n>> fait référence au _descripteur de fichier_. S'il est omis, alors la sortie standard (descripteur de fichier 1) est supposé.

Les symboles de redirections ` >` et `>>` fonctionne par défaut avec  **stdout**. Nous pouvons utiliser un des descripteurs de fichiers numéroque pour indiquer quel flux de sortie standard nous souhaitons rediriger.

* **>** - standard output
* **2>** - standard error

">" redirige stdout

```
root@ubuntu16-1:~/test-space/myfiles# ls -l
total 1144
drwxr-xr-x 2 root root    4096 Sep 30 05:42 dir1
drwxr-xr-x 2 root root    4096 Oct  5 05:25 dir2
-rw-r--r-- 1 root root    2084 Sep 30 05:44 myconf.txt
-rw-r----- 1 root root 1156369 Sep 30 05:41 mylog.txt
root@ubuntu16-1:~/test-space/myfiles# ls -l > list
root@ubuntu16-1:~/test-space/myfiles# cat list 
total 1144
drwxr-xr-x 2 root root    4096 Sep 30 05:42 dir1
drwxr-xr-x 2 root root    4096 Oct  5 05:25 dir2
-rw-r--r-- 1 root root       0 Oct  9 01:42 list
-rw-r--r-- 1 root root    2084 Sep 30 05:44 myconf.txt
-rw-r----- 1 root root 1156369 Sep 30 05:41 mylog.txt
```

"2>" redirige stderr

```
root@ubuntu16-1:~/test-space/myfiles# ls -l BlahBlah
ls: cannot access 'BlahBlah': No such file or directory
root@ubuntu16-1:~/test-space/myfiles# ls -l BlahBlah 2> error
root@ubuntu16-1:~/test-space/myfiles# ls -l
total 1152
drwxr-xr-x 2 root root    4096 Sep 30 05:42 dir1
drwxr-xr-x 2 root root    4096 Oct  5 05:25 dir2
-rw-r--r-- 1 root root      56 Oct  9 01:43 error
-rw-r--r-- 1 root root     267 Oct  9 01:42 list
-rw-r--r-- 1 root root    2084 Sep 30 05:44 myconf.txt
-rw-r----- 1 root root 1156369 Sep 30 05:41 mylog.txt
root@ubuntu16-1:~/test-space/myfiles# cat error 
ls: cannot access 'BlahBlah': No such file or directory
```

**Ajout**

 La commande avec le double symbole _n'écrase pas_ le contenu existant de la destination, il ajoute le nouveau contenu.

* **>>** - standard output
* **2>>** - standard error

```
root@ubuntu16-1:~/test-space/myfiles# date
Sat Oct 12 02:52:04 PDT 2019
root@ubuntu16-1:~/test-space/myfiles# date > mydate
root@ubuntu16-1:~/test-space/myfiles# cat mydate
Sat Oct 12 02:52:10 PDT 2019
root@ubuntu16-1:~/test-space/myfiles# date > mydate 
root@ubuntu16-1:~/test-space/myfiles# cat mydate 
Sat Oct 12 02:53:00 PDT 2019
root@ubuntu16-1:~/test-space/myfiles# date >> mydate 
root@ubuntu16-1:~/test-space/myfiles# cat mydate 
Sat Oct 12 02:53:00 PDT 2019
Sat Oct 12 02:53:23 PDT 2019
```

{% hint style="info" %}
 **Ecraser ou ne pas écraser ?**

Nous l'avons dis la redirection de sortie qui utilise n> écrase le fichier existant. Vous pouvez le controller avec l'option `noclobber` du pré-construit `set`. Utiliser `set -C` pour activer noclobber(pas d'écrasement):

```
user1@ubuntu16-1:~$ set -o | grep noclobber
noclobber      	off
user1@ubuntu16-1:~$ ls
Desktop    Downloads         Music     Public     Videos
Documents  examples.desktop  Pictures  Templates
user1@ubuntu16-1:~$ ls > list1
user1@ubuntu16-1:~$ set -C
user1@ubuntu16-1:~$ set -o | grep noclobber
noclobber      	on
user1@ubuntu16-1:~$ ls > list1
bash: list1: cannot overwrite existing file
```

S'il a été défini, nous pouvons le surcharger en utilisant n>|

```
user1@ubuntu16-1:~$ ls >| list1
```

Utiliser `set +C` pour désactiver l'option.
{% endhint %}

**Rediriger à la fois la sortie standard et les erreurs standard**

Parfois, nous voulons rediriger à la fois la sortie standard et les erreurs standard dans un fichier. C'es souvent fait pour les process automatisés ou les taches en arrière plans pour que nous puissions consulter la sortie plus tard.

* Utiliser **&>** ou **&>>** pour rediriger à la fois la sortie standard et les erreurs standards au même endroit.

```
root@ubuntu16-1:~/test-space/myfiles# ls blah &>result
root@ubuntu16-1:~/test-space/myfiles# cat result 
ls: cannot access 'blah': No such file or directory
root@ubuntu16-1:~/test-space/myfiles# ls blah2 &>>result
root@ubuntu16-1:~/test-space/myfiles# cat result 
ls: cannot access 'blah': No such file or directory
ls: cannot access 'blah2': No such file or directory
```

Nous pouvons le faire d'une autre manière, rediriger le descripteur de fichier _n_ et rediriger le descripteur de fichier  _m_ au même endroit en utilisant la construction m>\&n ou m>>\&n.

* **&1** et **&2** et **&0** font référence à l'espace courant de **stdout** , **stderror** et **stdin**.

```
root@ubuntu16-1:~/test-space/myfiles# ls blah3 > result 2>&1
root@ubuntu16-1:~/test-space/myfiles# cat result 
ls: cannot access 'blah3': No such file or directory
```

{% hint style="info" %}
 L'ordre dans lequel les sorties sont redirigés est important. Par exemple,\
`command 2>&1 >output.txt`\
 n'est pas la même chose que\
`command >output.txt 2>&1`

Dans le premier cas, stderr est redirigé dans la localisation courante de stdout et puis stdout est redirigé vers output.txt, mais cette seconde redirection affecte seulement stdout, pas stderr. Dans le second cas, stderr est redirigé dans la localisation courante de stdout et ça dans output.txt.
{% endhint %}

**Rédiriger dans /dev/null**

A d'autres momens, nous pourrions vouloirs ignorer soit la sortie standard soit les erreurs standards. Pour ce faire, rediriger le flux approprié dans un fichier vide, /dev/null.

/dev/null est un fichier spécia qui appelle le périphérique null. Il est aussi appelé bit-bucket ou le trou noir (black-hole) car il annule immédiatement tout ce qui est écrit et retournce seulement une fin de fichier (end-of-file EOF) lorsqu'il est lu.

```
root@ubuntu16-1:~/test-space/myfiles# ls -l
total 1156
drwxr-xr-x 2 root root    4096 Sep 30 05:42 dir1
drwxr-xr-x 2 root root    4096 Oct  5 05:25 dir2
-rw-r--r-- 1 root root     170 Oct  9 02:25 error
-rw-r--r-- 1 root root      97 Oct  9 03:17 list
-rw-r--r-- 1 root root    2084 Sep 30 05:44 myconf.txt
-rw-r--r-- 1 root root      58 Oct 12 02:53 mydate
-rw-r----- 1 root root 1156369 Sep 30 05:41 mylog.txt
root@ubuntu16-1:~/test-space/myfiles# ls -l Blah 2>/dev/null
```

### Rediriger l'entrée

"<" redirige stdin 

```
root@ubuntu16-1:~/test-space/myfiles# cat list 
total 1144
drwxr-xr-x 2 root root    4096 Sep 30 05:42 dir1
drwxr-xr-x 2 root root    4096 Oct  5 05:25 dir2
-rw-r--r-- 1 root root       0 Oct  9 01:42 list
-rw-r--r-- 1 root root    2084 Sep 30 05:44 myconf.txt
-rw-r----- 1 root root 1156369 Sep 30 05:41 mylog.txt
root@ubuntu16-1:~/test-space/myfiles# wc < list 
  6  47 267
```

ou simplement wc 0< list fait la même chose.

### here documents

Un here document (ou heredoc) est une manière d'obtenir le texte à entré dans un script ou une commande sans avoir à le mettre dans un fichier séparé.

```
Command << HeredocDelimiter
. . .
. . .
. . .
HeredocDelimiter
```

**<<**  redirige l'entrée standard dans la commande et prend une chaine jusqu'au "HeredocDelimiter" .

 **Note:** Il ne doit pas y avoir d'espace entre le symbole << et la limite de la chaine. S'il y a un caractère entre la chaine et le symbole << alors le document ne fonctionnera pas.

```
root@ubuntu16-1:~/test-space/myfiles# cat << EOF > /tmp/myfile.txt
> Theis line will be written to the file.
>     this line is 2nd line
> this is last line.
> EOF
root@ubuntu16-1:~/test-space/myfiles# cat /tmp/myfile.txt 
Theis line will be written to the file.
    this line is 2nd line
this is last line.
```

> Rappel: "-" Un tiret (utilisé seul) signifie généralement que l'entrée proviendra de `stdin` plutôt que d'un fichier nommé. Essayez : cat - << EOF > interesting.txt

### here string

 **<<<** Redirige une seule ligne de texte dans le stdin d'une commande. C'est appelé un here-string.

```
cmd <<< "string"
```

### **Tubes**

Nous utilisons l'opérateur`  |  `(pipe ou tube en français) entre deux commands pour rediriger le stdout de la première dans le stdin de la seconde. **`command | command`**

```
root@ubuntu16-1:~/test-space/myfiles# ls -l
total 1156
drwxr-xr-x 2 root root    4096 Sep 30 05:42 dir1
drwxr-xr-x 2 root root    4096 Oct  5 05:25 dir2
-rw-r--r-- 1 root root     170 Oct  9 02:25 error
-rw-r--r-- 1 root root      97 Oct  9 03:17 list
-rw-r--r-- 1 root root    2084 Sep 30 05:44 myconf.txt
-rw-r--r-- 1 root root      58 Oct 12 02:53 mydate
-rw-r----- 1 root root 1156369 Sep 30 05:41 mylog.txt
root@ubuntu16-1:~/test-space/myfiles# ls -l | cut -f1 -d' '
total
drwxr-xr-x
drwxr-xr-x
-rw-r--r--
-rw-r--r--
-rw-r--r--
-rw-r--r--
-rw-r-----
root@ubuntu16-1:~/test-space/myfiles# ls -l | cut -f1 -d' ' | wc -l
8
```

Certaines commandes comme grep peut accepter une entrée comme paramètres, mais certaines commandes acceptes des argument

> Paramètre vs Argument: 
>
> * **Paramètre** est une variable dans la déclaration d'une fonction.
> * **Argument** est la valeur actuel de la variable qui est passé à la fonction.
>
>     > ```
>     >                  Parameters
>     >                    |     |
>     > function test ( param1 , param2 ){
>     >     return ( param1 + param2 );
>     >     }
>     > test (5, 6)
>     >       |  |
>     >      Arguments
>     > ```

### Utiliser la sortie comme arguments <a href="using-output-as-arguments" id="using-output-as-arguments"></a>

Dans la partie précédente sur les pipelines, nous avons appris comment prendre la sortie d'une commande et l'utiliser en tant qu'entrée dans une autre commande. A la place, que se passe t'il si nous voulons utiliser la sortie d'une commande ou le contenu d'un fichier comme argument d'une commande plutôt que comme entrée ?. Les Pipelines ne fonctionnent pas pour ça.

 Les trois méthodes communes sont :

1. La commande `xargs`
2. La commande `find` avec l'option `-exec` (section précédente)
3. La substitution de commande ( nous le verrons plus tard)

### xargs

**xargs** est une commande Unix qui peut être utiliser pour construire et exécuter des commande depuis l'entrée standard.

```
xargs [options] [command]
```

> Si aucune commande n'est spécifié, xargs exécute echo par défaut.

```
root@ubuntu16-1:~/test-space/myfiles# ls | xargs 
dir1 dir2 interesting.txt list myconf.txt mydate mylog.txt
```

 Il y a plusieurs manières pour lesquel **xargs** est utile au quotidien avec les lignes de commande.

![](.gitbook/assets/usestreams-xarg.jpg)

Nous pouvons remplacer des occurence de l'argument via l'option` -I` :

```
root@ubuntu16-1:~/test-space/myfiles# find . -type f | xargs -I FILE  echo this is my file FILE yes
this is my file ./dir1/myvideo.mp4 yes
this is my file ./interesting.txt yes
this is my file ./list yes
this is my file ./dir2/zabbix-cli-rpm-2.1.1-1.tar.gz yes
this is my file ./dir2/zabbix-cli-manual-1.7.0.pdf yes
this is my file ./mylog.txt yes
this is my file ./myconf.txt yes
this is my file ./mydate yes
```

`Il est aussi possible de lancer plusieurs commandes avec xargs `

cat a.txt | xargs -I % sh -c {command1; command2; ... }

```
root@ubuntu16-1:~/test-space# cat foo 
one
two
three

root@ubuntu16-1:~/test-space# cat foo | xargs -I % sh -c 'mkdir  %  ;echo  "directory % has been made" '
directory one has been made
directory two has been made
directory three has been made

root@ubuntu16-1:~/test-space# ls
foo  one  three  two
```

Avec l'option `-L`, l'entrée sera cassé par ligne et non pas espace. D'autres options:

```
xargs options :
-0 : input items are terminated by null character instead of white spaces
-a file : read items from file instead of standard input
-d,–delimiter = delim : input items are terminated by a special character
-E eof-str : set the end of file string to eof-str
-I replace-str : replace occurrences of replace-str in the initial arguments with names read from standard input
-L max-lines : use at-most max-lines non-blank input lines per command line.
-p : prompt the user about whether to run each command line and read a line from terminal.
-r : If the standard input does not contain any nonblanks, do not run the command
-x : exit if the size is exceeded.
–help : print the summary of options to xargs and exit
–version : print the version no. of xargs and exit
```

| xarg command example | Description                                                                |
| -------------------- | -------------------------------------------------------------------------- |
| xargs --version      | Prints the version number of xargs command                                 |
| xargs -a test.txt    | It will show contents of file                                              |
| xargs -p -a test.txt | -p option prompts for confirmation before running each command line.       |
| xargs -r -a test.txt | -r option ensures if standard input is empty, then command is not executed |

### tee

 Parfois, nous voulons voir la sortie sur l'écran et en enregistrer une copie pour plus tard. Nous pouvons le faire en redirigeant la sortie dans un fichier dans une fenêtre et en utilisant `tail -f `pour suivre dans une autre fenêtre, utiliser la commande `tee` est plus simple.

```
tee [OPTION]... [FILE]...
```

 **tee** lit l'entrée standard et l'écrit dans la sortie standard et dans un ou plusieurs fichiers.

![](.gitbook/assets/upstreams-tee.jpg)

tee fonctionne toujours après le '|' et il faut les deux tâches en simultanné. Par exemple :

```
root@ubuntu16-1:~/test-space/myfiles# ls  | tee list.txt
dir1
dir2
interesting.txt
list
list.txt
myconf.txt
mydate
mylog.txt
root@ubuntu16-1:~/test-space/myfiles# cat list.txt 
dir1
dir2
interesting.txt
list
list.txt
myconf.txt
mydate
mylog.txt
```

Avec l'option  **`-a`** il n'écrase pas le contenu du fichier mais l'ajoute au fichier donné.



.

.

.

[https://developer.ibm.com/tutorials/l-lpic1-103-4/](https://developer.ibm.com/tutorials/l-lpic1-103-4/)

[https://www.gnu.org/software/libc/manual/html_node/Streams-and-File-Descriptors.html](https://www.gnu.org/software/libc/manual/html_node/Streams-and-File-Descriptors.html)

[https://geek-university.com/linux/streams/](https://geek-university.com/linux/streams/)

[https://ryanstutorials.net/bash-scripting-tutorial/bash-input.php](https://ryanstutorials.net/bash-scripting-tutorial/bash-input.php)

[https://www.howtogeek.com/435903/what-are-stdin-stdout-and-stderr-on-linux/](https://www.howtogeek.com/435903/what-are-stdin-stdout-and-stderr-on-linux/)

[https://www.digitalocean.com/community/tutorials/an-introduction-to-linux-i-o-redirection](https://www.digitalocean.com/community/tutorials/an-introduction-to-linux-i-o-redirection)

[https://www.serverwatch.com/columns/article.php/3860446/Shell-Scripts-and-Here-Documents.htm](https://www.serverwatch.com/columns/article.php/3860446/Shell-Scripts-and-Here-Documents.htm)

[https://linuxhint.com/bash-heredoc-tutorial/](https://linuxhint.com/bash-heredoc-tutorial/)

[https://stackoverflow.com/questions/40230008/whats-the-usages-of-hyphen-in-linux-shell](https://stackoverflow.com/questions/40230008/whats-the-usages-of-hyphen-in-linux-shell)

[https://www.geeksforgeeks.org/xargs-command-unix/](https://www.geeksforgeeks.org/xargs-command-unix/)

[https://www.tecmint.com/xargs-command-examples/](https://www.tecmint.com/xargs-command-examples/)

.
