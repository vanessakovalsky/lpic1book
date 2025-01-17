# 103.2. Traiter des flux de texte en utilisant les filtres

## **103.2 Traiter des flux de texte en utilisant les filtres**

**Poids:** 3

**Description:** Les candidats doivent être capable d'appliquer des filtres sur des flux de texte.

**Connaissances clés:**

Envoyer des fichiers texte et des flux de sortis aux utilitaires de filtre de texte en utilisant les commandes UNIX standard qui se trouve dans le paquet GNU textutils

**Concepts et Utilitaires:**

* cat
* cut
* expand
* fmt
* head
* join
* less
* nl
* od
* paste
* pr
* sed
* sort
* split
* tail
* tr
* unexpand
* uniq
* wc

Tout dans linux tourne autour des flux de texte contenant des données particulières.

### Flux

Un flux n'est rien d'autre qu'une séquence de bytes qui sont passé d'un fichier, périphérique, ou programme à un autre.

L'entrée et la sortie dans l'environnement Linux est distributé au travers de trois flux (qui sont en fait des fichiers spéciaux).

Ces flux sont :

* **flux d'entrée standard (stdin)**, qui fournit les commandes entrées.
* **flux de sortie standard (stdout)**, qui affiche la sortie des commandes.
* **standard error stream (stderr)**, qui affiche la sortie des erreurs des commands.

Les flux sont aussi numérotés : **stdin (0)** ,**stdout (1)**, **stderr (2)**.

![](.gitbook/assets/processtxt-streams.jpg)

### Enchainement avec |

L'enchainement est un mécanisme pour envoyer des données d'un programme à un autre. L'opérateur que nous utilisons est ( | ) (se trouve sur la touche 6 sur les claviers Azerty ou à côté de la touche antislahs sur les claviers Qwerty). Ce que fait l'opérateur est de récupérer la sortie du programme sur la gauche et de le donner comme entrée au programme sur la droite.

```
command1 | command2
```

D'autres commande peuvent avoir des options ou des arguments. Nous pouvons aussi utiliser | pour rediriger la sortie d'une secone commande dans le conduit de la troisème commande et continuer.

```
command 1 | command 2 | command3 | command 4 | ...
```

Construire de long enchainemenst de commandes qui ont chacune des capacités limités ets une manière commune dans Linux et Unix d'accomplir des tâches.

```
[root@centos7-1 ~]# dmesg | less
```

### Redirection 

Linux inclut des commande de redirection poru chaque flux. Nous pouvons utiliser `>` afin de rediriger le flux de sortie (la plupart du temps dans un fichier).

```
[root@centos7-1 temp]# ls -1
test.txt
unexpanded.txt
zip-3.0-11.el7.x86_64.rpm
zip.cpio

[root@centos7-1 temp]# ls -1 > list.txt

[root@centos7-1 temp]# cat list.txt 
list.txt
test.txt
unexpanded.txt
zip-3.0-11.el7.x86_64.rpm
zip.cpio
```

> "|" vs ">"
>
> La différence entre  > (opérateur de redirection) et | (opérateur d'enchainement(pipeline operator)) est que lorsque >  connecte une commande à un fichier, le | connecte la sortie d'une commande à une autre commande.

### Filtrage de texte

Le filtrage de texte est le processus de récupération d'un flux d'entrée de texte et d'effectuer des modification du texte avant de l'envoyer à un flux de sortie.

## cat

La commande **cat **(abreviation de “**concatenate**“) est une des commandes les plus uutilisés dans les systèmes d'exploitation de type Linux / Unix. La commande `cat` nous permet de créer un ou plusieurs fichiers, de voir le contenu fu fichier, de concaténer des ficheir et de rediriger la sortie dans un terminal ou un fichier.

```
cat [OPTION] [FILE]...
```

L'utilisation la plus simple de cat est l'affichage du contenu d'un fichier :

```
[root@centos7-1 ~]# cat file1
This is 1st line of file1.

This is 3rd line of file1.
```

Elle peut montrer le contenu de plusieurs fichiers :

```
[root@centos7-1 ~]# cat file1 file2
This is 1st line of file1.

This is 3rd line of file1.
This is 1st line of file1.
This is 2nd line of file2.

This is 4th line of file2.
```

La commande cat peut aussi être utilisée pour concaténer plusieurs fichiers ensemble :

```
[root@centos7-1 ~]# cat file1 file2 > newfile
[root@centos7-1 ~]# cat newfile 
This is 1st line of file1.

This is 3rd line of file1.
This is 1st line of file2.
This is 2nd line of file2.

This is 4th line of file2.
```

Créer un nouveau fichier avec cat :

```
[root@centos7-1 ~]# cat > newfile2
This is my second new file with input redirection
Ctrl+d
[root@centos7-1 ~]# cat newfile2
This is my second new file with input redirection
```

> "-" Un tiret (utilisé seul) signifie généralement que l'entrée sera pris de stdin en opposition à un fichier nommé:
>
> ```
> [root@centos7-1 ~]# cat file1 - file2
> This is 1st line of file1.
>
> This is 3rd line of file1.
> THIS IS MY INPUT 
> Ctrl+d
> This is 1st line of file2.
> This is 2nd line of file2.
>
> This is 4th line of file2.
> ```

Liste des options de la commande cat :

```
  -A, --show-all           equivalent to -vET
  -b, --number-nonblank    number nonempty output lines, overrides -n
  -e                       equivalent to -vE
  -E, --show-ends          display $ at end of each line
  -n, --number             number all output lines
  -s, --squeeze-blank      suppress repeated empty output lines
  -t                       equivalent to -vT
  -T, --show-tabs          display TAB characters as ^I
  -u                       (ignored)
  -v, --show-nonprinting   use
```

Maintenant quel est l'inverse de cat? Oui c'est ‘tac‘. `tac`  est une commande sous Linux, essyez la vous même.

## od

La commande od (Octal dump) dans Linux est utilisé pour afficher le contenu d'un fichier dans différents formats avec par défaut le format octal.\
Cette commande est très utile lorsque vous débugguer des scripts Linux pour les modifications non souhaités ou les caractères.

```
od [OPTION]... [FILE]...
```

Par exemple :

```
[root@centos7-1 ~]# cat testod.txt 
1
2
3
4
5
[root@centos7-1 ~]# od testod.txt 
0000000 005061 005062 005063 005064 005065
0000012
```

Avec l'option `-t` nous pouvons sélectionner le format de sortie et l'afficher. (Traditionnement les spéceifications de formats peuvent être mélangé entre elle):

```
-a same as -t a, select named characters, ignoring high-order bit
-b same as -t o1, select octal bytes
-c same as -t c, select printable characters or backslash escapes
-d same as -t u2, select unsigned decimal 2-byte units
-f same as -t fF, select floats
-i same as -t dI, select decimal ints
-l same as -t dL, select decimal longs
-o same as -t o2, select octal 2-byte units
-s same as -t d2, select decimal 2-byte units
-x same as -t x2, select hexadecimal 2-byte units
```

exemple:

```
[root@centos7-1 ~]# od -ta testod.txt 
0000000   1  nl   2  nl   3  nl   4  nl   5  nl
0000012
[root@centos7-1 ~]# od -tc testod.txt 
0000000   1  \n   2  \n   3  \n   4  \n   5  \n
0000012
```

`-A` Option pour afficher le contenu de l'entrée dans un format différent en concaténant certains caractères spéciaux (offsets).

* **Hexadecimal (en utilisant -x associé à -A)**
* **Octal (en utilisant -o associé à -A)**
* **Decimal (en utilisant -d associé à -A)**

```
[root@centos7-1 ~]# od -Ax -c testod.txt 
000000   1  \n   2  \n   3  \n   4  \n   5  \n
00000a
[root@centos7-1 ~]# od -Ao -c testod.txt 
0000000   1  \n   2  \n   3  \n   4  \n   5  \n
0000012
[root@centos7-1 ~]# od -Ad -c testod.txt 
0000000   1  \n   2  \n   3  \n   4  \n   5  \n
0000010
```

`-An` Opion pour afficher le contenu de l'entrée dans un format caractère mais sans information sur l'offset :

```
[root@centos7-1 ~]# od -An -c testod.txt 
   1  \n   2  \n   3  \n   4  \n   5  \n
```

## expand et unexpand

La commande **expand**  est utilisé pour convertir des tabulations en espace dans des fichiers.

```
 expand [OPTION]... [FILE]...
```

Essayons la :

```
[root@centos7-1 ~]# cat test.txt 
this    is    my    test    file.
[root@centos7-1 ~]# od -tc -An test.txt 
   t   h   i   s  \t   i   s  \t   m   y  \t   t   e   s   t  \t
   f   i   l   e   .  \n
[root@centos7-1 ~]# expand test.txt > expanded.txt
[root@centos7-1 ~]# od -tc -An expanded.txt 
   t   h   i   s                   i   s                        
   m   y                           t   e   s   t                
   f   i   l   e   .  \n
```

Par défaut, `expand` convertit les tabulation dans le nombre correspondant d'espace. Mis il est possible de modifier le nombre d'espace en utilisant l'option de ligne de commande `-t` (– – tabs=N). Cette option nécessite que nous entrions le nouveau nombre d'espaces(N) que nous voulons pour la conversion des tabulations.

```
[root@centos7-1 ~]# expand -t1 test.txt > expanded2.txt
[root@centos7-1 ~]# od -tc -An expanded2.txt 
   t   h   i   s       i   s       m   y       t   e   s   t    
   f   i   l   e   .  \n
```

Options de la commance expand :

```
  -i, --initial       do not convert tabs after non blanks
  -t, --tabs=NUMBER   have tabs NUMBER characters apart, not 8
  -t, --tabs=LIST     use comma separated list of explicit tab positions
      --help     display this help and exit
      --version  output version information and exit
```

La commande **unexpand** est utilisé pour convertir les caractères espaces (blancs) en tabulation dans chaque fichier (unexpand a besoin d'au moins deux espaces).

```
unexpand [OPTION]... [FILE]...
```

Faisons l'inverse:

```
[root@centos7-1 ~]# unexpand expanded.txt > unexpanded.txt
[root@centos7-1 ~]# od -tc -An unexpanded.txt 
   t   h   i   s                   i   s                        
   m   y                           t   e   s   t                
   f   i   l   e   .  \n
```

unexpand sans option prend juste l'espace initial!!! L'option `-a` convertit tous les blancs, au lieu du seul blanc initial:

> unexpand convertit seulement les doubles espace ou plus en tabulation, il ne convertit pas les espaces simple!

```
[root@centos7-1 ~]# unexpand -a expanded.txt > unexpanded2.txt
[root@centos7-1 ~]# od -tc -An unexpanded2.txt 
   t   h   i   s  \t   i   s  \t   m   y  \t   t   e   s   t  \t
   f   i   l   e   .  \n
```

Les options de la commande unexpand :

```
  -a, --all        convert all blanks, instead of just initial blanks
      --first-only  convert only leading sequences of blanks (overrides -a)
  -t, --tabs=N     have tabs N characters apart instead of 8 (enables -a)
  -t, --tabs=LIST  use comma separated LIST of tab positions (enables -a)
      --help     display this help and exit
      --version  output version information and exit
```

## Commande tr

tr signifie **translate**. L'utilitaire `tr` copie l'entrée standard dans la sortie standard avec le remplacement ou la suppression des caractères selectionnés. La syntaxe de la commande `tr` est :

```
tr [option] set1 [set2]
```

Convertissons les miniscules en majuscules :

```
[root@centos7-1 ~]# echo "this is  for   test 123" | tr abcdefghijklmnopqrstuvwxyz ABCDEFGHIJKLMNOPQRSTUVWXYZ
THIS IS  FOR   TEST 123
```

La commande suivante convertira aussi de miniscule vers majuscule:

```
[root@centos7-1 ~]# echo "this is  for   test 123" | tr [:lower:] [:upper:]
THIS IS  FOR   TEST 123
```

Traduire les espace blancs en tabulations :

```
[root@centos7-1 ~]# echo "this is  for   test 123" | tr [:space:] '\t'
this    is        for            test    123
```

S'il y a deux espaces ou plus présent côté à côté, alors la commande précédente transformera chaque espace en tabulation. Nous pouvons utiliser l'option `-s` pour éviter les répétition de caractères :

```
[root@centos7-1 ~]# echo "this is  for   test 123" | tr -s [:space:] '\t'
this    is    for    test    123
```

L'option `-d ` peut être utilisé pour supprimé les caractères spécifiés :

```
[root@centos7-1 ~]# echo "this is  for   test 123" | tr -d 't'
his is  for   es 123
[root@centos7-1 ~]# echo "this is  for   test 123" | tr -d [:digit:]
this is  for   test
```

Nous complétons l'ensemble en utilisant l'option `-c `. Par exemple, pour supprimer tous les caractères saufs les chiffres, vous pouvez utiliser la commande suivante :

```
[root@centos7-1 ~]# echo "this is  for   test 123" | tr -dc [:digit:]
123
```

tr a de nombreuses options. Utiliser tr --help pour plus d'informations.

## pr

La commande pr est utilisé pour formater les fichiers pour l'impression. L'entête par défaut inclut le nom du fichier et la date et l'heure de création du fichier, ainsi qu'un numéro de page et deux lignes blanche en pas de page.

```
[root@centos7-1 ~]# cat note.txt 
hi 
this is my note file.
linux is an operating system.
learn linux.

[root@centos7-1 ~]# pr note.txt 


2018-12-19 11:25                     note.txt                     Page 1


hi 
this is my note file.
linux is an operating system.
learn linux.
```

**Note:** Lorsque la sortie est créé à partir de plusieurs fichier ou du flux d'entrée standard, la date courrante et l'heure sont utilisé au lieu du nom du fichier et de la date de création.

Nous pouvons imprimer les ficheirs côté-à-côte en colonnes et controler de nombreux aspects du formatage avec les optionss.

```
[root@centos7-1 ~]# rpm -qa | pr --columns=2 -l 15
2018-12-19 11:35                                                  Page 1


NetworkManager-team-1.8.0-9.el7.x86 setserial-2.17-33.el7.x86_64
cdparanoia-libs-10.2-17.el7.x86_64  mozilla-filesystem-1.9-11.el7.x86_6
gtkmm30-3.22.0-1.el7.x86_64         hypervkvpd-0-0.30.20161211git.el7.x
python-configshell-1.1.fb23-3.el7.n less-458-9.el7.x86_64
khmeros-base-fonts-5.0-17.el7.noarc libgsf-1.14.26-7.el7.x86_64
liberation-fonts-common-1.07.2-15.e mariadb-libs-5.5.56-2.el7.x86_64
kexec-tools-2.0.14-17.el7.x86_64    rootfiles-8.1-11.el7.noarch
iptables-1.4.21-18.0.1.el7.centos.x langtable-data-0.0.31-3.el7.noarch
gnome-dictionary-libs-3.20.0-1.el7. libcgroup-tools-0.41-13.el7.x86_64
gd-2.0.35-26.el7.x86_64             perl-Pod-Usage-1.63-3.el7.noarch
```

`--column` définit le nombre de colonne créé dans la sortie.
`-l` définit la longueur de la page (par défaut 66 lignes). Comme d'habitude, utiliser la page de  **man** pour les détails.

## nl

nl est une commande linux pour numéroter les lignes de fichiers, il copie les fichiers dans la sortie standard en le préfixant avec des numéro de ligne.

```
nl [OPTION]... [FILE]...
```

```
[root@centos7-1 ~]# nl note.txt 
     1    hi 
     2    this is my note file.
     3    linux is an operating system.
```

`-n Format` Utilise la valeur de la variable Format comme format du nombre de ligne. Les formats connus sont :

* ln : aligné à gauche,
* rn : aligné à droite suppression des zero 
* rz: aligné à droite, les zéro sont conservés

```
[root@centos7-1 ~]# nl -nln note.txt 
1         hi 
2         this is my note file.
3         linux is an operating system.

[root@centos7-1 ~]# nl -nrn note.txt       #default
     1    hi 
     2    this is my note file.
     3    linux is an operating system.

[root@centos7-1 ~]# nl -nrz note.txt 
000001    hi 
000002    this is my note file.
000003    linux is an operating system.
```

> Par défaut nl saute les lignes vides et ne leur donne pas de numéro, utiliser l'option -ba pour leur assigner des numéros.

Autres options de ln :

```
  -b, --body-numbering=STYLE      use STYLE for numbering body lines
  -d, --section-delimiter=CC      use CC for separating logical pages
  -f, --footer-numbering=STYLE    use STYLE for numbering footer lines
  -h, --header-numbering=STYLE    use STYLE for numbering header lines
  -i, --line-increment=NUMBER     line number increment at each line
  -l, --join-blank-lines=NUMBER   group of NUMBER empty lines counted as one
  -n, --number-format=FORMAT      insert line numbers according to FORMAT
  -p, --no-renumber               do not reset line numbers at logical pages
  -s, --number-separator=STRING   add STRING after (possible) line number
  -v, --starting-line-number=NUMBER  first line number on each logical page
  -w, --number-width=NUMBER       use NUMBER columns for line numbers
      --help     display this help and exit
      --version  output version information and exit
```

`cat -n filename` fait la même chose que la commande`  nl  `.

## fmt

fmt est un formateur simple optimal de texte, il reforme les paragrpahe du fichier spécifié et affiche le résultat sur la sortie standard.

```
fmt [-WIDTH] [OPTION]... [FILE]...
```

```
[root@centos7-1 ~]# cat note.txt 
hi 
this is my note file.
linux is an operating system.
learn linux.

[root@centos7-1 ~]# fmt note.txt 
hi this is my note file.  linux is an operating system.  learn linux.
```

Par défaut, fmt définit la largeur des colonnes à 75. Cela peut être modifié avec l'option -w , --width=WIDTHoption.

```
[root@centos7-1 ~]# fmt -w 12  note.txt 
hi this
is my
note file.
linux is an
operating
system.
learn
linux.
```

Options de la commande fmt:

```
  -c, --crown-margin        preserve indentation of first two lines
  -p, --prefix=STRING       reformat only lines beginning with STRING,
                              reattaching the prefix to reformatted lines
  -s, --split-only          split long lines, but do not refill
  -t, --tagged-paragraph    indentation of first line different from second
  -u, --uniform-spacing     one space between words, two after sentences
  -w, --width=WIDTH         maximum line width (default of 75 columns)
  -g, --goal=WIDTH          goal width (default of 93% of width)
      --help     display this help and exit
      --version  output version information and exit
```

## sort et uniq

### Sort
**Sort** est un programme Linux utiliser pour afficher les lignes des fichiers texte en entrées et les concaténer dans un ordre défini. La commande Sort prend un espace blanc comme séparateur de champs et tout le fichier entré est utilisé comme une clé de tri. Il est important de noter que la commande sort ne trie pas les fichiers mais affiche seulement une sortie trier, sauf si vous redirigé la sortie.

```
sort [OPTION]... [FILE]...
```

```
[root@centos7-1 ~]# cat 1.txt 
D 1
d 1
c 2
C 2
A 3
B 4
f 14
[root@centos7-1 ~]# sort 1.txt 
A 3
B 4
c 2
C 2
d 1
D 1
f 14
```

Si un fichier a des mots / lignes qui commencent avec des caractères miniscules et majuscules, la commande sort affiche alors ceux en majuscule en premier. Cependant nous pouvons changer ce comportement avec l'option de la ligne de commande -f :

```
[root@centos7-1 ~]# sort -f 1.txt 
A 3
B 4
C 2
c 2
D 1
d 1
f 14
```

L'option ` -n  ` trie le contenu numériquement. Nous pouvons aussi trier un fichier sur une colonne définit avec l'option `-k`n :

```
[root@centos7-1 ~]# sort  -n -k2 1.txt 
d 1
D 1
c 2
C 2
A 3
B 4
f 14
```

Utiliser `-r ` pour inverser le résultats des comparaisons. Les autres options de la commande sort :

```
  -b, --ignore-leading-blanks  ignore leading blanks
  -d, --dictionary-order      consider only blanks and alphanumeric characters
  -f, --ignore-case           fold lower case to upper case characters
  -g, --general-numeric-sort  compare according to general numerical value
  -i, --ignore-nonprinting    consider only printable characters
  -M, --month-sort            compare (unknown) < 'JAN' < ... < 'DEC'
  -h, --human-numeric-sort    compare human readable numbers (e.g., 2K 1G)
  -n, --numeric-sort          compare according to string numerical value
  -R, --random-sort           sort by random hash of keys
      --random-source=FILE    get random bytes from FILE
  -r, --reverse               reverse the result of comparisons
      --sort=WORD             sort according to WORD:
                                general-numeric -g, human-numeric -h, month -M,
                                numeric -n, random -R, version -V
  -V, --version-sort          natural sort of (version) numbers within text
```

Sort peut trier le contenu de deux fichier sur la sortie standard en un! `sort 1.txt 2.txt`

### Uniq 

La commande **uniq** est utilisé pour raporter ou omettre les lignes répétées, elle filtre les lignes depuis l'entrée standard et écrit le résultat dans la sortie standard.

```
[root@centos7-1 ~]# cat assets.txt 
motherboard
motherboard
cpu
cpu
ram
ram
ram
ram
monitor
monitor
hdd
ssd
mouse
keyboard
keyboard
[root@centos7-1 ~]# uniq assets.txt 
motherboard
cpu
ram
monitor
hdd
ssd
mouse
keyboard
```

Utiliser -c pour afficher le nombre de répétition pour chaque ligne :

```
[root@centos7-1 ~]# uniq -c assets.txt 
      2 motherboard
      2 cpu
      4 ram
      2 monitor
      1 hdd
      1 ssd
      1 mouse
      2 keyboard
```

\-d affiche seulement les lignes répétés et à l'inverse -u montre seulement les lignes uniques :

```
[root@centos7-1 ~]# uniq -d assets.txt 
motherboard
cpu
ram
monitor
keyboard
[root@centos7-1 ~]# uniq -u assets.txt 
hdd
ssd
mouse
```

Essayer `-D` pour voir toutes les lignes dupliquées. Les autres options de uniq --help :

```
  -c, --count           prefix lines by the number of occurrences
  -d, --repeated        only print duplicate lines, one for each group
  -D, --all-repeated[=METHOD]  print all duplicate lines
                          groups can be delimited with an empty line
                          METHOD={none(default),prepend,separate}
  -f, --skip-fields=N   avoid comparing the first N fields
      --group[=METHOD]  show all items, separating groups with an empty line
                          METHOD={separate(default),prepend,append,both}
  -i, --ignore-case     ignore differences in case when comparing
  -s, --skip-chars=N    avoid comparing the first N characters
  -u, --unique          only print unique lines
  -z, --zero-terminated  end lines with 0 byte, not newline
  -w, --check-chars=N   compare no more than N characters in lines
      --help     display this help and exit
      --version  output version information and exit
```

## split

La commande split est utilisé pour couper ou séparé un fichier en morceaux.

```
 split [options] filename prefix
```

* Remplacer filename avec le nom du fichier que vous voulez découper.
* Remplacer prefix avec le nom que vous voulez donnés au fichiers de sortie..
* Vous pouvez utiliser l'option  exclude, ou la remplacer avec une des suivantes ::
* `-l linenumber`
* `-b bytes`

Si nous utilisons l'option -l (un L miniscule), remplace le numéro de ligne avec le numéro des lignes que nous voulons dans chaquem fichiers découpé (par défaut 1,000).

```
[root@centos7-1 split]# ls
my7lines.txt
[root@centos7-1 split]# cat my7lines.txt 
this is 1st line.
this is 2nd line.
this is 3rd line.
this is 4th line
this is 5th line.
this is 6th line.
this is 7th line.

[root@centos7-1 split]# split -l 2 my7lines.txt 

[root@centos7-1 split]# ls
my7lines.txt  xaa  xab  xac  xad

[root@centos7-1 split]# echo "xaa";cat xaa;echo "xab";cat  xab;echo "xac";cat xac;echo "xad"; cat  xad
xaa
this is 1st line.
this is 2nd line.
xab
this is 3rd line.
this is 4th line
xac
this is 5th line.
this is 6th line.
xad
this is 7th line.
```

La commande split nous donnera chaque fichier de sortie qu'elle a crée avec le préfixe de nom et un numéro à la fi de l'extension qui indique l'ordre. Par défaut , la commande split ajoute aa au premier fichier de sortie, et continue dans l'alphabet jusqu' zz pour les fichiers conséquents. Si vous ne spécifiez pas de préfix, la plupart des systèmes utilise x.

If we use the -b option, replace bytes with the number of bytes you'd like in each of the smaller files.

```
[root@centos7-1 split2]# du -ah
51M    ./dsl-4.11.rc2.iso
51M    .

[root@centos7-1 split2]# split -b 10MB dsl-4.11.rc2.iso 
[root@centos7-1 split2]# ls 
dsl-4.11.rc2.iso  xaa  xab  xac  xad  xae  xaf
[root@centos7-1 split2]# du -ah
51M    ./dsl-4.11.rc2.iso
9.6M    ./xaa
9.6M    ./xab
9.6M    ./xac
9.6M    ./xad
9.6M    ./xae
2.7M    ./xaf
101M    .
```

Les autres options sont :

```
  -a, --suffix-length=N   generate suffixes of length N (default 2)
      --additional-suffix=SUFFIX  append an additional SUFFIX to file names
  -b, --bytes=SIZE        put SIZE bytes per output file
  -C, --line-bytes=SIZE   put at most SIZE bytes of lines per output file
  -d, --numeric-suffixes[=FROM]  use numeric suffixes instead of alphabetic;
                                   FROM changes the start value (default 0)
  -e, --elide-empty-files  do not generate empty output files with '-n'
      --filter=COMMAND    write to shell COMMAND; file name is $FILE
  -l, --lines=NUMBER      put NUMBER lines per output file
  -n, --number=CHUNKS     generate CHUNKS output files; see explanation below
  -u, --unbuffered        immediately copy input to output with '-n r/...'
      --verbose           print a diagnostic just before each
                            output file is opened
      --help     display this help and exit
      --version  output version information and exit

SIZE is an integer and optional unit (example: 10M is 10*1024*1024).  Units
are K, M, G, T, P, E, Z, Y (powers of 1024) or KB, MB, ... (powers of 1000).
```

Pour rassembler les fichiers découpés utiliser `cat x* > orginalfile` .

## wc

La commande wc (word count) est utilisé pour trouver le nombre de saut de ligne, le nombre de mot, de bute et de caractère dans un fichier.

```
wc [options] filenames
```

Un exemple basique de la commande WC

```
[root@centos7-1 ~]# wc /etc/inittab 
 17  80 511 /etc/inittab
```

Trois nombre sont montré ci-dessus : **17** (nombre de  **lignes**), **80** (nombre de **mots**_\[par défaut séparé par des espaces]_) et **511**(nombre de  **bytes**) du fichier.

options:

```
  -c, --bytes            print the byte counts
  -m, --chars            print the character counts
  -l, --lines            print the newline counts
      --files0-from=F    read input from the files specified by
                           NUL-terminated names in file F;
                           If F is - then read names from standard input
  -L, --max-line-length  print the length of the longest line
  -w, --words            print the word counts
      --help     display this help and exit
      --version  output version information and exit
```

## Commande head et tail :

### ![](.gitbook/assets/processtxt-headtail.jpg)

### head

La commande head lit les 10 premières lignes d'un fichier avec son nom.

```
head [options] [file(s)]
```

Pa exemple essayons avec le fichier /var/log/yum.log :

```
[root@centos7-1 ~]# head /var/log/yum.log 
Aug 26 04:48:25 Updated: openldap-2.4.44-15.el7_5.x86_64
Aug 26 04:48:25 Installed: openldap-clients-2.4.44-15.el7_5.x86_64
Aug 26 04:48:27 Installed: openldap-servers-2.4.44-15.el7_5.x86_64
Oct 13 03:38:41 Installed: perl-Data-Dumper-2.145-3.el7.x86_64
Oct 13 03:38:41 Installed: perl-Net-Daemon-0.48-5.el7.noarch
Oct 13 03:38:41 Installed: perl-Digest-1.17-245.el7.noarch
Oct 13 03:38:41 Installed: perl-Digest-MD5-2.52-3.el7.x86_64
Oct 13 03:38:41 Installed: 7:squid-migration-script-3.5.20-12.el7.x86_64
Oct 13 03:38:41 Installed: 1:perl-Compress-Raw-Zlib-2.061-4.el7.x86_64
Oct 13 03:38:42 Installed: libecap-1.0.0-1.el7.x86_64
```

Pour retrouver le nombre de lignes souhaité utiliser l'option -n\<number> ou l'option plus simple -\<number> :

```
[root@centos7-1 ~]# head -n 2 /var/log/yum.log 
Aug 26 04:48:25 Updated: openldap-2.4.44-15.el7_5.x86_64
Aug 26 04:48:25 Installed: openldap-clients-2.4.44-15.el7_5.x86_64
[root@centos7-1 ~]# head -2 /var/log/yum.log 
Aug 26 04:48:25 Updated: openldap-2.4.44-15.el7_5.x86_64
Aug 26 04:48:25 Installed: openldap-clients-2.4.44-15.el7_5.x86_64
```

Options depuis `head --help` :

```
  -c, --bytes=[-]K         print the first K bytes of each file;
                             with the leading '-', print all but the last
                             K bytes of each file
  -n, --lines=[-]K         print the first K lines instead of the first 10;
                             with the leading '-', print all but the last
                             K lines of each file
  -q, --quiet, --silent    never print headers giving file names
  -v, --verbose            always print headers giving file names
      --help     display this help and exit
      --version  output version information and exit

K may have a multiplier suffix:
b 512, kB 1000, K 1024, MB 1000*1000, M 1024*1024,
GB 1000*1000*1000, G 1024*1024*1024, and so on for T, P, E, Z, Y.
```

### tail

La commande tail affiche les 10 dernières lignes d'un fichier texte .

```
tail [options] [filenames]
```

Comme la commande head ci-dessus, la commande tail supporte aussi les options -n nombre de lignes et  n nombre de caractères.

```
[root@centos7-1 ~]# tail -n 5 /var/log/yum.log 
Dec 05 10:02:37 Updated: firefox-60.3.0-1.el7.centos.x86_64
Dec 05 10:02:37 Updated: nss-tools-3.36.0-7.el7_5.x86_64
Dec 05 10:02:37 Updated: 1:dbus-x11-1.10.24-7.el7.x86_64
Dec 08 11:54:14 Installed: zip-3.0-11.el7.x86_64
Dec 08 13:10:52 Installed: vsftpd-3.0.2-22.el7.x86_64
```

L'option \-f demande à tail de boucler à l'infini, en récupérant les nouvelles données à la fin du fichier. Lorsque de nouvelles données sont ajoutées, elles seront affichées. Cela fonctionne bien avec les fichiers de log et voyons ce que cela donne :

```
[root@centos7-1 ~]# tail -f /var/log/dmesg
[   19.126805] AES CTR mode by8 optimization enabled
[   19.129902] ppdev: user-space parallel port driver
[   19.155269] alg: No test for __gcm-aes-aesni (__driver-gcm-aes-aesni)
[   19.160414] Adding 4063228k swap on /dev/mapper/centos-swap.  Priority:-1 extents:1 across:4063228k FS
[   19.176446] alg: No test for crc32 (crc32-pclmul)
[   19.220329] XFS (sda1): Mounting V5 Filesystem
[   19.288818] XFS (sda1): Ending clean mount
[   21.853831] floppy0: no floppy controllers found
[   21.854029] work still pending
[   22.082648] type=1305 audit(1543863215.542:3): audit_pid=681 old=0 auid=4294967295 ses=4294967295 res=1
```

options:

```
  -c, --bytes=K            output the last K bytes; or use -c +K to output
                             bytes starting with the Kth of each file
  -f, --follow[={name|descriptor}]
                           output appended data as the file grows;
                             an absent option argument means 'descriptor'
  -F                       same as --follow=name --retry
  -n, --lines=K            output the last K lines, instead of the last 10;
                             or use -n +K to output starting with the Kth
      --max-unchanged-stats=N
                           with --follow=name, reopen a FILE which has not
                             changed size after N (default 5) iterations
                             to see if it has been unlinked or renamed
                             (this is the usual case of rotated log files);
                             with inotify, this option is rarely useful
      --pid=PID            with -f, terminate after process ID, PID dies
  -q, --quiet, --silent    never output headers giving file names
      --retry              keep trying to open a file if it is inaccessible
  -s, --sleep-interval=N   with -f, sleep for approximately N seconds
                             (default 1.0) between iterations;
                             with inotify and --pid=P, check process P at
                             least once every N seconds
  -v, --verbose            always output headers giving file names
      --help     display this help and exit
      --version  output version information and exit
```

## less

La commande less nous permet de voir le contenu d'un fichier et de naviguer dans ce fichier.

```
[root@centos7-1 ~]# dmesg |less
```

```
[    0.000000] BIOS-e820: [mem 0x00000000bfeff000-0x00000000bfefffff] ACPI NVS
[    0.000000] BIOS-e820: [mem 0x00000000bff00000-0x00000000bfffffff] usable
[    0.000000] BIOS-e820: [mem 0x00000000f0000000-0x00000000f7ffffff] reserved
[    0.000000] BIOS-e820: [mem 0x00000000fec00000-0x00000000fec0ffff] reserved
[    0.000000] BIOS-e820: [mem 0x00000000fee00000-0x00000000fee00fff] reserved
[    0.000000] BIOS-e820: [mem 0x00000000fffe0000-0x00000000ffffffff] reserved
[    0.000000] BIOS-e820: [mem 0x0000000100000000-0x000000013fffffff] usable
:
```

Par défaut le seul moyen de sortir de la commande less est d'appuyer sur la touche q. Pour changer ce comportement et sortir automatiquement du fichier lorsque l'on atteint la fin utiliser l'option `-e` ou `-E`. `less -e /var/log/auth.log` ou `less -E /var/log/auth.log`

* Pour ouvrir un fichier à la première occurence d'un pattern utiliser la syntaxe suivante :

`less +/sshd /var/log/auth.log`

* Afin d'ajouter automatiquement le contenu d'un fichier ouvert dans une commande less utiliser la combinaison de touches Shift+f  ou lancer less avec la syntaxe suivante :

`less +F /var/log/messages`

Cela lance less en mode interractive et affiche le nouveau contenu à la volée alors qu'il attend de nouvelles données à écrire dans le ficheir. Ce comportement est similaire à la commande tail -f. Pour sortir du mode live utiliser les touches `Ctrl+c`.

**Astuce**: En combinaison avec un patter, vous pouvez voir le fichier de log de manière interractive avec les touches `Shift+f` tout en recherchant un mot-clé.

> **less vs more**
>
> La commande less est similaire à more, la différence principale entre les deux est que la commande less est plus rapide parce qu'elle ne charge pas le fichier entier d'un coup et permet la navigation dans le fichier en utilisant les touches haut / bas.
>
> Que vous décidiez d'utiliser more ou less, ce qui est un choix personnel, rappelez-vous que less est more avec plus de fonctionnalités.

## cut

La commande cut dans UNIX et un utilitaire en ligne de commande pour couper des sections depuis chaque ligne d'un fichier et écrire le résultat dans la sortie standard. Elle peut être utilisé pour coupé une ligne par **position de bytes**, **caractère** et **délimiteur**.

```
cut OPTION... [FILE]...
```

**Couper par position de byte:**

```
[root@centos7-1 ~]# echo "linux" | cut -b 1
l
[root@centos7-1 ~]# echo "linux" | cut -b 1,5
lx
[root@centos7-1 ~]# echo "linux" | cut -b 1-4
linu
```

**Couper par caractère:**

```
[root@centos7-1 ~]# echo '♣foobar' | cut -c 1,7
♣r
[root@centos7-1 ~]# echo '♣foobar' | cut -c 5-7
bar
```

**Couper basé sur un délimiteur :**

![](.gitbook/assets/processtxt-cut.jpg)

Pour couper en utilisant un délimiteur utiliser l'option -d. Elles est normalement utilisé en conjonction avec l'option -f  pour spécifier le champs qui doit être couper. Exemples :

```
[root@centos7-1 ~]# cut 1.txt -d: -f1
1
2
3
4
[root@centos7-1 ~]# cut 1.txt -d: -f2
a,w
b,x
c,y
d,z
[root@centos7-1 ~]# cut 1.txt -d, -f1
1:a
2:b
3:c
4:d
[root@centos7-1 ~]# cut 1.txt -d, -f2
w
x
y
z
```

cut a de nombreuses options:

```
  -b, --bytes=LIST        select only these bytes
  -c, --characters=LIST   select only these characters
  -d, --delimiter=DELIM   use DELIM instead of TAB for field delimiter
  -f, --fields=LIST       select only these fields;  also print any line
                            that contains no delimiter character, unless
                            the -s option is specified
  -n                      with -b: don't split multibyte characters
      --complement        complement the set of selected bytes, characters
                            or fields
  -s, --only-delimited    do not print lines not containing delimiters
      --output-delimiter=STRING  use STRING as the output delimiter
                            the default is to use the input delimiter
      --help     display this help and exit
      --version  output version information and exit
```

## paste

La commande paste affiche les lignes correspondantes de plusieurs fichiers côte-à-côte.

```
paste [OPTION]... [FILE]...
```

![](.gitbook/assets/processtxt-paste.jpg)

```
[root@centos7-1 ~]# paste 1.txt 2.txt 
a    e
b    f
c    g
d    h
```

paste écrit les lignes qui contienne des séquences de lignes qui correspondent dans chaque fichier, séparé par des tabulations. Pour utiliser un deux-points (:) comme délimiteur au lieu d'une tabulation utiliser l'option -d :

```
[root@centos7-1 ~]# paste -d: 1.txt 2.txt 
a:e
b:f
c:g
d:h
```

Options de la commande paste:

```
  -d, --delimiters=LIST   reuse characters from LIST instead of TABs
  -s, --serial            paste one file at a time instead of in parallel
      --help     display this help and exit
      --version  output version information and exit
```

## join

Rassemble les lignes de deux fichiers qui partage un champ de données commun.

>  Lorsque **vous utilisez `join`**, le **fichier d'entrée doit être trié seulement par le champs de jointure**_, autrement vous pourriez avoir un avertissement

```
join [OPTION]... FILE1 FILE2
```

![](.gitbook/assets/processtxt-join.jpg)

```
[root@centos7-1 ~]# join 1.txt 2.txt 
1 a w
4 d z
5 e q
```

Par défaut, la commande join affiche seulement les lignes avec des correspondances, les lignes sans correspondance ne sont pas dans la sortie. Si vous les voulez quand même utiliser l'option -a. Cette option nécessite que vous passiez un numéro de fichier pour que l'outil sache de quel fichier nous parlons.

```
[root@centos7-1 ~]# join 1.txt 2.txt -a 1
1 a w
2 b
4 d z
5 e q
[root@centos7-1 ~]# join 1.txt 2.txt -a 2
1 a w
3 y
4 d z
5 e q
```

Afin d'afficher les lignes sans correspondances (ce qui signifie, supprimer les lignes avec correspondance de la sortie), utiliser l'option -v. Cette option fonctionne exactement de la même manière que l'option -a.

```
[root@centos7-1 ~]# join 1.txt 2.txt -v 1
2 b
[root@centos7-1 ~]# join 1.txt 2.txt -v 2
3 y
```

join combine les lignes des fichiers sur un champ commun qui est par défaut le premier champs. Cependant, si nous voulons, nous pouvons lui spécifier un champ différents pour caque fichier en utilisant les options de la ligne de commande -1 et -2 . Par exemple `join -1 2 -2 2 file1 file2` utilise le second champs de chaque ligne. 
Options de la commande join :

```
  -a FILENUM        also print unpairable lines from file FILENUM, where
                      FILENUM is 1 or 2, corresponding to FILE1 or FILE2
  -e EMPTY          replace missing input fields with EMPTY
  -i, --ignore-case  ignore differences in case when comparing fields
  -j FIELD          equivalent to '-1 FIELD -2 FIELD'
  -o FORMAT         obey FORMAT while constructing output line
  -t CHAR           use CHAR as input and output field separator
  -v FILENUM        like -a FILENUM, but suppress joined output lines
  -1 FIELD          join on this FIELD of file 1
  -2 FIELD          join on this FIELD of file 2
  --check-order     check that the input is correctly sorted, even
                      if all input lines are pairable
  --nocheck-order   do not check that the input is correctly sorted
  --header          treat the first line in each file as field headers,
                      print them without trying to pair them
  -z, --zero-terminated     end lines with 0 byte, not newline
      --help     display this help and exit
      --version  output version information and exit
```

## sed

Le nom **Sed**est l'abreviation pour **\_s_tream \_ed_itor**.\
**S** editeur de flux est utilisé pour faire des transformation basique de texte d'un flux en entrée (un fichier ou une entré depuis un pipeline). Sed utilise les expressions régulières et l'usage le plus basique (et populaire) de sef est de substituer des caractères.

Par exemple remplaçons 'l' avec "L" dans un fichier texte :

```
[root@centos7-1 ~]# cat sample.txt 
there are different operating systems in our planet.
one of them is linux.
almost six hundred linux distributions exist.

[root@centos7-1 ~]# sed 's/l/L/' sample.txt 
there are different operating systems in our pLanet.
one of them is Linux.
aLmost six hundred linux distributions exist.
```

Par défaut sed fait le remplacement une seule fois sur la première instance du terme, utiliser le flag -g pour faire le remplacement sur toutes les instances du terme sur chaque ligne du fichier.

![](.gitbook/assets/processtxt-seds.jpg)

```
[root@centos7-1 ~]# sed 's/l/L/g' sample.txt 
there are different operating systems in our pLanet.
one of them is Linux.
aLmost six hundred Linux distributions exist.
```

De plus, nous pouvons utiliser gi au lieu de g afin d'ignorer la casse des caractères:

```
[root@centos7-1 ~]# sed 's/linux/LINUX/gi' sample.txt 
there are different operating systems in our planet.
one of them is LINUX.
almost six hundred LINUX distributions exist.
```

Un autre exemple est de remplacer les espaces blancs avec des tabulations :

```
[root@centos7-1 ~]# cat sample.txt 
there are different operating systems in our planet.
one of them is linux.
almost six hundred linux distributions exist.

[root@centos7-1 ~]# sed 's/ /\t/g' sample.txt  | cat 
there    are    different    operating    systems    in    our    planet.
one    of    them    is    linux.
almost    six    hundred    linux    distributions    exist.
```

Sed est très puissant, et les tâches qu'il peut accomplir sont limités seulement par votre imagination.

## Exercices 

### Exercices guidés

* Quelqu’un vient de faire don d’un ordinateur portable à votre école et vous souhaitez maintenant installer Linux dessus. Il n’y a pas de manuel et vous avez été contraint de démarrer sur une clé USB sans environnement graphique. Vous disposez d’un shell et vous savez que pour chaque processeur que vous avez, il y aura une ligne correspondante dans le fichier /proc/cpuinfo :

    processor	: 0
    vendor_id	: GenuineIntel
    cpu family	: 6
    model		: 158

    (lignes ignorées)

    processor	: 1
    vendor_id	: GenuineIntel
    cpu family	: 6
    model		: 158

    (plus de lignes ignorées)

        En utilisant les commandes grep et wc, affichez le nombre de processeurs dont vous disposez.
<details>
  <summary>Réponse</summary>
        Voici deux possibilités :

        $ cat /proc/cpuinfo | grep processor | wc -l
        $ grep processor /proc/cpuinfo | wc -l

        Maintenant que vous savez qu’il existe plusieurs façons de faire la même chose, à quel moment devez-vous utiliser l’une ou l’autre ? Cela dépend en fait de plusieurs facteurs, les deux plus importants étant la performance et la lisibilité. La plupart du temps, vous utiliserez des commandes shell à l’intérieur de scripts shell pour automatiser vos tâches. Plus vos scripts sont volumineux et complexes, plus vous devez vous soucier de leur rapidité.
</details>
        Faites la même chose avec sed au lieu de grep.
<details>
  <summary>Réponse</summary>
        Maintenant, au lieu de grep, nous allons essayer avec sed :

        $ sed -n /processor/p /proc/cpuinfo | wc -l

        Ici, nous avons utilisé sed avec l’option -n pour que sed n’affiche rien sauf ce qui correspond à l’expression processor, comme indiqué par la commande p. Comme nous l’avons fait dans les solutions basées sur grep, wc -l va compter le nombre de lignes et donc le nombre de processeurs que nous avons.

        Regardez bien le prochain exemple :

        $ sed -n /processor/p /proc/cpuinfo | sed -n '$='

        Cette séquence de commandes fournit le même résultat que dans l’exemple précédent où la sortie de sed était envoyée vers la commande wc. La différence ici est qu’au lieu d’utiliser wc -l pour compter le nombre de lignes, sed est invoqué à nouveau pour fournir une fonctionnalité équivalente. Encore une fois, nous supprimons les résultats de sed avec l’option -n à l’exception de l’expression que nous appelons explicitement, en l’occurrence '$='. Cette expression demande à sed de rechercher la dernière ligne ($) et affiche le numéro de cette ligne (=).
</details>

* Explorez votre fichier local /etc/passwd avec les commandes grep, sed, head et tail en fonction des tâches ci-dessous :

  *  Quels utilisateurs ont accès à un shell Bash ?
<details>
  <summary>Réponse</summary>
        $ grep ":/bin/bash$" /etc/passwd

        Nous allons améliorer cette réponse en affichant uniquement le nom de l’utilisateur qui utilise le shell Bash.

        $ grep ":/bin/bash$" /etc/passwd | cut -d: -f1

        Le nom d’utilisateur est le premier champ (paramètre -f1 de la commande cut) et le fichier /etc/passwd utilise des : comme séparateurs (paramètre -d: de la commande cut). Il suffit d’envoyer la sortie de la commande grep vers la commande cut appropriée.
</details>

  * Votre système comporte un certain nombre d’utilisateurs destinés à gérer des programmes spécifiques ou à des fins administratives. Ils n’ont pas accès à un shell. Combien d’entre eux existent sur votre système ?
<details>
  <summary>Réponse</summary>
        Le moyen le plus simple de le savoir est d’afficher les lignes correspondant aux comptes qui n’utilisent pas le shell Bash :

        $ grep -v ":/bin/bash$" /etc/passwd | wc -l
</details>

  * Combien d’utilisateurs et de groupes existent sur votre système (rappelez-vous : utilisez uniquement le fichier /etc/passwd) ?
<details>
  <summary>Réponse</summary>
        Le premier champ de chaque ligne de votre fichier /etc/passwd est le nom de l’utilisateur, le second est généralement un x indiquant que le mot de passe de l’utilisateur n’est pas stocké ici (il est chiffré dans le fichier /etc/shadow). Le troisième est l’identifiant de l’utilisateur (UID) et le quatrième est l’identifiant du groupe (GID). Ceci devrait donc nous donner le nombre d’utilisateurs :

        $ cut -d: -f3 /etc/passwd | wc -l

        Disons que c’est vrai dans la plupart des cas. Cependant, il y a des situations où vous définirez différents super-utilisateurs ou d’autres types d’utilisateurs spéciaux partageant le même UID. Donc, pour être sûr, nous allons passer le résultat de notre commande cut à la commande sort et ensuite compter le nombre de lignes.

        $ cut -d: -f3 /etc/passwd | sort -u | wc -l

        Maintenant, pour le nombre de groupes :

        $ cut -d: -f4 /etc/passwd | sort -u | wc -l
</details>

  * Affichez uniquement la première ligne, la dernière ligne et la dixième ligne de votre fichier /etc/passwd.
<details>
  <summary>Réponse</summary>
        Cela fera l’affaire :

        $ sed -n -e '1'p -e '10'p -e '$'p /etc/passwd

        Rappelez-vous que le paramètre -n indique à sed de ne pas imprimer autre chose que ce qui est spécifié par la commande p. Le signe dollar ($) utilisé ici est une expression régulière signifiant la dernière ligne du fichier.
</details>

* Prenons cet exemple de fichier /etc/passwd. Recopiez les lignes ci-dessous dans un fichier local nommé mypasswd pour cet exercice.
    ```
    root:x:0:0:root:/root:/bin/bash
    daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
    bin:x:2:2:bin:/bin:/usr/sbin/nologin
    sys:x:3:3:sys:/dev:/usr/sbin/nologin
    sync:x:4:65534:sync:/bin:/bin/sync
    nvidia-persistenced:x:121:128:NVIDIA Persistence Daemon,,,:/nonexistent:/sbin/nologin
    libvirt-qemu:x:64055:130:Libvirt Qemu,,,:/var/lib/libvirt:/usr/sbin/nologin
    libvirt-dnsmasq:x:122:133:Libvirt Dnsmasq,,,:/var/lib/libvirt/dnsmasq:/usr/sbin/nologin
    carol:x:1000:2000:Carol Smith,Finance,,,Main Office:/home/carol:/bin/bash
    dave:x:1001:1000:Dave Edwards,Finance,,,Main Office:/home/dave:/bin/ksh
    emma:x:1002:1000:Emma Jones,Finance,,,Main Office:/home/emma:/bin/bash
    frank:x:1003:1000:Frank Cassidy,Finance,,,Main Office:/home/frank:/bin/bash
    grace:x:1004:1000:Grace Kearns,Engineering,,,Main Office:/home/grace:/bin/ksh
    henry:x:1005:1000:Henry Adams,Sales,,,Main Office:/home/henry:/bin/bash
    john:x:1006:1000:John Chapel,Sales,,,Main Office:/home/john:/bin/bash
    ```
  * Affichez tous les utilisateurs du groupe 1000 (utilisez sed pour sélectionner le champ approprié) depuis votre fichier mypasswd.
<details>
  <summary>Réponse</summary>
        Le GID est le quatrième champ du fichier /etc/passwd. Vous pourriez être tenté d’essayer ceci :

        $ sed -n /1000/p mypasswd

        Dans ce cas, vous obtiendrez également cette ligne :

        carol:x:1000:2000:Carol Smith,Finance,,,Main Office:/home/carol:/bin/bash

        Vous savez que ce n’est pas correct puisque Carol Smith est membre du GID 2000 et que la correspondance a été établie grâce à l’UID. Cependant, vous avez peut-être remarqué qu’après le GID, le champ suivant commence par une majuscule. Nous pouvons utiliser une expression régulière pour résoudre ce problème.

        $ sed -n /:1000:[A-Z]/p mypasswd

        L’expression [A-Z] correspondra à n’importe quel caractère majuscule unique. Vous en apprendrez davantage dans la leçon correspondante.
</details>
        
  * Affichez uniquement les noms complets de tous les utilisateurs de ce groupe (utilisez sed et cut).
<details>
  <summary>Réponse</summary>
        Utilisez la même technique que vous avez utilisée pour résoudre la première partie de cet exercice et envoyez le résultat vers une commande cut.

        $ sed -n /:1000:[A-Z]/p mypasswd | cut -d: -f5
        Dave Edwards,Finance,,,Main Office
        Emma Jones,Finance,,,Main Office
        Frank Cassidy,Finance,,,Main Office
        Grace Kearns,Engineering,,,Main Office
        Henry Adams,Sales,,,Main Office
        John Chapel,Sales,,,Main Office

        Nous y sommes presque ! Notez que les champs dans les résultats peuvent être séparés par des virgules ,. Nous allons donc renvoyer la sortie vers une autre commande cut en utilisant la virgule , comme délimiteur.

        $ sed -n /:1000:[A-Z]/p mypasswd | cut -d: -f5 | cut -d, -f1
        Dave Edwards
        Emma Jones
        Frank Cassidy
        Grace Kearns
        Henry Adams
        John Chapel
</details>

### Exercices d’approfondissement

* En utilisant à nouveau le fichier mypasswd des exercices ci-dessus, trouvez une commande Bash qui sélectionnera au hasard une personne du bureau principal (Main Office) pour gagner une tombola. Utilisez la commande sed pour afficher uniquement les lignes du bureau principal, puis une séquence de commandes cut pour extraire le prénom de chaque utilisateur de ces lignes. Enfin, vous allez trier ces noms au hasard et afficher uniquement le premier nom de la liste.
<details>
  <summary>Réponse</summary>
    Tout d’abord, voyez l’effet de l’option -R sur la sortie de la commande sort. Répétez cette commande plusieurs fois sur votre machine (notez que vous devrez mettre 'Main Office' entre guillemets simples pour que sed le traite comme une seule chaîne) :

    $ sed -n /'Main Office'/p mypasswd | cut -d: -f5 | cut -d, -f1 | sort -R

    Voici une solution à ce problème :

    $ sed -n /'Main Office'/p mypasswd | cut -d: -f5 | cut -d, -f1 | sort -R | head -1
</details>

* Combien de personnes travaillent dans la finance (Finance), l’ingénierie (Engineering) et les ventes (Sales) ? (Pensez à utiliser la commande uniq).
<details>
  <summary>Réponse</summary>
    Développez ce que vous avez appris dans les exercices précédents. Essayez ceci :

    $ sed -n /'Main Office'/p mypasswd
    $ sed -n /'Main Office'/p mypasswd | cut -d, -f2

    Notez à présent que nous ne nous soucions pas du caractère : comme délimiteur. Nous recherchons juste le deuxième champ lorsque nous séparons les lignes par les virgules ,.

    $ sed -n /'Main Office'/p mypasswd | cut -d, -f2 | uniq -c
          4 Finance
          1 Engineering
          2 Sales

    La commande uniq n’affiche que les lignes uniques (pas les lignes répétées) et l’option -c indique à uniq de compter les occurrences de lignes identiques. Faites bien attention : uniq ne prend en compte que les lignes adjacentes. Dans le cas contraire, vous devrez utiliser la commande sort.
</details>

* Maintenant vous souhaitez préparer un fichier CSV (Comma Separated Values) afin de pouvoir facilement importer, depuis le fichier mypasswd de l’exemple précédent, le fichier names.csv dans LibreOffice. Le contenu du fichier aura le format suivant :

First Name,Last Name,Position
Carol,Smith,Finance
...
John,Chapel,Sales

Astuce : Utilisez les commandes sed, cut, et paste pour obtenir le résultat souhaité. Notez que la virgule (,) servira de délimiteur pour ce fichier.

<details>
  <summary>Réponse</summary>
    Start with the sed and cut commands, building on top of what we learned from the previous exercises:

    $ sed -n /'Main Office'/p mypasswd | cut -d: -f5 | cut -d" " -f1 > firstname

    Now we have the file firstname with the first names of our employees.

    $ sed -n /'Main Office'/p mypasswd | cut -d: -f5 | cut -d" " -f2 | cut -d, -f1 > lastname

    Now we have the file lastname containing the surnames of each employee.

    Next we determine which department each employee works in:

    $ sed -n /'Main Office'/p mypasswd | cut -d: -f5 | cut -d, -f2 > department

    Before we work on the final solution, try the following commands to see what type of output they generate:

    $ cat firstname lastname department
    $ paste firstname lastname department

    And now for the final solution:

    $ paste firstname lastname department | tr '\t' ,
    $ paste firstname lastname department | tr '\t' , > names.csv

    Here we use the command tr to translate \t, the tab separator, by a ,. tr is quite useful when we need to exchange one character for another. Be sure to review the man pages for both tr and paste. For example, we can use the -d option for the delimiter to make the previous command less complex:

    $ paste -d, firstname lastname department

    We used the paste command here once we needed to get you familiar with it. However we could have easily performed all of the tasks in a single command chain:

    $ sed -n /'Main Office'/p mypasswd | cut -d: -f5 | cut -d, -f1,2 | tr ' ' , > names.csv
</details>

* Admettons que la feuille de calcul names.csv créée dans l’exercice précédent soit un fichier important et que nous voulions nous assurer que personne ne puisse l’altérer entre le moment où nous l’envoyons à quelqu’un et le moment où notre destinataire le reçoit. Comment assurer l’intégrité de ce fichier en utilisant md5sum ?
<details>
  <summary>Réponse</summary>
  If you look into the man pages for md5sum, sha256sum and sha512sum you will see they all start with the following text:

  “compute and check XXX message digest”

  Where “XXX” is the algoritm that will be used to create this message digest.

  We will use md5sum as an example and later you can try with the other commands.

  $ md5sum names.csv
  61f0251fcab61d9575b1d0cbf0195e25  names.csv

  Now, for instance, you can make the file available through a secure ftp service and send the generated message digest using another secure means of communication. If the file has been slightly modified the message digest will be completely different. Just to prove it, edit names.csv and change Jones to James as demonstrated here:

  $ sed -i.backup s/Jones/James/ names.csv
  $ md5sum names.csv
  f44a0d68cb480466099021bf6d6d2e65  names.csv

  Whenever you make files available for download, it is always a good practice to also distribute a message digest correspondent so people who download your file can produce a new message digest and check against the original. If you browse through https://kernel.org you will find the page https://mirrors.edge.kernel.org/pub/linux/kernel/v5.x/sha256sums.asc where you can obtain the sha256sum for all files available for download.
</details>

* Vous vous êtes promis de lire un livre classique à raison de 100 lignes par jour et vous avez décidé de commencer par Mariner and Mystic de Herman Melville. Imaginez une commande en utilisant split pour séparer ce livre en segments de 100 lignes chacun. Pour obtenir le livre en format texte simple, recherchez-le sur https://www.gutenberg.org.
<details>
  <summary>Réponse</summary>
  Tout d’abord, nous allons récupérer le livre dans son intégralité sur le site du Projet Gutenberg, où vous pouvez télécharger ce livre et d’autres ouvrages disponibles dans le domaine public.

  $ wget https://www.gutenberg.org/files/50461/50461-0.txt

  Vous devrez peut-être installer wget s’il n’est pas déjà présent sur votre système. Alternativement, vous pouvez aussi vous servir de curl. Utilisez less pour vérifier le livre :

  $ less 50461-0.txt

  Nous allons maintenant découper le livre en plusieurs morceaux de 100 lignes chacun :

  $ split -l 100 -d 50461-0.txt melville

  50461-0.txt est le fichier que nous allons découper. melville sera le préfixe pour les fichiers scindés. L’option -l 100 spécifie le nombre de lignes et l’option -d indique à split de numéroter les fichiers (en utilisant le suffixe fourni). Vous pouvez utiliser nl sur n’importe lequel des fichiers découpés (probablement pas sur le dernier) et confirmer que chacun d’entre eux compte une centaine de lignes.
</details>

* En utilisant ls -l sur le répertoire /etc, quel genre d’affichage obtenez-vous ? En utilisant la commande cut sur le résultat de la commande ls donnée, comment afficheriez-vous uniquement les noms des fichiers ? Qu’en est-il du nom de fichier et du propriétaire du fichier ? En plus des commandes ls -l et cut, utilisez la commande tr pour compacter plusieurs occurrences d’un espace en un seul espace afin de faciliter le formatage de la sortie avec une commande cut.
<details>
  <summary>Réponse</summary>
  La commande ls en elle-même vous fournira juste les noms des fichiers. Nous pouvons cependant préparer le résultat de la commande ls -l (la liste détaillée) pour extraire des informations plus spécifiques.

  $ ls -l /etc | tr -s ' ' ,
  drwxr-xr-x,3,root,root,4096,out,24,16:58,acpi
  -rw-r--r--,1,root,root,3028,dez,17,2018,adduser.conf
  -rw-r--r--,1,root,root,10,out,2,17:38,adjtime
  drwxr-xr-x,2,root,root,12288,out,31,09:40,alternatives
  -rw-r--r--,1,root,root,401,mai,29,2017,anacrontab
  -rw-r--r--,1,root,root,433,out,1,2017,apg.conf
  drwxr-xr-x,6,root,root,4096,dez,17,2018,apm
  drwxr-xr-x,3,root,root,4096,out,24,16:58,apparmor
  drwxr-xr-x,9,root,root,4096,nov,6,20:20,apparmor.d

  L’option -s indique à tr de réduire les espaces consécutifs en un seul espace. La commande tr fonctionne pour tout type de caractère répétitif que vous spécifiez. Ensuite, nous remplaçons les espaces par une virgule ,. Nous n’avons pas besoin de remplacer les espaces dans notre exemple, nous allons donc simplement omettre les ,.

  $ ls -l /etc | tr -s ' '
  drwxr-xr-x 3 root root 4096 out 24 16:58 acpi
  -rw-r--r-- 1 root root 3028 dez 17 2018 adduser.conf
  -rw-r--r-- 1 root root 10 out 2 17:38 adjtime
  drwxr-xr-x 2 root root 12288 out 31 09:40 alternatives
  -rw-r--r-- 1 root root 401 mai 29 2017 anacrontab
  -rw-r--r-- 1 root root 433 out 1 2017 apg.conf
  drwxr-xr-x 6 root root 4096 dez 17 2018 apm
  drwxr-xr-x 3 root root 4096 out 24 16:58 apparmor

  Si je ne veux que les noms de fichiers, il suffit d’afficher le neuvième champ :

  $ ls -l /etc | tr -s ' ' | cut -d" " -f9

  Pour le nom du fichier et son propriétaire, nous aurons besoin du neuvième et du troisième champ :

  $ ls -l /etc | tr -s ' ' | cut -d" " -f9,3

  Et si nous avons juste besoin du nom des répertoires et de leur propriétaire ?

  $ ls -l /etc | grep ^d | tr -s ' ' | cut -d" " -f9,3
</details>

* Cet exercice suppose que vous travaillez sur une machine réelle (pas une machine virtuelle). Vous devez également vous munir d’une clé USB. Consultez les pages de manuel de la commande tail et voyez comment faire pour lire un fichier à la volée au fur et à mesure que du texte y est ajouté. Tout en surveillant la sortie de la commande tail sur le fichier /var/log/syslog, insérez une clé USB. Saisissez la commande complète que vous utiliseriez pour obtenir le produit (Product), le fabricant (Manufacturer) et la quantité totale de mémoire de votre clé USB.
<details>
  <summary>Réponse</summary>
  $ tail -f /var/log/syslog | grep -i 'product\:\|blocks\|manufacturer'
  Nov  8 06:01:35 brod-avell kernel: [124954.369361] usb 1-4.3: Product: Cruzer Blade
  Nov  8 06:01:35 brod-avell kernel: [124954.369364] usb 1-4.3: Manufacturer: SanDisk
  Nov  8 06:01:37 brod-avell kernel: [124955.419267] sd 2:0:0:0: [sdc] 61056064 512-byte logical blocks: (31.3 GB/29.1 GiB)

  Bien entendu, ceci n’est qu’un exemple et les résultats peuvent varier en fonction du fabricant de votre clé USB. Notez l’utilisation de l’option -i avec la commande grep car nous ne sommes pas sûrs que les chaînes de caractères que nous recherchons soient en majuscules ou en minuscules. Nous avons également utilisé le | comme un OU logique. Nous recherchons donc les lignes contenant product OU blocks OU manufacturer.

</details>

.

.

.

sources:

[https://developer.ibm.com/tutorials/l-lpic1-103-2/](https://developer.ibm.com/tutorials/l-lpic1-103-2/)

[https://ryanstutorials.net/linuxtutorial/piping.php#piping](https://ryanstutorials.net/linuxtutorial/piping.php#piping)

[https://www.tecmint.com/linux-io-input-output-redirection-operators/](https://www.tecmint.com/linux-io-input-output-redirection-operators/)

[https://www.digitalocean.com/community/tutorials/an-introduction-to-linux-i-o-redirection](https://www.digitalocean.com/community/tutorials/an-introduction-to-linux-i-o-redirection)

[https://www.tecmint.com/13-basic-cat-command-examples-in-linux/](https://www.tecmint.com/13-basic-cat-command-examples-in-linux/)

[https://kb.iu.edu/d/afar](https://kb.iu.edu/d/afar)

[https://www.tecmint.com/wc-command-examples/](https://www.tecmint.com/wc-command-examples/)

[https://www.tecmint.com/view-contents-of-file-in-linux/](https://www.tecmint.com/view-contents-of-file-in-linux/)

[http://landoflinux.com/linux_expand_unexpand_command.html](http://landoflinux.com/linux_expand_unexpand_command.html)

[https://www.geeksforgeeks.org/expand-command-in-linux-with-examples/](https://www.geeksforgeeks.org/expand-command-in-linux-with-examples/)

[https://www.thegeekstuff.com/2012/12/linux-tr-command](https://www.thegeekstuff.com/2012/12/linux-tr-command)

[https://www.howtoforge.com/linux-uniq-command/](https://www.howtoforge.com/linux-uniq-command/)

[https://www.tecmint.com/linux-file-operations-commands/](https://www.tecmint.com/linux-file-operations-commands/)

[https://www.tecmint.com/20-advanced-commands-for-linux-experts/](https://www.tecmint.com/20-advanced-commands-for-linux-experts/)

[https://www.ibm.com/support/knowledgecenter/en/ssw_aix\_72/com.ibm.aix.cmds4/nl.htm](https://www.ibm.com/support/knowledgecenter/en/ssw_aix\_72/com.ibm.aix.cmds4/nl.htm)

[https://shapeshed.com/unix-fmt/](https://shapeshed.com/unix-fmt/)

[https://www.tecmint.com/linux-more-command-and-less-command-examples/](https://www.tecmint.com/linux-more-command-and-less-command-examples/)

[https://www.tecmint.com/linux-file-operations-commands/](https://www.tecmint.com/linux-file-operations-commands/)

[https://shapeshed.com/unix-cut/](https://shapeshed.com/unix-cut/)

[https://www.computerhope.com/unix/upaste.htm](https://www.computerhope.com/unix/upaste.htm)

[https://www.howtoforge.com/tutorial/linux-join-command/](https://www.howtoforge.com/tutorial/linux-join-command/)

[https://www.tecmint.com/sed-command-to-create-edit-and-manipulate-files-in-linux/](https://www.tecmint.com/sed-command-to-create-edit-and-manipulate-files-in-linux/)

[https://www.tecmint.com/linux-sed-command-tips-tricks/](https://www.tecmint.com/linux-sed-command-tips-tricks/)

.
