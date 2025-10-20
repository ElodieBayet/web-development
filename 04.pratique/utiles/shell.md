
# Pratique : Shell

## Sommaire
1. [Généralités](#généralités)
    - [Commandes](#commades)
1. [Scripts](#scripts)
    - [Base](#base)
    - [Conditions](#conditions)
    - [Boucles](#boucles)
1. [Annexes](#annexes)
    - [Glossaire](#glossaire)
    - [Pour en savoir plus](#pour-en-savoir-plus)

---


## Généralités

_Shell_ désigne un interpréteur de commande dans les systèmes _Unix_. _Bash_ est l'un des _Shell_ existant dans _Unix_. Mais aussi _zsh_ ou _csh_.
Originellement
```
	   -- › csh › tcsh ›
	sh -- › ksh ------ › zsh
	   -- › bash ----- › 
```

Ces interpréteurs de commandes sont des programmes, et ont donc parfois leurs propre commandes – attention à la compatibilité.

2 types de commandes sont supportées
- interne : implémentée directement dans le terminal
- externe : faisant appel à un utilitaire stocké sur le disque

Par défaut le shell d'un système _Unix_ démarre depuis le répertoire de l'User qui se connecte.


### Commades

Un _Shell_ supporte des commandes internes qu'il implémente directement et des commandes externes qui font appel à un utilitaire stocké sur disque.
Exemple d'une commande interne
```sh
cd ./monDossier
```
Sa syntaxe est `cd(posix)`, où posix est le positionnement de `x` dans le répertoire ciblé.

Exemples de commandes externes
```sh
cat sample.txt
```
Syntaxe : `cat({options}, fichier)`, afficher le contenu d'un (ou plusieurs) fichier(s)

```sh
grep fichier sample.txt

# Toutes les lignes qui ne contiennent pas 'fichier'
grep -v fichier sample.txt
```
Syntaxe : `grep({options}, pattern, fichier)`, extraire les lignes de texte qui contiennent ou pas un pattern

```sh
wc sample.txt
```
Syntaxe : `wc({options}, fichier)`, compter le nombre de caractères et de lignes


#### Entrées et sorties

I/O standards
- `stdin` : l'entrée standard, par défaut le clavier
- `stdout` : la sortie standard, par défaut le terminal, flux de sortie 1
- `stderr` : la sortie standard, par défaut le terminal également, flux de sortie 2

I/O modifiés
- `<` : entrée, on écrit la commande suivie par `<` puis du nom du fichier à lire
- `>` ou  `>>` : sortie, la commande suivie par `>` ou `>>` puis du nom de fichier où écrire/sauvegarder le résultat
    - si le fichier n'existe pas, il sera créé
    - s'il existe, il sera réinitialiser (écrasé). Sauf si `>>` est utilisé et que le fichier existe, alors la sortie sera ajoutée à la fin du fichier

Les combinaisons de commandes sont possibles avec _Shell_. Comme `sort(1)` et `head(1)` qui trient et affichent les premiers noms d'une liste
Exemple
```sh
echo "Un petit fichier text" > test.txt
echo "Aaaaa Bbbbb" >> test.txt
echo "Ccccc Ddddd" >> test.txt
grep -v bbbb < text.txt > out.txt
```
Dans cet exemple, la `stderr` reste dirigée vers le terminal, il faut ajouter `2> err.txt` pour "logguer" les erreurs dans un fichier. 
Ou alors, utiliser le même fichier en redirigeant d'abord les erreurs vers `stdout` puis vers le fichier : `2>&1 > out.txt`.

Autre mécanisme de combinaisons de commandes, la `stdout` d'un programme redirigée vers la `stdin` d'un autre programme sans passer par un fichier intermédiaire : `|` (l'opérateur pipe).
Exemple
```sh
echo "Un fichier texte" | wc -c
cat sample.txt | sort | uniq # 'uniq' c'est pour filtrer, retirer les lignes en double
```


---


## Scripts

_Shell_ pour être utilisée comme interpréteur de scripts. Sous _Unix_, les 2 premiers octets d'un fichier déterminent si le fichier est un exécutable ou pas. Donc, les scripts doivent commencer par `#!` suivi de l'interpréteur
```sh
#!/bin/bash
echo "Hello World !"
```


### Base

Variables d'un script _Shell_
```sh
#!/bin/bash
PROG="Linfo"
COURS=1252
echo $PROG$COURS # '$' réfère à la variable du même nom
```

Pour éviter tout risque d'ambiguité, le nom de la variable peut être placé entre `{}`
```sh
#!/bin/bash
milieu="mi"
echo do${milieu}no # sinon, l'interpréteur considère la variable 'milieuno' au lieu de 'milieu'
```

Arguments = variables
- `$#` : la qté d'arguments passés au script bash
- `$1 $2 ...` : chaque argument dans l'ordre de leurs déclarations
- `$@` : la liste de tous les arguments

Exemple
```sh
#!/bin/bash
PROG="Linfo"
COURS=1252
echo "Vous avez" $# "arguments"
echo "Le 1er est" $1
echo "La liste complète est" $@
```
Si on appelle un script en redirigeant sa `stdin` ou sa `stdout`, le script n’est pas "informé" de cette redirection, ainsi, si l’on exécute le script précédent en faisant `args.sh arg1 > args.txt`, le fichier "args.txt" contient le même résultat qu'une exécution simple du script.


### Conditions

Syntaxe
```sh
if [cond]; then... fi
```
Quelques conditions
- `$i -eq $j` : est vrai si les deux variables contiennent le même nombre numérique
- `$i -lt $j` : est vrai si `$i` est un nombre inférieur à `$j`
- `$i -gt $j` : est vrai si `$i` est un nombre supérieur à `$j`
- `$i -ge $j` : est vrai si `$i` est un nombre supérieur ou égal à `$j`
- `$i = $j` : est vrai si la chaine `$i` est la même que la chaine `$j`
- `-z $s` : est vrai si `$s` est une chaine vide
- `$i -ne $j` : est vrai si le nombre `$i` n'est pas égal à `$j`

Exemple
```sh
#!/bin/bash
if [ $# -ne 2 ]; then
    echo "Erreur : 2 arguments sont nécessaires" > /dev/stderr
    exit 2 
fi
if [ $1 -ep $2 ]; then
    echo "Nombres égaux"
else
    echo "Nombre différents"
fi
exit 0
```

Rappel : chaque programme renvoie un code de retour égal à 0 si tout est ok.
- Valeur de retour `0` : R.A.S. et le programme a été testé vrai
- Valeur de retour `1` : R.A.S, mais le programme a été testé faux
- Valeur de retour `2` : une erreur s'est produite

Exemple 
```sh
#!/bin/bash
# wordin.sh
# vérifie si un mot passé en $1 est présent dans le fichier passé en $2
if [ $# -ne 2 ]; then
    echo "Erreur : 2 arguments sont nécessaires" > /dev/stderr
    exit 2 
fi

grep $1 $2 > /dev/null # on envoie le résultat dans un "trou noir" :D

# $? contient la valeur de retour de grep
if [ $? -ep 0 ]; then
    echo "Le mot" $1 "est présent"
    exit 0
else
    echo "Le mot" $1 "est absent"
    exit 1
fi
```

Tester les fichiers
- `-f my_file` : est vrai si 'my_file' est un fichier
- `-s my_file` : est vrai si 'my_file' n'est pas vide, ou vrai si 'my_file' est un nom de répertoire
- `-r my_file`, `-w my_file`, `-x my_file` : est vrai si 'my_file' peut être lu, écrit, exécuté
- `-d my_dir` : est vrai si 'my_dir' est un répertoire

Exemple
```sh
#!/bin/bash
# Vérifie si un fichier contient des données
if [ $# -ne 1 ]; then
    echo "Erreur : 1 argument est nécessaire" > /dev/stderr
    exit 2 
fi
if [ -d $1 ]; then
    echo "Non, $1 est un répertoire" > /dev/stderr
    exit 2
fi
if [ ! -f $1 ]; then
    echo "Non, $1 n'existe pas" > /dev/stderr # si fichier était un rép. le 2e if l'aurai attrapé
fi 
if [ -s $1 ]; then
    echo "Le fichier $1 contient des données" > /dev/stdout
else
    echo "Non, le fichier $1 ne contient pas de données" > /dev/stdout
fi
```


#### Opérateurs

Logiques
- `-a` : 'AND', conjonction
- `-o` : 'OR', disjonction
- `!` : 'NOT', négation

```sh
if [ -d $1 -o !-f $1 ]; then
    echo "Erreur : $1 n'existe pas ou alors ce n'est pas un fichier mais un répertoire" > /dev/stderr
    exit 2
fi
```

Rappel : les programmes et les tests logiques renvoient `0` pour 'vrai'. Sinon, ce sont des codes d'erreurs – donc supérieur à `0`.


### Boucles

Syntaxe
```sh
for {i} in {liste}; do... done
```

Exemple 
```sh
#!/bin/bash
all_students="Julie Maxime Sarah"
for student in $all_students; do
    total_lines=`wc -l TP1-$student.txt | cut -d' '-f1`
    echo "Bonjour $stu, ton compte rendu TP comporte $total_lines lignes."
done
```

Explications
- `for student in $all_students` : prendra chaque élément séparé par des espaces
- `wc -l TP1-$student.txt` : compte le nombre de lignes, option `-l`, de chaque fichier au nom de l'étudiant
- `cut -d` : isole la 1re 'colonne' d'information qui est séparée des suivantes par un espace, avec l'option `-d`. La commande précédente contenait trop d'information, il fallait extraire le nombre de lignes seules, et stocker sont résultat dans `$total_lines`
`|` : opérateur 'pipe', le `stdout` de `wc -l...` est passé à la commande `cut...`

Constructions utiles
- ` : les apostrophess inversées pour récupérer le résultat d'une commande qu'ils englobent
- `*` : représente n'importe quelle chaine de caractères, même vide, `ab*yz` peut valoir 'abCDEyz', ou 'abyz'
- `?` : représente 1 caractère obligatoire, `ab?ce` peut valoir 'abXde' mais pas 'abce'

```sh
#!/bin/bash
for f in TP1-*.txt; do
    echo "Génération de la signature du fichier $f..."
    sha1sum $f > $f.sha1
done
```


---
---


## Annexes

### Glossaire


### Pour en savoir plus

- [Syllabus LEPL1503/LINFO1252 : Shell Scripts - UCLouvain](https://sites.uclouvain.be/SystInfo/notes/Theorie/html/shell/shell.html#scripts-les-bases)