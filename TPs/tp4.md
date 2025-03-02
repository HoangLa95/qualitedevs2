# TP4 : Build system et documentations

:::{note} Intégration Git dans un IDE
:class: dropdown
Encore une séance dans le terminal !
:::

:::{important} `.gitignore`  
Nous allons créer des fichiers `makefile` sans extension. Il est donc nécessaire d'ajouter `!makefile` au `.gitignore` afin de ne pas les ignorer, tout en continuant à exclure les fichiers exécutables.
De plus, nous allons générer les répertoires `build/` et `docs/`, qui contiendront des fichiers de compilation. Ceux-ci doivent être ignorés.  
:::

(tp4-objectifs)=
Le but de ce TP est de comprendre les points suivants :
- [ ] [Les commandes `make`](#tp4-make-commands)
- [ ] [Le fonctionnement de Makefile](#tp4-make-behavior)
- [ ] [L'organisation des fichiers de compilation](#tp4-build-directory)
- [ ] [L'organisation des fichiers de code](#tp4-project-organization)
- [ ] [README](../Cours/cm4.md/#readme)
- [ ] [Documentations Doxygen/Javadoc](../Cours/cm4.md/#documentation-du-code)

## Exercice 1 : Makefile

Voici un exemple minimal d'un projet en C++ :
```{code} md
minimal-project/
    hello.h
    hello.cpp
    main.cpp
    makefile
```

:::{important} Build systems
Au lieu de compiler et d'exécuter chaque projet manuellement, les projets devenant de plus en plus complexes nécessitent un **build system** reposant sur des **build scripts**.  

Un **build script** est un fichier qui automatise le processus de compilation et de gestion des dépendances d'un projet. Il définit les étapes nécessaires à la construction du projet, comme la compilation du code source, l'assemblage des fichiers, la génération de la documentation ou encore l'exécution des tests. Son objectif est de simplifier, standardiser et accélérer le processus de construction du projet.  

Les **build systems** standards en C++ sont **CMake** et **Makefile**. D'autres langages utilisent leurs propres outils, comme **MSBuild** pour C# ou **Gradle** et **Maven** pour Java. Certains IDE intègrent également leur propre build system. Par exemple, un projet Java sous Eclipse utilise le système de build natif d'Eclipse.  

Dans ce TP, nous allons explorer le fonctionnement d'un build system à travers un build script en **Makefile**, un build system bas niveau pour C/C++ conçu pour les systèmes Unix (Linux et macOS). **CMake** est plus haut niveau et **cross-platform** (compatible avec d'autres systèmes comme Windows), mais nous nous concentrerons sur **Makefile**, qui offre un meilleur contrôle à bas niveau. 
:::

:::{warning} Linux sous Windows
:class: dropdown
Si vous utilisez Windows à l'IUT, vous pouvez tenter d'utiliser **Git Bash** et espérer que les bonnes installations sont là. Si cela ne fonctionne pas, utilisez Linux (distribution Debian à l'IUT)...
Si vous utilisez votre propre machine, vous pouvez installer [**Windows Subsystem for Linux (WSL)**](https://learn.microsoft.com/fr-fr/windows/wsl/install) pour bénéficier d'une machine virtuelle Linux avec, par défaut, la distribution Ubuntu. Toutefois, effectuez ces installations chez vous afin de ne pas perdre de temps en TP.
:::

1. Créez le répertoire et les fichiers ci-dessus dans `TP4/` avec les codes correspondant.

`hello.h`
```{code} cpp
#ifndef HELLO_H
#define HELLO_H

void sayHello();

#endif
```

`hello.cpp`
```{code} cpp
#include <iostream>
#include "hello.h"

void sayHello() {
    std::cout << "Hello World!" << std::endl;
}
```

`main.cpp`
```{code} cpp
#include "hello.h"

int main() {
    sayHello();
    return 0;
}
```

`makefile`
```{code} makefile
all: executable

executable: main.o hello.o
	g++ -o executable main.o hello.o

main.o: main.cpp
	g++ -c main.cpp

hello.o: hello.cpp
	g++ -c hello.cpp

run:
	./executable

clean:
	rm -f executable main.o hello.o

.PHONY: all run clean
```

(tp4-make-commands)=
:::{important} Makefile
Un **Makefile** utilise une syntaxe basée sur des **cibles**. Dans le terminal, on peut exécuter une cible avec la commande :  
```{code} sh
make <cible>
```  
Voici quelques exemples courants :  
- `make` exécute la cible par défaut **`all`**, qui compile le programme.  
- `make run` exécute le programme compilé (l'exécutable).  
- `make clean` supprime les fichiers générés par la compilation pour nettoyer le projet.  

Par défaut, `make` cherche un fichier nommé `makefile` ou `Makefile` pour y trouver les cibles à exécuter. Si votre build script porte un autre nom, comme `my-build-script`, vous devrez spécifier son nom avec l'option `-f` :  
```{code} sh
make -f my-build-script <cible>
```  
(`-f` signifie **file**, pour indiquer un fichier makefile spécifique).
:::

:::{important} Syntaxe d'un makefile
La syntaxe de base d'une cible :
```{code} makefile
<cible>: <premier prérequis> <deuxième prérequis> <...>
    <première commande>
    <deuxième commande>
    <...>
```
:::

(tp4-make-behavior)=
:::{important} Comportement de `make <cible>`
`make <cible>` cherche à créer un fichier portant le nom de la **cible** en exécutant les commandes associées. Pour simplifier, nous parlerons uniquement de "fichier" plutôt que de "fichier/répertoire".  

Avant de construire une cible, `make` cherche à construire ses **prérequis**.
Si tout est à jour, c'est-à-dire que pour chaque paire de (cible, prérequis) qui intervient dans la construction de la cible, le fichier correspondant à la cible a été créé/modifié après le fichier correspondant au prérequis, alors `make` ne fait rien. Si le fichier n'existe pas, `make` considère qu'il n'est pas à jour.
Quand une cible n'est pas à jour, `make` exécute les commandes associées à la cible.

Par exemple, 
```{code} makefile
cible : prérequis
    commande cible

prérequis :
    commande prérequis
```
Dans l'exemple précédent, si les fichiers `cible` et `prérequis` existent, la dernière modification de `cible` est à 14h00 et de `prérequis` à 13h59, alors `make` vérifier que `prérequis` est à jour (ce qui est le cas vu qu'il n'a pas prérequis lui-même), puis vérifie que `cible` est à jour (ce qui est le cas vu que `cible` la dernière modification de `cible` est plus récente que celle de `prérequis`) et ne fait rien.

Ce comportement optimise la compilation, évitant de recompiler l'intégralité du projet, ce qui est crucial pour les grands projets pouvant prendre plusieurs heures à compiler.  

**Explication du Makefile de l'exemple** :  
- `all` : C'est la **cible par défaut** exécutée lorsque l'on tape `make`. Elle ne contient pas de commande et vérifie simplement si `executable` est à jour.  
- `executable` : Cette cible dépend de `main.o` et `hello.o`, qui sont des **fichiers objets** générés à partir des fichiers `.cpp` du projet. Une fois ces prérequis à jour, `make` exécute `g++ -o executable main.o hello.o`  qui relie `main.o` et `hello.o` pour créer l'exécutable `executable` (Linking, dernière étape de la compilation en C++).
- `main.o` et `hello.o` :  
  - `main.o` dépend de `main.cpp`. Même si `main.cpp` n'est pas une cible explicite, `make` vérifie simplement si le fichier existe et s'il est à jour.  
  - Pour générer `main.o`, `make` exécute `g++ -c main.cpp`. Cela exécute le **préprocesseur**, génère la **translation unit** et compile le code en **fichier objet** `.o`.  
  - Même logique pour `hello.o`.  
- `run` : Suppose que le programme est déjà compilé et exécute simplement `./executable`.
- `clean` :  
  - N'a aucun prérequis.  
  - Supprime les fichiers générés avec `rm -f executable main.o hello.o`. 
  - L'option `-f` (**force**) permet de supprimer sans confirmation et sans erreur si les fichiers n'existent pas. Cette option est couramment utilisée pour `clean` dans les Makefiles.
:::

:::{important} `.PHONY`
Vous avez peut-être remarqué que certaines cibles (`all`, `run`, `clean`) ne correspondent pas à des fichiers, et les commandes associées ne génèrent pas de fichiers ou de répertoires portant ces noms.  

Comme ces fichiers n'existent pas, `make` considère toujours que ces cibles ne sont pas à jour et les exécute systématiquement, ce qui est bien le comportement voulu pour `all`, `run` et `clean`.  

**Problème potentiel :** 
Si le projet contient des fichiers nommés `all`, `run` ou `clean`, `make` pourrait les interpréter comme des fichiers à jour et ne pas exécuter les commandes associées.  

**Solution : les cibles `.PHONY`**  
Pour éviter cette confusion, on déclare ces cibles comme **fictives** (*phony*) en ajoutant la directive `.PHONY: all run clean`.
Cela indique que `all`, `run` et `clean` ne sont pas des fichiers, mais des commandes à exécuter systématiquement, même si des fichiers portant ces noms existent.
:::

:::{important} Pourquoi séparer la compilation en plusieurs étapes ?
Nous avons l'habitude de compiler en une seule étape sans générer de fichiers intermédiaires comme les fichiers objets `.o`. Pourquoi ne faisons-nous pas la même chose ici ?
  
Dans un projet en évolution, les différentes parties du code qui composent l'exécutable (ou les exécutables) final arrivent progressivement. Il est donc essentiel de pouvoir compiler et tester certaines parties du code sans attendre que tout le projet soit terminé.
Même si tous les fichiers sont présents, modifier une seule partie du code ne signifie pas que nous devons tout recompiler. Pour les projets volumineux, où la compilation peut prendre plusieurs minutes (voire plusieurs heures), éviter une recompilation complète est un gain de temps considérable.  

Ainsi, découper la compilation en plusieurs étapes (génération de fichiers `.o` pour chaque fichier `.cpp`, puis linking final) est une bonne pratique pour les projets évolutifs et volumineux.
:::

2. Exécutez les commandes `make`, `make run` et `make clean` et observez leur effet dans le terminal et sur les fichiers.

:::{important} Prérequis cachés
Dans le makefile précédent, il existe des prérequis implicites qui sont les fichiers header `.h`. Par exemple, `main.cpp` et `hello.cpp` dépendent de `hello.h`, mais ce prérequis n'est pas spécifié dans le makefile. Cela ne pose pas de problème pour la compilation, car `g++` comprend, grâce aux directives `#include`, qu'il doit inclure les headers appropriés lors de la traduction du code source `.cpp`.

Un problème peut survenir lorsqu'un header est modifié et que l'on souhaite recompiler seulement une partie du projet au lieu de le recompiler entièrement. Étant donné que les headers ne sont pas mentionnés explicitement parmi les prérequis, si tous les autres fichiers existent déjà et que leur date de modification n'indique aucune modification, `make` ne recompilerait pas le projet. Il est donc nécessaire d'ajouter ces prérequis dans le makefile, même s'ils n'apparaissent pas dans les commandes de compilation.
:::

3. Apportez les modifications suivantes au makefile :
```{code} makefile
all: executable

executable: main.o hello.o
	g++ -o executable main.o hello.o

main.o: main.cpp hello.h
	g++ -c main.cpp

hello.o: hello.cpp hello.h
	g++ -c hello.cpp

run:
	./executable

clean:
	rm -f executable main.o hello.o

.PHONY: all run clean
```

4. Exécutez à nouveau les différentes commandes `make`. Le comportement de ces commandes ne doit pas changer.

5. Apportez les modifications suivantes au makefile :
```{code} makefile
-include *.d

all: executable

executable: main.o hello.o
	g++ -o executable main.o hello.o

main.o: main.cpp
	g++ -c main.cpp -MMD 

hello.o: hello.cpp
	g++ -c hello.cpp -MMD 

run:
	./executable

clean:
	rm -f executable main.o hello.o main.d hello.d

.PHONY: all run clean
```

6. Exécutez la commande `make` et examinez le contenu des fichiers `.d` générés.

:::{important} Fichiers `.d` (dépendances)  
Afin d'éviter d'avoir à spécifier manuellement les headers pour la compilation de chaque fichier source, nous générons automatiquement les fichiers de dépendances `.d` en utilisant l'option `-MMD` (qui inclut les prérequis avec les headers que nous avons créés, mais pas ceux des bibliothèques C++ stables) et demandons à `make` d'inclure ces prérequis avec l'option `-include *.d`.  

En raison de l'ordre d'exécution, la génération de `main.d` par exemple se produit après que les prérequis de `main.o` aient été traités. Ce n'est pas un problème, car l'objectif d'ajouter les headers aux prérequis est d'éviter de recompiler l'intégralité du projet une fois qu'il a déjà été compilé. La première compilation générera donc ces dépendances, et lors des compilations suivantes, les fichiers `.d` seront inclus, permettant à `make` de détecter les changements dans les headers.
:::

7. Apportez les modifications suivantes au makefile :
```{code} makefile
-include build/dependencies/*.d 

all: build build/binaries/executable

build:
	mkdir -p build/dependencies build/objects build/binaries 

build/binaries/executable: build/objects/main.o build/objects/hello.o
	g++ -o build/binaries/executable build/objects/main.o build/objects/hello.o

build/objects/main.o: main.cpp
	g++ -c main.cpp -o build/objects/main.o -MMD -MF build/dependencies/main.d  

build/objects/hello.o: hello.cpp
	g++ -c hello.cpp -o build/objects/hello.o -MMD -MF build/dependencies/hello.d  

run:
	./build/binaries/executable

clean:
	rm -rf build

.PHONY: all run clean
```

(tp4-build-directory)=
:::{important} `build/`
Afin d'éviter de mélanger les fichiers de compilation avec le code source, nous allons les générer dans un répertoire nommé `build` et les organiser dans des sous-répertoires : `dependencies/` pour les fichiers `.d`, `objects/` pour les fichiers `.o` et `binaries/` pour les exécutables.  

Pour ce faire, nous créons une cible `build` qui génère les répertoires nécessaires à l'aide de l'option `-p` (pour `parents`), permettant de créer tous les répertoires (y compris les parents) s'ils n'existent pas déjà. Si les répertoires existent déjà, la commande ne les écrasera pas.  

Dans les prérequis de la cible `all`, nous ajoutons `build` en premier, afin d'exécuter d'abord la cible `build`. Sinon, `make` ne pourra pas trouver les fichiers prérequis si les répertoires de `build` n'existent pas encore.  

Puisque nous ne générons plus tous les fichiers au même endroit, nous spécifions avec l'option `-o` l'emplacement et le nom du fichier `.o` à créer. Par exemple, `-o build/objects/main.o` indique que `main.o` doit être généré dans `build/objects/` sous ce nom. Il en va de même pour `-MF build/dependencies/main.d` pour les fichiers de dépendances contenant les prérequis.  

Maintenant, pour nettoyer le projet (`make clean`), il suffit de supprimer le répertoire `build/`. L'option `-rf` de la commande `rm` combine les options `recursive` et `force`, permettant ainsi de supprimer les répertoires et tous leurs sous-répertoires sans confirmation.  
:::

8. Exécutez les différentes commandes `make`, `make run` et `make clean` et observez.

9. Commentez le makefile (avec `#`) pour clarifier les points que nous avons abordés (en français si vous le souhaitez).

(tp4-project-organization)=
:::{important} Organisation d'un gros projet
Les gros projets s'organisent de la façon suivante avec plusieurs makefile (un à la racine du projet, puis un par module).
```{code} md 
minimal-project/
    source/
        first-module/
            hello.cpp
            hello.h
            makefile
        second-module/
            hi.cpp
            hi.h
            makefile
        main.cpp
    makefile
```
Nous allons découvrir la gestion de la compilation dans ce cas-ci dans la partie bonus qui suit.
:::

### Bonus

:::{important} Bonus
Finissez le reste du TP d'abord !
:::


Le code actuel du makefile contient beaucoup de redondances. Nous pouvons refactoriser ce code en utilisant des variables de la manière suivante :
```{code} makefile
BUILD_DIRECTORY = build
DEPENDENCY_DIRECTORY = $(BUILD_DIRECTORY)/dependencies
OBJECT_DIRECTORY = $(BUILD_DIRECTORY)/objects
BINARY_DIRECTORY = $(BUILD_DIRECTORY)/binaries

EXECUTABLE = $(BINARY_DIRECTORY)/executable

SOURCE_FILES = $(wildcard *.cpp)

OBJECT_FILES = $(SOURCE_FILES:.cpp=.o)
OBJECT_FILES := $(OBJECT_FILES:%=$(OBJECT_DIRECTORY)/%)

DEPENDENCY_FILES = $(SOURCE_FILES:.cpp=.d)
DEPENDENCY_FILES := $(DEPENDENCY_FILES:%=$(DEPENDENCY_DIRECTORY)/%)

-include $(DEPENDENCY_FILES)

all: $(BUILD_DIRECTORY) $(EXECUTABLE)

$(BUILD_DIRECTORY):
	mkdir -p $(DEPENDENCY_DIRECTORY) $(OBJECT_DIRECTORY) $(BINARY_DIRECTORY)

$(EXECUTABLE): $(OBJECT_FILES)
	g++ -o $@ $^

$(OBJECT_DIRECTORY)/%.o: %.cpp
	g++ -c $< -o $@ -MMD -MF $(DEPENDENCY_DIRECTORY)/$*.d

run:
	./$(EXECUTABLE)

clean:
	rm -rf $(BUILD_DIRECTORY)

.PHONY: all run clean
```

:::{important} Variables en Makefile
Les variables dans un Makefile sont nommées en suivant la convention UPPER_SNAKE_CASE. On accède à leur valeur en utilisant `$(VARIABLE)`.

Ici, nous avons défini des variables pour les différents noms de répertoires et types de fichiers. Voyons le contenu de ce code :
- `BUILD_DIRECTORY` désigne le répertoire qui contiendra nos fichiers de compilation : ici, `build`.
- Dans `build`, nous allons organiser les répertoires pour les différents types de fichiers mentionnés précédemment. Par exemple, la valeur de `OBJECT_DIRECTORY` est `build/objects`.
- `EXECUTABLE` nous permet de définir le nom de l'exécutable qui sera situé dans `build/binaries/`. Il est important de différencier le nom du fichier du chemin d'accès au fichier (*file path*) à partir du répertoire courant. `$(EXECUTABLE)` sera utilisé pour générer le fichier au bon endroit en utilisant un chemin d'accès, et ce n'est pas seulement le nom de l'exécutable.
- `SOURCE_FILES` est la liste des chemins d'accès aux fichiers sources. Ici, comme ces fichiers sont situés dans le même répertoire que le makefile, nous pouvons obtenir ces chemins avec la commande `wildcard *.cpp`, qui retourne `main.cpp hello.cpp`. Nous réorganiserons ces fichiers sources dans des sous-répertoires plus tard.
- `OBJECT_FILES` est la liste des chemins d'accès aux fichiers objets. Nous obtenons cette liste d'abord avec `OBJECT_FILES = $(SOURCE_FILES:.cpp=.o)`, qui parcourt la liste `main.cpp hello.cpp` et remplace l'extension `.cpp` par `.o` pour obtenir `main.o hello.o`. L'instruction `OBJECT_FILES := $(OBJECT_FILES:%=$(OBJECT_DIRECTORY)/%)` parcourt cette liste `main.o hello.o` et remplace le motif `%` (par exemple `main.o`) par `$(OBJECT_DIRECTORY)/%`, ce qui donne `build/objects/main.o`. Ainsi, `OBJECT_FILES` devient `build/objects/main.o build/objects/hello.o`. L'utilisation de `:=` au lieu de `=` demande à `make` de n'interpréter l'expression qu'une seule fois, contrairement à `=`, qui entraîne une boucle infinie en raison de la présence de `OBJECT_FILES` des deux côtés de la définition.
- Il en va de même pour `DEPENDENCY_FILES`.

Le reste du code ressemble à celui du précédent, avec les variables utilisées à la place des valeurs définies en début de fichier :

- `$@` renvoie la valeur de la cible courante.
- `$^` renvoie la valeur des prérequis.
- `$<` renvoie la valeur du premier prérequis.
- `$*` renvoie la valeur du motif `%` courant.

Par exemple :
```{code} makefile
$(EXECUTABLE): $(OBJECT_FILES)
	g++ -o $@ $^
```
Ici, `$@` renvoie `$(EXECUTABLE)`, qui est `build/binaries/executable`, et `$^` renvoie `$(OBJECT_FILES)`, qui est `build/objects/main.o build/objects/hello.o`. Ainsi, la commande `g++ -o $@ $^` devient `g++ -o build/binaries/executable build/objects/main.o build/objects/hello.o`.

Un autre exemple :
```{code} makefile
$(OBJECT_DIRECTORY)/%.o: %.cpp
	g++ -c $< -o $@ -MMD -MF $(DEPENDENCY_DIRECTORY)/$*.d
```
Le motif `$(OBJECT_DIRECTORY)/%.o` permet de définir les cibles sous la forme `build/objects/<filename>.o` et répète le motif `%` dans les prérequis avec `%.cpp`. Ainsi, `build/objects/main.o` a pour prérequis `main.cpp` car `%` ici correspond à `main`.  
Pour la cible `build/objects/main.o`, nous avons donc `$<` qui est `main.cpp`, `$@` qui est `build/objects/main.o`, et `$(DEPENDENCY_DIRECTORY)/$*.d` qui est `build/dependencies/main.d`, car `$*` prend la valeur de `%`, soit `main`.  
La commande `g++ -c $< -o $@ -MMD -MF $(DEPENDENCY_DIRECTORY)/$*.d` devient donc `g++ -c main.cpp -o build/objects/main.o -MMD -MF build/dependencies/main.d`, ce qui est la même ligne de code qu'auparavant.  
Il en est de même pour la cible `build/objects/hello.o`.
:::

1. Exécutez les commandes `make`, `make run` et `make clean` et observez.

:::{warning} Le makefile parfait ?
Nous pouvons encore aller plus loin dans la refactorisation en créant une variable pour le compilateur `g++`, au cas où nous souhaiterions changer de compilateur un jour, ou une variable pour les options de compilation `-c -o -MMD -MF <...>`, qui pourrait même varier selon le contexte.  
Dans le futur, nous pourrions aussi ajouter des cibles pour générer la documentation ou encore pour les tests.  
Mais de manière générale, notre makefile semble bien adapté pour un projet évolutif : il n'est pas nécessaire de le modifier fréquemment lorsque nous ajoutons du code au projet.

Malheureusement, ce n'est pas encore le cas !  
Vous avez dû remarquer que tous les fichiers de code source se trouvent au même endroit. Si nous avons un gros projet avec plusieurs modules, notre makefile ne sera plus adapté car, par exemple, il devient difficile de gérer les cibles `.o`, qui se trouvent toutes dans `build/objects/`, mais qui dépendent de fichiers `.cpp` provenant de répertoires différents.

Selon le module, il peut aussi être souhaitable de compiler le projet de manière différente ou de ne compiler qu'un seul module. Par exemple, si d'autres équipes travaillent sur les autres modules, ou si le projet est trop grand pour effectuer un `make` complet simplement pour tester un module.

La bonne pratique ici est d'avoir plusieurs makefiles, un pour chaque module, et un makefile principal qui appellera les makefiles des différents modules.
:::


2. Réorganisez votre projet minimal de la façon suivante :
```{code} md
minimal-project/
    source/
        first-module/
            hello.cpp
            hello.h
            makefile
        second-module/
            hi.cpp
            hi.h
            makefile
        main.cpp
    makefile
```

11. Dans `hi.h`, recopiez le code de `hello.h` et modifiez l'include guard et le nom de la fonction à `sayHi`.

12. Dans `hi.cpp`, recopiez le code de `hello.cpp` et modifiez l'include pour inclure `hi.h`, le nom de la fonction à `sayHi` et la sortie console à `Hi!` par exemple au lieu de `Hello World!`.

13. Dans `main.cpp`, modifiez les includes pour inclure `first-module/hello.h` et `second-module/hi.h` et ajouter `sayHi();` après `sayHello();`.

Pour cet exemple minimal, nous allons utiliser le même makefile dans les deux modules, mais nous pouvons imaginer que ces makefiles soient différents et même écrits par des personnes différentes.

14. Modifiez les makefiles des modules de la façon suivante :
```{code} makefile
BUILD_DIRECTORY = ../../build
DEPENDENCY_DIRECTORY = $(BUILD_DIRECTORY)/dependencies
OBJECT_DIRECTORY = $(BUILD_DIRECTORY)/objects

SOURCE_FILES = $(wildcard *.cpp)

OBJECT_FILES = $(SOURCE_FILES:.cpp=.o)
OBJECT_FILES := $(OBJECT_FILES:%=$(OBJECT_DIRECTORY)/%)

DEPENDENCY_FILES = $(SOURCE_FILES:.cpp=.d)
DEPENDENCY_FILES := $(DEPENDENCY_FILES:%=$(DEPENDENCY_DIRECTORY)/%)

-include $(DEPENDENCY_FILES)

all: $(BUILD_DIRECTORY) $(OBJECT_FILES)

$(BUILD_DIRECTORY):
	mkdir -p $(DEPENDENCY_DIRECTORY) $(OBJECT_DIRECTORY) $(BINARY_DIRECTORY)

$(OBJECT_DIRECTORY)/%.o: %.cpp
	g++ -c $< -o $@ -MMD -MF $(DEPENDENCY_DIRECTORY)/$*.d

clean:
	rm -rf $(OBJECT_FILES) $(DEPENDENCY_FILES)

.PHONY: all clean
```

:::{important} Makefile d'un module
- Le code reste inchangé sans générer d'exécutable.
- Le répertoire `build` sera créé dans le répertoire grand-parent (`../../build`), au même niveau que `source`.
- La cible par défaut `all` va créer les `$(OBJECT_FILES)` au lieu de l'exécutable.
- La cible `clean` supprime uniquement les fichiers objets et de dépendances liés au module.
:::

15. Modifiez le makefile à la racine du projet de la façon suivante :
```{code} makefile
BUILD_DIRECTORY = build
DEPENDENCY_DIRECTORY = $(BUILD_DIRECTORY)/dependencies
OBJECT_DIRECTORY = $(BUILD_DIRECTORY)/objects
BINARY_DIRECTORY = $(BUILD_DIRECTORY)/binaries

SOURCE_DIRECTORY = source

EXECUTABLE = $(BINARY_DIRECTORY)/executable

MODULES = $(wildcard $(SOURCE_DIRECTORY)/*/)
SOURCE_FILES = $(wildcard $(SOURCE_DIRECTORY)/*.cpp)

OBJECT_FILES = $(SOURCE_FILES:$(SOURCE_DIRECTORY)/%.cpp=%.o)
OBJECT_FILES := $(OBJECT_FILES:%=$(OBJECT_DIRECTORY)/%)

DEPENDENCY_FILES = $(SOURCE_FILES:$(SOURCE_DIRECTORY)/%.cpp=%.d)
DEPENDENCY_FILES := $(DEPENDENCY_FILES:%=$(DEPENDENCY_DIRECTORY)/%)

all: $(BUILD_DIRECTORY) $(EXECUTABLE)

$(BUILD_DIRECTORY):
	mkdir -p $(DEPENDENCY_DIRECTORY) $(OBJECT_DIRECTORY) $(BINARY_DIRECTORY)

$(EXECUTABLE): $(MODULES) $(OBJECT_FILES) 
	g++ -o $@ $(wildcard $(OBJECT_DIRECTORY)/*.o)

$(MODULES):
	$(MAKE) -C $@

$(OBJECT_DIRECTORY)/%.o: $(SOURCE_DIRECTORY)/%.cpp
	g++ -c $< -o $@ -MMD -MF $(DEPENDENCY_DIRECTORY)/$*.d

run:
	./$(EXECUTABLE)

clean:
	rm -rf $(BUILD_DIRECTORY)

.PHONY: all run clean $(MODULES)
```

:::{important} Makefile à la racine
- Nous récupérons le répertoire des codes source (`source/`).
- À l'intérieur de `source/`, nous récupérons les noms des modules qui correspondent aux sous-répertoires.
- Nous récupérons aussi les fichiers de code `.cpp`. Dans cet exemple, nous avons seulement `main.cpp`, mais il est possible d'avoir d'autres fichiers.
- De manière similaire à avant, nous récupérons les noms des fichiers objets et des fichiers de dépendances à générer.
- Pour créer l'exécutable final, il faut d'abord compiler les modules et les fichiers objets des nouveaux codes sources, puis relier tous les fichiers objets dans `build/objects/`.
- Pour chaque module (`source/first-module source/second-module`), nous nous plaçons à l'intérieur du sous-répertoire correspondant (par exemple `source/first-module`) avec l'option `-C source/first-module`. Ensuite, nous exécutons `make` via la variable native `$(MAKE)`, qui reprend les options de la commande `make` dans notre terminal. Par exemple, il est possible de paralléliser la compilation lorsque notre machine dispose de plusieurs processeurs, car des fichiers sources peuvent être compilés en même temps. Ainsi, `make -j4` utilise 4 processeurs simultanément, et `$(MAKE)` reprend l'option `-j4` pour compiler les modules. Pour notre exemple, nous aurions pu remplacer `$(MAKE)` par `make` directement, mais il est préférable d'utiliser la variable native associée à la commande `make`.  
:::

16. Commentez les makefiles grâce à `#` (en français si vous le souhaitez) pour clarifier les différents blocs de code et ajoutez des exemples pour les commandes pour clarifier la syntaxe.

Par exemple :
```{code} makefile
# Compile source codes to object files
$(TARGET_VARIABLE): $(PREREQUISITE_VARIABLE)
    # For example: g++ -c prerequisite.cpp -o target.o
    g++ -c $< -o $@ 
```

## Exercice 2 : Documentations

1. Réécrivez votre README (en français si vous le souhaitez) et vérifiez le rendu du README sur GitLab.

:::{note} Rappels
- Les fonctionnalités principales de ce projet sont d'apprendre les bonnes pratiques de code et de développement.
- Il n'y a pas de documentation complémentaire.
- Vous pouvez expliquer comment cloner le dépôt en créant un PAT (il n'est pas nécessaire d'être aussi détaillé que dans le premier TP) et comment compiler le code en ligne de commande avec `g++` pour les instructions d'installation.
- Vous pouvez expliquer comment exécuter un exécutable en ligne de commande pour les instructions d'utilisation.
- Vous pouvez ajouter une licence (fichier `LICENSE` en anglais) MIT dans un fichier séparé et un lien vers la licence.  
:::

:::{hint} Quelques syntaxes Markdown
:class: dropdown
- `#` fait des titres. Plus il y a de `#`, plus le titre est petit.
- `[texte](lien)` crée des liens hypertextes.
- ` ``` code ``` ` crée des blocs de code.
- `**texte en gras**` met le texte en gras.
- `*texte en italique*` met le texte en italique.
- `-` crée des listes à puces.
- `1.`, `2.`, etc. crée des listes numérotées.
- `> citation` suivi de `> - auteur`  crée une citation avec un auteur.
- `![texte alternatif](lien d'image)` insère une image.
- `- [ ]` crée une case à cocher, et `- [x]` une case cochée.
- `---` insère une ligne horizontale.
::: 

2. Créez un répertoire `documentations/` dans `TP4/`.

En cours, nous avons vu un exemple de documentation utilisant Doxygen (très similaire à la syntaxe Javadoc). Étant donné que Doxygen n'est pas (encore) installé sur les machines de l'IUT, nous allons revoir le même exemple en Javadoc.

3. Recopiez le code suivant dans un fichier `Temperature.java`.

```{code} java
/**
 * Represents a temperature in Celsius and Fahrenheit.
 *
 * This class allows you to set a temperature in Celsius, convert it to Fahrenheit,
 * and vice versa. It also provides the ability to get the current temperature in either unit.
 *
 * <p> Example usage: </p> 
 * <pre>
 * <code>
 * Temperature currentTemperature = new Temperature(25.0);  // 25°C
 * currentTemperature.displayTemperature();  // Output: Temperature: 25°C / 77°F
 * currentTemperature.setFahrenheit(100.0);
 * currentTemperature.displayTemperature();  // Output: Temperature: 37.7778°C / 100°F
 * </code>
 * </pre>
 */
public class Temperature {
    private double celsius;

    /**
     * Constructs a new Temperature object.
     *
     * Initializes the temperature in Celsius.
     *
     * @param celsius The initial temperature in Celsius.
     */
    public Temperature(double celsius) {
        this.celsius = celsius;
    }

    /**
     * Gets the current temperature in Celsius.
     *
     * @return The temperature in Celsius.
     */
    public double getCelsius() {
        return celsius;
    }

    /**
     * Sets the temperature in Celsius.
     *
     * This method sets the temperature value directly in Celsius.
     *
     * @param celsius The new temperature in Celsius.
     */
    public void setCelsius(double celsius) {
        this.celsius = celsius;
    }

    /**
     * Gets the current temperature in Fahrenheit.
     *
     * @return The temperature in Fahrenheit.
     */
    public double getFahrenheit() {
        return celsius * 9 / 5 + 32; // Formula to convert Farenheit to Celsius
    }

    /**
     * Sets the temperature using a value in Fahrenheit.
     *
     * This method converts the given Fahrenheit value to Celsius and sets it.
     *
     * @param fahrenheit The temperature in Fahrenheit.
     */
    public void setFahrenheit(double fahrenheit) {
        this.celsius = (fahrenheit - 32) * 5 / 9; // Formula to convert Celsius to Farenheit
    }

    /**
     * Converts and displays the temperature in both Celsius and Fahrenheit.
     *
     * This method prints the current temperature in both Celsius and Fahrenheit.
     */
    public void displayTemperature() {
        System.out.println("Temperature: " + celsius + "°C / " + getFahrenheit() + "°F");
    }
}
```

4. Générer la documentation pour `Temperature.java` avec la commande `javadoc -d docs Temperature.java`, ce qui créera un répertoire `docs/`.

5. Consultez la documentation générée en ouvrant `docs/index.html` dans un navigateur web.

6. Reprenez la syntaxe Doxygen vue en cours pour rédiger la documentation de la classe `Product` de l'exercice `short-functions` du `TP3`.

:::{warning} `@example` dans Doxygen
:class: dropdown
Le tag `@example` dans Doxygen doit être utilisé dans un bloc séparé. Sinon, Doxygen va reconnaître tout le bloc comme un exemple et ne va pas montrer les descriptions dans la documentation générée, contrairement à Javadoc qui n'a pas de tag `@example`.
:::

Revenez aux [objectifs](#tp4-objectifs) et cochez les points que vous avez maîtrisés. Revenez sur les points que vous n'avez pas encore bien compris. Appelez votre encadrant si besoin.