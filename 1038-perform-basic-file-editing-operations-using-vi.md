# 103.8. Édition de fichier simple

**Poids:** 3

**Description:** Les candidats doivent être en mesure d'éditer des fichiers texte avec vi. Cet objectif inclut le déplacement dans vi, les modes de base de vi, l'insertion, la modification, la suppression, la copie et la recherche de texte. Cela inclut également la connaissance d'autres éditeurs de texte courants ainsi que la configuration de l'éditeur de texte par défaut.

**Connaissances clés:**

* Déplacement dans un document édité avec vi.
* Compréhension et utilisation des modes de base de vi.
* Insertion, modification, suppression, copie et recherche de texte dans vi.
* Connaissance de base de Emacs, nano et vim.
* Configuration de l'éditeur de texte par défaut.

**Concepts et Utilitaires:**

* vi
* /, ?
* h,j,k,l
* i, o, a
* c, d, p, y, dd, yy
* ZZ, :w!, :q!, :e!

Le besoin d'apprendre comme utilise les éditeurs de texte dans Linux est indiscutable. Chaque administrateur système et ingénieur travaille avec des fichiers de configuration au quotidient, et la plupart du temps cela est fait en utilisant uniquement un ou plusieurs outil de l'interface en ligne de commande (comme **nano**, **vi**, ou **emacs**).

### vi

L'éditeur vi (visual editor) est de manière quasi certaine présente sur chaque système Linux ou UNIX. De fait, si un système n'a qu'un seul éditeur, ce sera probablement vi, donc il n'est pas inutile d'apprendre à connaitre  vi.

En utilisant l'éditeur vi, nous pouvons modifier un fichier existant ou créer un nouveau fichier depuis zéro. Nous pouvons aussi utiliser cet éditeur seulement pour lire un fichier texte.

> L'éditeur vi est bien même dans une session ssh!

#### vi ou vim ?

 La plupart des distributions Linux embarque maintenant l'éditeur vim (pour **V**i **IM**proved) plutôt que le classique vi. Vim est compatible avec vi et a un mode graphique disponible (gvim) ainsi qu'une interface standard en mode texte comme vi. La commande  `vi` est utilisé comme alias ou lien symbolique vers le programme vim.

 Il y a plusieurs versions de vim: tiny, small, normal, big, et huge. Nous pouvons trouver quel version de vim nous sommes entrain d'utiliser et quelles fonctionnalités sont inclues en utilisant la commande :

```
root@ubuntu16-1:~# vi --version
VIM - Vi IMproved 7.4 (2013 Aug 10, compiled Nov 24 2016 16:44:48)
Included patches: 1-1689
Extra patches: 8.0.0056
Modified by pkg-vim-maintainers@lists.alioth.debian.org
Compiled by pkg-vim-maintainers@lists.alioth.debian.org
Huge version without GUI.  Features included (+) or not (-):
+acl             +farsi           +mouse_netterm   +tag_binary
+arabic          +file_in_path    +mouse_sgr       +tag_old_static
+autocmd         +find_in_path    -mouse_sysmouse  -tag_any_white
-balloon_eval    +float           +mouse_urxvt     -tcl
-browse          +folding         +mouse_xterm     +terminfo
++builtin_terms  -footer          +multi_byte      +termresponse
+byte_offset     +fork()          +multi_lang      +textobjects
+channel         +gettext         -mzscheme        +timers
...
```

Afin de lire, créer ou modifier un fichier avec vi, donner lui le nom du fichier :

```
vi filename
```

{% hint style="info" %}
**Si vous taper seulement vi(m) et appuyer sur entrée la version de vi(m) sera affichée :**

```
                              VIM - Vi IMproved                                
~                                                                               
~                               version 7.4.1689                                
~                           by Bram Moolenaar et al.                            
~           Modified by pkg-vim-maintainers@lists.alioth.debian.org             
~                 Vim is open source and freely distributable                   
~                                                                               
~                        Help poor children in Uganda!                          
~                type  :help iccf<Enter>       for information                  
~                                                                               
~                type  :q<Enter>               to exit                          
~                type  :help<Enter>  or  <F1>  for on-line help                 
~                type  :help version7<Enter>   for version info        
```
{% endhint %}

Commençons à apprendre vi(m):

### Modes de vi 

Vim a actuellement trois modes : **mode insertion**, **mode commande**, et **mode escape (last-line)**. Commençons avec le mode par défaut lorsque vous démarrer vi le mode commande démarre.

* **mode commande** :  Lorsque vous lancer vim filename pour modifier un fichier, Vim démarre en mode commande. Cela signifie que toutes les clés alphanumérics sont liés à des commandes, plutôt que d'insérer cs caractères.(enregistrer, quitter, rechercher/remplacer, naviguer, exécuter des macros,...)
* **mode insertion** :  _Pour entrer en mode insertion, taper i (for “insert”)_ et maintenant les frappes sur le clavier se comporteront comme vous l'attendez. Vous pouvez taper normalement sauf si vous voulez faire une correction, enregistrer le ficheir, ou faire une autre opération qui est réservé au mode commande ou au mode escape (last-line). _Pour sortir du mode insertion, appuyer sur la touche Echap._
* **mode escape (last-line)** : Une fois que vous avez appuyez sur Echap, vous entrer dans le mode commande de nouveau. Si vous voulez enregistrer votre fichier ou faire une recherche dans votre document? Pas de problème, _appuyez sur : et Vim passera en mode escape (last-line)_. Vim attend maintenant que vous entriez une commande comme :w pour enregistrer le fichier ou :q pour quitter l'éditeur.

![](.gitbook/assets/performbasicfileop-vimodes.jpg)

Si cela semble compliqué, ça ne l'est pas. Cela prend quelques jours d'entrainement pour que votre cerveau passe d'un mode à l'autre et mémorise les touches les plus importantes pour se déplacer, les commandes et le reste.

####  Déplacer le curseur 

La première chose que vous voudrez apprendre est comment se déplacer dans un fichier. Lorsque vous êtes en mode commande, souvenez vous des touches suivantes et de ce qu'elles font :

| Key    | function                                                       |
| ------ | -------------------------------------------------------------- |
| h      | Déplacer à gauche d'un caractère sur la ligne courantee        |
| j      | Descendre à la prochaine ligne                                 |
| k      | Monter à la ligne précédente                                   |
| l      | Déplacer à droit d'un caractère sur la ligne courante          |
| w      | Se déplacer au prochain mot sur la ligne courante              |
| e      | Se déplacer jusqu'à la fin du prochain mot sur la ligne courante |
| b      | Se déplacer au début du mot précédent sur la ligne courante    |
| Crtl-f | Descendre d'une page                                           |
| Ctrl-b | Remonter d'une page                                            |

>  Si vous taper un nombre avant une de ces commande, alors la commande sera exécuter plusieurs fois. Le nombre est appelé  _compteur de répétitions_ ou simplement _compteur_. Par exemple, 5h déplacer à gauche de 5 caractères. Vous pouvez utiliser le compteur de répétition avec de nombreuses commandes de vi.

#### Sauter  <a href="moving-to-lines" id="moving-to-lines"></a>

| key | function                 |
| --- | ------------------------ |
| H   | Se déplacer en haut de l'écran    |
| M   | Se déplacer au milieu de l'écran |
| L   | Se déplacer en bas de l'écran |

| key | function                                                              |
| --- | --------------------------------------------------------------------- |
| W   | Sauter jusqu'au début d'un mot (le mot peut contenir de la ponctuation)  |
| E   | Sauter jusqu'à la fin d'un mot (le mot peut contenir de la ponctuation)    |
| B   | Revenir au début d'un mot (le mot peut contenir de la ponctuation) |

| key | function                                          |
| --- | ------------------------------------------------- |
| 0   | Revenir au début de la ligne               |
| ^   | Sauter jusqu'au premier caractère non blanc de la ligne |
| $   | Sauter à la fin de la ligne                       |
| g\_ | Sauter au dernier caractère non-blanc de la ligne  |
| gg  | Aller à la première ligne du document  |
| G   | Aller à la dernière ligne du document              |

### Modifier du texte <a href="editing-text" id="editing-text"></a>

Maintenant que nous pouvons ouvrir un fichier dans vi, se déplacer à l'intérieur et sortir, il est temps d'apprendre à modifier le texte du fichier.

| key( pour l'insertion) | function                                                       |
| ------------------- | -------------------------------------------------------------- |
| i                   | Insérer du texte avant le curseur                          |
| I                   | Insérer du texte au début de la ligne           |
| a                   | Ajouter du texte après le curseur                           |
| A                   | Ajouter du texte à la fin de la ligne courante                 |
| o                   | Ouvrir et mettre du texte dans une nouvelle ligne en dessous de la ligne courante |
| O                   | Ouvrir et mettre du texte dans une nouvelle ligne au dessus de la ligne courante |

| key(pour la modification) | function                                                                                    |
| ----------------- | ------------------------------------------------------------------------------------------- |
| r                 | Remplace un seul caractère sous le curseur                                          |
| R                 | Remplacer des caractères, en commençant à la position courante du curseur                       |
| cw                | Modifier le mot courant avec un nouveau texte, en commençant à partir du caractère sous le curseur |
| cNw               | Modifier N mot en commençant avec le caractère sous le curseur; e.g., c5w modifie 5 mots |
| C                 | Modifier (remplacer) les caractère de la ligne courante                             |
| cc                | Modifier (remplacer) la ligne courante en entier                             |
| Ncc or cNc        | Modifier (remplacer) les N prochaines lignes, en commençant avec la ligne courante    |

| key (pour la suppression) | function                                                                        |
| ------------------ | ------------------------------------------------------------------------------- |
| x                  | Supprimer un seul caractère sous le curseur                                            |
| Nx                 | Supprimer N caractères, en commençant avec le caractère sous le curseur                       |
| dw                 | Supprimer le mot qui commence avec le caractère sous le curseur                    |
| dNw                | Supprimer N mots en commençant avec le caractère sous le curseur; e.g., d5w supprime 5 mots |
| d^                 | Supprimer du début de la ligne jusqu'au curseur                                           |
| d$ and D           | Supprimer le reste de la ligne, en commençant avec la position courante du curseur |
| dd                 | Supprimer la ligne courante en entier                                                      |
| Ndd or dNd         | Supprimer N lignes, en commençant par la ligne courante; e.g., 5dd supprime 5 lignes      |

| key(pour couper et coller)  | function                                                                       |
| ----------------------------- | ------------------------------------------------------------------------------ |
| yy                            | Copier (couper) la ligne courante dans le tampon                              |
| Nyy or yNy                    | Copier (couper) les N prochaines lignes, en incluant la ligne courantes, dans le buffer |
| p                             | Copier les lignes depuis le tampon vers le texte après la ligne courante     |
| P                             | Copier les lignes depuis le tampon dans le texte avant la ligne courante    |

### Recherche

| key      | function                                                       |
| -------- | -------------------------------------------------------------- |
| /string  | Rechercher en avant pour une occurence d'une chaine dans le texte                |
| ? string | Rechercher en arrière pour une occurence d'une chaine dans le texte               |
| n        | Se déplacer à la prochaine occurence de la chaine recherchée                       |
| N        | Se déplacer à la prochaine occurence de la chaine recherchée dans la direction opposée |

### Remplacement

| key             | function                                                    |
| --------------- | ----------------------------------------------------------- |
| :s/old/new/     | Remplacer la premier ancienne par la nouvelle seulement dans la ligne courante               |
| :s/old/new/g    | Remplacer toutes les anciennes avec les nouvelles seulement dans la ligne courante                 |
| :%s/old/new/g   | Remplacer remplacer tous les anciens par les nouveaux dans le fichier                    |
| :%s/old/new/gc  | Remplacer tous les anciens par les nouveaux dans le fichier avec des confirmations |
| :%s/old/new/gic | Idem que le précédent mais insensible à la casse                          |
| :noh            | Supprimer le surlignement des correspondances de recherche                       |

### Quitter

Si vous êtes en mode insertion, appuyer sur Echap. Puis entrer `:` et vous verrez une ligne en bas de l'écran avec un curseur qui attend une entrée.

| key        | function                                                                     |
| ---------- | ---------------------------------------------------------------------------- |
| :w         | Cela écrit le fichier avec le nom de fichier existant mais ne quite pas.           |
| :w  _file_ | Si vous n'avez pas de nom de fichier ou vous voulez écrire avec un _nom de fichier_ différent |
| :q         | Qutier (échoue si vous aves dez modifications non enregistrées)                                    |
| :q!        | Quitter l'éditeur sans enregistrer.                                                 |
| :wq        | Ecrire (enregistrer) et quitter (renvoit une erreur si le fichier ne peut pas être écrit)              |
| :wq!       | Essaie d'écrire et quitter (s'il est en lecture seul, quitte sans enregistrer! )         |
| ZZ and :x  | Sort et enregistre le fichier s'il est modifié                                           |
| q! or ZQ   | Quitte et annule les modifications non enregistrées                                          |

Appuyez sur ESC pour revenir au mode normal de commande!

> Lorsque vous voulez écire et quitter un fichier, considérer vos permissions, la commande suivante écrit le contenu du fichier en utilisant sudo :
>
> :w !sudo tee % 
>
> * :w – Ecrit un fichier (dans le tampon). 
> * !sudo – Appel des commandes shell avec les droits d'admininistration. 
> * tee – La commande de sortie de l'écriture (vim :w) est redirigé en utilisant tee. 
> * % – Le % n'est rien d'autre que le nom du fichier.

### Rechargement

| key | function                                           |
| --- | -------------------------------------------------- |
| :e  | (raccourci pour `:edit`) recharge le fichier depuis le disque |
| :e! | Annule les changements locaux et recharge.          |

{% hint style="success" %}
### Astuce VIM : lancer des commandes externes dans un shell

Il arrive que nous ayons besoin de lancer une commande dans un shell et de voir son résultat dans le fichier que nous éditons. On peut le faire de deux manières :

1. Nous pouvons suspendre notre session (ctrl-Z), et lancer la commande dans notre shell. Cela fait beaucoup de raccourci claviers!

2. A la place, nous pouvons utiliser l'outil fourni par vim “run a shell command”:

> :!cmd Lance une commande shell, montre la sortie et l'invite de commande avan de retourner le résultat au tampon.
{% endhint %}

### Aide

Vous avez besoin d'aide? Vous pouvez utiliser `:help` ou `:help keyword` et vim ouvrira l'aide pour les mots clés.

.

.

.

[https://developer.ibm.com/tutorials/l-lpic1-103-8/](https://developer.ibm.com/tutorials/l-lpic1-103-8/)

[https://www.tecmint.com/learn-vi-and-vim-editor-tips-and-tricks-in-linux/](https://www.tecmint.com/learn-vi-and-vim-editor-tips-and-tricks-in-linux/)

[https://www.geeksforgeeks.org/vi-editor-unix/](https://www.geeksforgeeks.org/vi-editor-unix/)

[https://www.linux.com/tutorials/vim-101-beginners-guide-vim/](https://www.linux.com/tutorials/vim-101-beginners-guide-vim/)

[https://vim.rtorr.com/](https://vim.rtorr.com)

[https://www.cs.colostate.edu/helpdocs/vi.html](https://www.cs.colostate.edu/helpdocs/vi.html)

[https://www.endpoint.com/blog/2009/03/10/vim-tip-of-day-running-external](https://www.endpoint.com/blog/2009/03/10/vim-tip-of-day-running-external)

.
