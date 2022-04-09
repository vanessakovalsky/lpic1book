# 103.7. Recherche dans des fichiers texte avec les expressions rationnelles

**Poids:** 3

**Description:** Les candidats doivent être en mesure de manipuler des fichiers et des données de type texte en utilisant des expressions rationnelles. Cet objectif inclut la création d'expressions rationnelles simples contenant différents caractères de notation ainsi que la compréhension des différences entre les expressions rationnelles de simples et étendues. Cela inclut également l'utilisation des expressions rationnelles dans des commandes pour effectuer des recherches dans une arborescence ou dans le contenu d'un fichier.

**Connaissances clés:**

* Création d'expressions rationnelles simples contenant différents éléments de notation.
* Compréhension des différences entre les expressions rationnelles simples et étendues.
* Compréhension des concepts de caractères spéciaux, classes de caractères et ancres.
* Utilisation des expressions rationnelles dans des commandes pour effectuer des recherches dans une arborescence ou dans le contenu d'un fichier.
* Utilisation des expressions rationnelles pour supprimer, modifier et substituer du texte.

**Concepts et Utilitaires:**

* grep
* egrep
* fgrep
* sed
* regex(7)

Alors que nous travaillons avec des fichiers texte, il arrive souvent que nous recherchions du texte dans un fichier texte. Nous pouvons rechercher quelque chose qui n'est pas spécifique. Par exemple, nous recherchons "linux" ou "Linux" ou peu importe, c'est là que les expressions régulières sont utiles.(la section légère la plus importante!)

### Expression régulière

Les expressions régulières sont utilisés lorsque nous voulonre rechercher des lines de texte spécifique qui contiennent un certain modèle. Regex peut être utilisé avec de nombreux programme comme like grep, sed, vi, bash, rename et beaucoup d'autres. Ici nous voyons l'utilisation de regex avec la commande grep.

 Un modèle regex utilise un **moteur d'expression régulière** qui traduit ce modèle.

Linux a deux moteurs d'expressions regulières :

* Le moteur **Basic Regular Expression (BRE)**.
* Le moteur **Extended Regular Expression (ERE)**.

> Il y a une différente entre les expressions régulières basiques et étendues. Certains utilitaires sont écrit pour supporté seulement les expressions régulières basique(BRE) et d'autres utilitaires sont écrit pour supporté les expressions régulières étendues (ERE) également. La plupart des programmes Linux fonctionne ave les spécifications du moteur BRE. Avec GNU grep, il n'y a pas de différence fonctionnelle.

## De quoi est composé une expression régulière

Il y a deux types de caractères qui peuvent être trouvés dans les expressions régulières :

* caractère litéral
* metacaractères

**Caractères litéraux** sont les caractères standard qui compose nos chaines de caractères. Chaque caractère dans une phrase est un caractère litéral. Vous pouvez utiliser une expression régulière pour rechercher chaque caractère litéral dans cette chaine.

**Meta caractères** sont une bête différente; ils sont ce qui donne aux expressions régulières leur puissance. Avec les meta caractères, nous pouvons faire beaucoup plus que de chercher un seul caractère. Les méta caractères nous permetten de chercher des combinaisons de chaines et bien d'autres choses. 

## grep

grep signifie **g**eneral **r**egular **e**xpression **p**arser. Le filtre grep recherche dans un fichier un modèle spécifique de caractère et affiche toutes les lignes qui contiennent ce modèle. grep est un utilitaire qui tire de nombreux bénéfices des expressions régulières.

```
grep [options] pattern [files]
```

Voyons quelques exemples :

| command                          | description            |
| -------------------------------- | ---------------------- |
| echo "linux is my os" \| grep l  | **l**inux is my os     |
| echo "linux is my os" \| grep i  | l**i**nux **i**s my os |

 **Concatenation**

 La concaténation de deux expressions régulières crées une expression plus longue. 

| regex                                    | match                              |
| ---------------------------------------- | ---------------------------------- |
| echo "aa ab ba aaa bbb AB BA" \| grep a  | **aa a**b b**a** **aaa** bbb AB BA |
| echo "aa ab ba aaa bbb AB BA" \| grep ab | aa **ab** ba aaa bbb AB BA         |

```
Options Description
-c : This prints only a count of the lines that match a pattern
-h : Display the matched lines, but do not display the filenames.
-i : Ignores, case for matching
-l : Displays list of a filenames only.
-n : Display the matched lines and their line numbers.
-v : This prints out all the lines that do not matches the pattern
-e exp : Specifies expression with this option. Can use multiple times.
-f file : Takes patterns from file, one per line.
-E : Treats pattern as an extended regular expression (ERE)
-w : Match whole word
-o : Print only the matched parts of a matching line,
 with each such part on a separate output line.
```

### Expressions régulières étendues(Extended Regular Expressions)

 **Repetition**

* Le **\*** signifie que l'item précédent correspond  **0** **ou plusieurs** fois.
* Le **+** signifie que l'item correspond **1 ou plusieurs** fois.
* Le **?** signifie que l'item précédent correspond, **0 ou 1** fois.

{% hint style="info" %}
#### Jokers et Regex: Si similiaire, si différent

 Les débutants confondent parfois les **jokers**(globbing) avec les **expressions régulières** lorsqu'il utilise grep mais ce n'est pas la même chose.\
 Les **jokers** sont une fonctionnalité fourni par le shell pour continuer des noms de fichier alors que les expressions regulières sont des mécanismes de filtres du texte conçu pour être utilisé avec des utilitaires comme like grep, sed et  awk.
{% endhint %}

| Special Character | Sens dans les Globs                | Sens dans Regex                                             |
| ----------------- | ---------------------------------- | ----------------------------------------------------------- |
| **\***            | zero ou plus de caractères         | zero ou plus du caractères qu'il suit                       |
| **?**             | occurence unique de n'importe quel caractère | zero ou plus du caractère qu'il suit mais pas plus que 1 |
| **.**             | caractère literal "."              | n'importe quel caractère unique                             |

Afin d'éviter toute erreur en utilisant les expression régulières étendues, utiliser `grep` avec l'option ` -E`, `-E` traite le modèle comme une expression regulière étendue(ERE).

{% hint style="info" %}
**Guillemets double " "** : Nous avons besoin de mettre notre modèle regex étendu entre guillemets double, autrement il peut être interprété par shell et nous donner un résultat différent. 
{% endhint %}

| regex                                           | match                              |
| ----------------------------------------------- | ---------------------------------- |
| echo "aa ab ba aaa bbb AB BA" \| grep -E "a\*b" | aa **ab** **b**a aaa** bbb **AB BA |
| echo "aa ab ba aaa bbb AB BA" \| grep -E  "a+b" | aa **ab** ba aaa bbb AB BA         |
| echo "aa ab ba aaa bbb AB BA" \| grep -E "a?b"  | aa **ab** **b**a aaa **bbb** AB BA |

C'est une des raison de l'arrivée de egrep:

{% hint style="success" %}
## egrep

 **egrep** est une commande de recherche de modèle qui appartient à la famille des fonctions grep. Il fonctionne de la même manière que le fait **`grep -E`**. Il traite le modèle comme une expression régulière étendue et affiche les lignes qui correspondent au modèle. S'il y a plusieurs fichiers avec le modèle correspondant il affiche aussi le nom du fichier pour chaque ligne.

```
egrep [ options ] 'PATTERN' files 
```

 **Options:** La plupart des options de cette commande sont les même que grep.

Donc au lieu d'utiliser la commande grep -E nous utiliserons plus facilement egrep.
{% endhint %}

#### Accolades (Curly Braces)

Les accolades nous permettent de spécifier le nombre d'existence pour un modèle, il a trois formats :

* **{N}** signifie que l'item précédent correspond _exactement_ **N fois**.
* **{N,}** signifie que l'item précédent correspond **N ou plusieurs fois**.
* **{N,M}** signifie que l'item précédent correspond _au moins_ **N fois**, mais pas plus que _ **M fois**_.

| regex                                             | match                                   |
| ------------------------------------------------- | --------------------------------------- |
| echo "ab aab aaab aaaab ba Ab" \| egrep  "a{1}b"  | **ab** a**ab** aa**ab** aaa**ab** ba Ab |
| echo "ab aab aaab aaaab ba Ab" \| egrep "a{2,}b"  | ab **aab** **aaab** **aaaab** ba Abb    |
| echo "ab aab aaab aaaab ba Ab" \| egrep "a{1,3}b" | **ab aab aaab** a**aaab** ba Ab         |

**N'importe quel caractère**

Le .(point) est un méta caractère qui signidie n'importe quel caractère (à l'exception d'une nouvelle ligne `\n` )

* Le  **.** correspond à **n'importe quel caractère**. 

> Un des modèles les plus utilisé est .**‘\*’**, qui correspond à une longueur arbitraire d'une chaine qui contient n'importe quel caractère (ou aucun caractère)

| regex                                         | match                     |
| --------------------------------------------- | ------------------------- |
| echo "ac abc aaabbbccc abcz" \| egrep "a.\*c" | **ac abc aaabbbccc abc**z |

**Caractère ancre (Anchor Characters)**

Les caractères ancres pour le positionnement facile des caractères sur une ligne sont utiliser pour localiser le début ou la fin d'une ligne dans un texte :

  
* **^** correspond au début de la ligne.
* **$**  correspond à la fin de la ligne.

```
root@ubuntu16-1:~# cat text
abc
bcd
efg
root@ubuntu16-1:~# egrep "^b" text
bcd
root@ubuntu16-1:~# egrep "g$" text
efg
```

**Alternance(Alternation) (plusieurs chaînes)**

L'opérateur d'alternance (|) correspond soit à l'expression précédente soit à l'expression suivante. Par exemple :

* **cat|dog** correspond à **cat ou dog**.

| regex                                            | match                     |
| ------------------------------------------------ | ------------------------- |
| echo "cat dog was a cartoon" \| egrep "cat\|dog" | **cat dog** was a cartoon |

### Classe de caractères

Nous pouvons chercher n'importe quel caractère avec le caractère spécial point, mais et si vous souhaitez qu'il fasse partie d'un ensemble de caractère seulement ? Nous pouvons utiliser une classe de caractère. La classe de caractères correspond à un ensemble de caractère, si l'un d'eux est trouvé, le modèle correspond. La classe de caractère est défini en utilisant des crochets \[ ]

**Expressions entre crochets**

En plaçant des groupe de caractère entre crochets ("\[" et "]"), nous pouvons spécifieer que le caractère à cette position peut être n'importe quel caractère présent dans les crochets.

*  **\[set_of_characters]**  Correspond à un seul caractère dans _**set_of_characters**_. Par défaut, la correspondance est sensible à la casse.

| regex                                              | match                     |
| -------------------------------------------------- | ------------------------- |
| echo "cat dog was a cartoon" \| egrep "cart\[o]\*" | cat dog was a **cartoo**n |

### Nier une classe de caractère

Et si nous cherchons un caractère qui n'est pas dans la classe de caractère? Pour cela, précèder la classe de caractère avec un accent circonflexe **^.**

*  **\[^set_of_characters]** _Negation:_ Correspond à n'importe quel caractère qui **n'est pas** dans _**set_of_characters**_. Par défaut la correspondance est sensible à la casse.

| regex                                                 | match                     |
| ----------------------------------------------------- | ------------------------- |
| echo "cat dog was a cartoon" \| grep ".\*\[^cartoon]" | **cat dog was a** cartoon |

**Expression d'ensemble (Range expression)**

Pour spécifier un rang de caractères, nous pouvons utiliser le symbole tiret **(-)**, comme 0-9 pour les chiffres. Noter que le rang est dépendant de la langue.

| regex                                        | match                               |
| -------------------------------------------- | ----------------------------------- |
| echo "a12345a a54321a 123" \| egrep "\[a-z]" | **a**12345**a** **a**54321**a** 123 |

###  Classe de caractères spéciales (**Classe nommées)**

Plusieurs classe nommé fournissent un raccourci pratique pour les classes communément utilisé. Les classes nommés s'ouvre avec \[: et se referme avec :] et peuvent être utilisé avec des accolades. Quelques exemples :

* **\[\[:alnum:]]** Caractères Alphanumeriques .
* **\[\[:alpha:]]** Caractères Alphabetiques.
* **\[\[:blank:]]** Espace et caractère de tabulation.
* **\[\[:digit:]]** Les chiffres de 0 à 9 (équivalent à 0-9).
* **\[\[:upper:]]** et **\[:lower:]** Casse de lettre en majuscules ou minicules respectivement.

| **regex**                                                 | match                               |
| --------------------------------------------------------- | ----------------------------------- |
| echo "a12345a a54321a 123 678 9" \| egrep "\[\[:alpha:]]" | **a**12345**a** **a**54321**a** 123 |

**Groupes d'expressions**

En plaçant une partie d'expression régulière entre parenthèses, nous pouvons grouper des parties d'expressions regulière ensemble.

* **() Groupes d'expressions régulières**

| regex                                                    | match                         |
| -------------------------------------------------------- | ----------------------------- |
| echo "a12345a a54321a 123 678 9" \| egrep "(1a.\*)\|(9)" | a12345a a5432**1a 123 678 9** |

### **Caractères spéciaux**

Les modèles regex utilise des caractères spéciaux. Et nous ne pouvons pas les inclure dans nos modèle, et si nous le faisons, nous n'aurons pas les résultats attendus. Ces caractères spéciaux sont reconnu par regex :

**.\*\[]^${}\\+?|()**

Et comment  nous pouvons rechercher pour ceux-là dans un texte ? C'est là que fgrep entre en jeu.

### fgrep

 Si vous ne voulez pas la puissance de regex, cela peut être très frustrant. C'est particulièremen vrai si vous rechercher certains des caractères spéciaux dans un morceau de texte. Vous pouvez utiliser la commande `fgrep`  (ou `grep -F`, ce qui est la même chose) afin d'annuler certaines substitutions de regex, vous chercherez exactement ce que vous avez taper, meême s'il y a des caractères spéciaux.

```
fgrep [options] [ -e pattern_list] [pattern] [file]
```

```
    -c : It is used to print only a count of the lines which contain the pattern.
    -h : Used to display the matched lines.
    -i : During comparisions, it will ignore upper/lower case distinction.
    -l : Used to print the names of files with matching lines once, separated by new-lines. It will not repeat the names of files when the pattern is found more than once.
    -n : It is used precede each line by its line number in the file (first line is 1).
    -s : It will only display the error messages.
    -v : Print all lines except those contain the pattern.
    -x : Print only lines matched entirely.
    -e pattern_list : Search for a string in pattern-list (useful when the string begins with a “-“).
    -f pattern-file : Take the list of patterns from pattern-file.
    pattern : Specify a pattern to be used during the search for input.
    file : A path name of a file to be searched for the patterns. If no file operands are specified, the standard input will be used.

```

Par exemple:

```
root@ubuntu16-1:~# cat text
^abc
bcd$
efg|
root@ubuntu16-1:~# fgrep "^" text
^abc
root@ubuntu16-1:~# fgrep "$" text
bcd$
```

### sed

 La commande Sed  ou **S**tream **Ed**itor est un utilitaire très puissant fournit par les systèmes Linux/Unix. Il est principalement utilisé pour de la **substitution de texte**, **rechercher & remplacer** mais il peut aussi faire d'autre manipulations de texte comme **insertion**, **suppression**, **recherche**, etc. 

Avec **sed**, nous pouvons modifier des fichiers complet sans avoir besoin de l'ouvrir. Sed supporte aussi l'utilisation d'expressions régulières, ce qui le rend encore plus puissant comme **outil de manipulation du texte**.

 Nous avons précédemment utilisé sed pour de la substitution de texte, ici nous voulons utiliser le regex avec ça.

```
sed OPTIONS... [SCRIPT] [INPUTFILE...]
```

 `-r, --regexp-extended ` dit à sed que nous utilisons des **expressions régulières** dans ce script.

```
root@ubuntu16-1:~# cat text
Comment this is a  comment:
comment pigs can not fly because the sky is high!

root@ubuntu16-1:~# sed -r "s/^(Comment|comment)/\#/" text
# this is a  comment:
# pigs can not fly because the sky is high!
```

Par défaut, sed affiche chaque ligne. S'il fait une substitution, le nouveau texte est affiché à la place de l'ancien. Si vous utiliser l'argument optionnel de sed, "sed -n," il ne le fera pas:

```
root@ubuntu16-1:~# sed -rn "s/^(Comment|comment)/\#/"  text
root@ubuntu16-1:~# 
```

Lorsque l'option "-n" est utilisé, le flag "p" permettra de n'afficher que les lignes modifiées :

```
root@ubuntu16-1:~# sed -rn "s/^(Comment|comment)/CHANGED/p"  text
CHANGED this is a  comment:
CHANGED pigs can not fly because the sky is high!
```

sed est un outil craiment puissant et pas si compliqué, nous avons juste eu un aperçu ici! 

.

.

.

[http://www.grymoire.com/Unix/Regular.html](http://www.grymoire.com/Unix/Regular.html)

[https://www.linux.com/tutorials/introduction-regular-expressions-new-linux-users/](https://www.linux.com/tutorials/introduction-regular-expressions-new-linux-users/)

[https://linux.die.net/Bash-Beginners-Guide/sect\_04\_01.html](https://linux.die.net/Bash-Beginners-Guide/sect\_04\_01.html)

****[https://dzone.com/articles/35-examples-of-regex-patterns-using-sed-and-awk-in](https://dzone.com/articles/35-examples-of-regex-patterns-using-sed-and-awk-in)

[https://www.digitalocean.com/community/tutorials/using-grep-regular-expressions-to-search-for-text-patterns-in-linux#regular-expressions](https://www.digitalocean.com/community/tutorials/using-grep-regular-expressions-to-search-for-text-patterns-in-linux#regular-expressions)

[https://www.linuxnix.com/10-file-globbing-examples-linux-unix/](https://www.linuxnix.com/10-file-globbing-examples-linux-unix/)

[https://www.linuxjournal.com/content/globbing-and-regex-so-similar-so-different](https://www.linuxjournal.com/content/globbing-and-regex-so-similar-so-different)

[https://www.zyxware.com/articles/4627/difference-between-grep-and-egrep](https://www.zyxware.com/articles/4627/difference-between-grep-and-egrep)

[https://www.geeksforgeeks.org/fgrep-command-in-linux-with-examples/](https://www.geeksforgeeks.org/fgrep-command-in-linux-with-examples/)

[https://www.linuxtechi.com/20-sed-command-examples-linux-users/](https://www.linuxtechi.com/20-sed-command-examples-linux-users/)

[http://www.grymoire.com/Unix/Sed.html](http://www.grymoire.com/Unix/Sed.html)

[https://www.linuxtechi.com/20-sed-command-examples-linux-users/](https://www.linuxtechi.com/20-sed-command-examples-linux-users/)

.
