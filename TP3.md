# COMPTE RENDU  
#WU QI&&TAGNE FABIOLA
# TP 3 - Gestion des paquets

### Exercice 1. Variables d’environnement 

1. **Quels sont les 5 derniers paquets installés sur votre machine ?**
La commande pour afficher les 5 derniers paquets installé est `grep installed /var/log/dpkg.log | tail -5`
, nous affichons un historique des paquets installés sur la machine, et en ajoutant le suffixe “| tail -n 5”, nous ne faisons apparaître que les 5 derniers.

```linux
2020-02-28 17:24:13 status installed libarchive13:amd64 3.4.0-1ubuntu0.1
2020-02-28 17:24:13 status installed libc-bin:amd64 2.30-0ubuntu2
2020-02-28 17:24:14 status half-installed libxml2:amd64 2.9.4+dfsg1-7ubuntu3
2020-02-28 17:24:14 status installed libxml2:amd64 2.9.4+dfsg1-7ubuntu3.1
2020-02-28 17:24:14 status installed libc-bin:amd64 2.30-0ubuntu2
```
2. **Utiliser dpkg et apt pour compter le nombre de paquets installés (ne pas hésiter à consulter le manuel !).**
**Comment explique-t-on la (petite) différence de comptage ?**
Commande dpkg: `dpkg --list | grep "^ii  " | wc -l`
Commande apt:  `apt list --installed | grep "$matique]" | wc -l`

Pour apt, le code permettant de compter le nombre de paquets installés est: apt list --installed | wc -l et pour dpkg, il s'agit de: dpkg -l | grep ii | wc -l. Les commandes affichent respectivement 555 et 556 paquets. On constate qu’apt a un paquet de plus mais en fait cela est dû au fait que nous utilisons une méthode qui compte les lignes affichées correspondant au nombre de paquets installés. dpkg -l | wc -l montre aussi les paquets dont il ne reste que les fichiers de configuration. Les deux valeurs 555 et 556 ne sont pas exactes car des lignes n'ayant pas de rapport avec les paquets sont comptées, ce qui augmente le nombre retourné par wc -l sans que cela ait un rapport avec le nombre de paquets réel. Cet écart est toutefois léger (moins de 10).

On a récupéré 556 comme résultat pour toutes les deux commandes.
Car on a ajouté quelques symboles de grep ignorant des lignes inutiles. `^***` C'est pour adapter des lignes commencant avec `***`. Et `$***` c'est pour adapter des lignes derminant avec `***`

3. **Combien de paquets sont disponibles en téléchargement ?**
`apt list |wc -l`
On récupère 61599 comme résultat.

4. **Créer un alias “maj” qui met à jour le système**
Afin de mettre en oeuvre la modification, c'est meilleur de ajouter la ligne suivant dans le fichier.bashrc:
`alias maj='sudo apt update && sudo apt upgrade'`

5. **A quoi sert le paquet fortunes? Installez-le.**
Le paquet fortunes permet d'afficher quelques linges de citations, proverbes, adages dans le CLI.
`sudo apt install fortunes`

6. **Quels paquets proposent de jouer au sudoku ?**
le paquet sudoku
 Les paquets permettant de jouer au sudoku peuvent être connus avec: `apt search sudoku`. Cela affiche en revanche d'autres paquets sans rapport avec le jeu mais présentant “sudoku” dans leur nom. Pour en citer quelques-uns, il y a: sudoku, ksudoku, gnome-sudoku.


7. **Lister les derniers paquets installés explicitement avec la commande apt install.**
`grep "apt install" /var/log/apt/history.log`
```linux
Commandline: apt install sudoku
```

### Exercice 2
**A partir de quel paquet est installée la commande ls ?**
la commande `ls` est installée dans le paquet `coreutils`.
Tout d'abord, il faut savoir ou se trouve `ls`. On peut utiliser `which -a ls`, et après la commande dpkg -S est utilisé pour le chemin /bin/ls(`dpkg -S /bin/ls`).
Pour réunir deux commandes, `xargs` peut être utilisé de passer l'argument entre les deux commandes.
Mais on passe aussi le mauvais résultat. C'est meilleur de le rediriger  au chemin null: `2>/dev/null`.
A la fin, la commande est : 
```linux
which -a $1 | xargs dpkg -S 2>/dev/null
```

### Exercice 3
```shell
#!/bin/bash
if [[ $(dpkg -l |awk '{print $2}') =~ $1]]; then
echo "INSTALLE"
else
echo "NON INSTALLE
fi
```

### Exercice 4
**Lister les programmes livrés avec coreutils. A quoi sert la commande '[' et comment afficher ce qu'elle retourne ?**
La commande `dpkg -L coreutils` est pour lister les programmes livrés avec coreutils.
La commande "[]" réalise un test, elle exécute l'instruction donnée dans les crochets et retourne le résultat ou une erreur.
[ a la même fonctionnalité que test, d'après le manuel, c'est à dire vérifier le type d'un fichier ou comparer des valeurs.
Pour afficher un retour, voici comment faire: [ -f toto.txt ] && echo “oui” || echo “non”.
Si toto.txt existe, cela affichera “oui”, sinon cela affichera “non”.



***
### Exercice 5 : aptitude
**Installez le paquet emacs à l’aide de la version graphique d’aptitude**
1. `sudo apt install aptitude` installer aptitude.
2. toucher `/` pour chercher `emacs`.
3. toucher `g` pour l'installer.

On commence par installer aptitude en rentrant sudo apt install aptitude. Une fois cette opération effectuée, on lance aptitude en administrateur avec sudo aptitude, puis on recherche emacs avec la touche / et on l'installe en se positionnant dessus et en appuyant sur la touche + pour le sélectionner et ensuite sur g.

### Exercice 6. Installation d’un paquet par PPA 
Certains logiciels ne figurent pas dans les dépôts officiels. C’est le cas par exemple de la version ”officielle”
de Java depuis qu’elle est développée par Oracle. Dans ces cas, on peut parfois se tourner vers un ”dépôt
personnel” ou PPA.

1. JAVA11 n'est plus supporté en ligne dans PPA, donc on a changé à java13.
```linux
sudo apt install oracle-java13-installer
```
Après l'installation, on le vérifie par la commande `java --version` et récupère:
```linux
java 13.0.2 2020-01-14
Java(TM) SE Runtime Environment (build 13.0.2+8)
Java HotSpot(TM) 64-Bit Server VM (build 13.0.2+8, mixed mode, sharing)
```
il y a alors un ficher cré qui s'appelle *linuxuprisin-ubuntu-java-eoan.list*,et il contient:
```
deb http://ppa.launchpad.net/linuxuprising/java/ubuntu eoan main
# deb-src http://ppa.launchpad.net/linuxuprising/java/ubuntu eoan main
```

### Exercice 7 : Création de dépôt personnalisé.

Dans cet exercice, vous allez créer vos propres paquets et dépôts, ce qui vous permettra de gérer les
programmes que vous écrivez comme s’ils provenaient de dépôts officiels.

####	Création d’un paquet Debian avec dpkg-deb
```
cd scripts
mkdir origine-commande
cd origine-commande
mkdir Debian
mkdir usr
mkdir usr/local
mkdir usr/local/bin
cd DEBIAN
nano control

***
Package:origine-commande
Version:0.1
Maintainer:WU-QI
Architecture:all
Description:Cherche l'origine d'une commande
Section:utils
Priority:optional
***
```
après l'execusion de la commande dpkg-deb `--build origine-commande`, un message au-desous affiche
```
dpkg-deb: building package 'origine-commande' in 'origine-commande.deb'.
```

####	Création du dépôt personnel avec reprepro
```
mkdir repo-cpe
mkdir repo-cpe/conf
mkdir repo-cpe/packages
nano repo-cpe/conf/distributions

***
Origin: WU-QI CPE
Label: repo-cpe
// Suite: stable
Codename: cosmic # le nom de la distribution cible
Architectures: i386 amd64 # (architectures cibles)
Components: universe # (correspond à notre cas)
Description: hello world
***


cd repo-cpe
sudo apt install reprepro
reprepro -b . export
cp ../scripts/origine-commande.deb packages/
cp ../scripts/origine-commande.deb
sudo nano /etc/apt/sources.list.d/repo-cpe.list

***
deb file:/home/user/repo-cpe cosmic multiverse
***

sudo apt update
```

####	Signature du dépôt avec GPG
1. Commencez par créer une nouvelle paire de clés avec la commande
```
gpg --gen-key
Real name: WU-QI
Email address: qi.wu@cpe.fr
okay
passphrase : 0
```
2. Ajoutez à la configuration du dépôt
On ajoute à la configuration du dépôt la ligne suivante : SignWith: yes (on passe pour cela par la commande nano /home/repo-cpe/conf/distributions, puis on ajoute la ligne voulue).

3. Ajoutez la clé à votre dépôt :
`reprepro --ask-passphrase -b . export`

4. Ajoutez votre clé publique à votre dépôt avec la commande
`gpg --export -a "qi.wu@cpe.fr" > public.key`

5. Enfin, ajoutez cette clé à la liste des clés fiables connues de apt :
`sudo apt-key add public.key`

### Exercice 8. Installation d’un logiciel à partir du code source

```linux
git clone https://github.com/jubalh/nudoku
cd nudoku
sudo apt autoreconf
autoreconf -i
./configure
make
./src/nudoku
```
