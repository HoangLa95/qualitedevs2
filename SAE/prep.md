# Préparation au projet : Développement d'une application

Cette séance est dédiée à la préparation de votre projet SAÉ 1256, qui consiste en la création d'une application. Le projet intègre trois ressources principales : Qualité de développement (R203), IHM (R202) et DOO (R201).

:::{important} Quiz
Un Quiz sur votre préparation sera ouverte la semaine prochaine **jusqu'au vendredi 21/02 à 23h59**.
:::


## Prise en main du sujet

:::{important} Sujet
Vous devez prendre connaissance du sujet du projet qui se trouve sur [Moodle](https://cours.iut-orsay.fr/course/view.php?id=2817).
:::

S'il y a des éléments techniques du sujet que vous n'avez pas encore vu, pas de panique, c'est normal puisque nous débutons le semestre. 
Vous aurez l'occasion de le découvrir prochainement.
Si vous avez des questions spécifiques sur le sujet, réservez-les pour le premier amphi avec M. La, M. Ravenet et M. Martin.

## Constitution des groupes de travail

:::{important} Groupe de travail
Formez vos groupes de travail sur Moodle en respectant les instructions indiquées.
:::

## Git

### Création du dépôt

Une fois que votre groupe est formé, le dépôt Git pour le projet doit être créé par un seul membre du groupe. 
Deadline pour la création du dépôt : **vendredi 21/02 à 23h59**.

:::{important} Nom du dépôt
Le dépôt Git doit être nommé `s2-sae-dev-app-<numéro du groupe>` (par exemple : `s2-sae-dev-app-ab1`).
:::

:::{important} Invitez les membres et les professeurs.
Il faut inviter les autres membres du groupe ainsi que M. La (La Hoang *hla*), M. Ravenet (Ravenet Brian *bravene*) et M. Martin (Martin Jean-Claude *jmarti15*) en tant que **Maintainer**.
Il n'est pas nécessaire d'ajouter les encadrants des séances à votre dépôt.
::: 

:::{note} README
Le fichier README de votre dépôt Git doit contenir les noms des membres du groupe et toute information pertinente concernant le projet.
Ce README peut être utilisé comme un document simple de gestion/répartition du travail de votre groupe. Il est à maintenir durant le projet.
:::

### Organisation du dépôt
     
Il est essentiel d’organiser correctement votre dépôt Git pour éviter le mélange des fichiers de rendus.
Créez des sous-dossiers spécifiques pour chaque type de rendu (exemple : un dossier pour les maquettes, un autre pour UML et modèle Java, etc.).

:::{important} `.gitignore`
Maintenez un fichier `.gitignore` adapté à votre environnement de développement.
Par exemple, si vous utilisez Eclipse pour votre projet Java, ajoutez les règles de `.gitignore` appropriées ([un exemple](https://github.com/github/gitignore/blob/main/Global/Eclipse.gitignore)).

Veillez à maintenir votre fichier `.gitignore` en fonction des logiciels que vous utilisez (une simple recherche sur Google vous permettra de trouver le code approprié à ajouter à `.gitignore`).
:::
     
### Travail collaboratif

Apprenez à travailler en équipe avec Git. 
Il est important d'éviter les conflits de fusion, en particulier lorsque plusieurs membres du groupe travaillent sur le même fichier. 
   
:::{important} Découpage du code
Organisez-vous pour découper votre code en différentes classes et fichiers.
Chaque membre peut travailler sur une partie distincte du code sans risquer de conflits.
Utilisez un découpage de code modulaire, que vous pourrez fusionner ultérieurement si nécessaire.

Par exemple, ici nous avons un fichier `Car.java`
```{code} java
public class Car {
    private String make;
    private String model;

    public Car(String make, String model) {
        this.make = make;
        this.model = model;
    }

    public void displayDetails() {
        System.out.println("Car Make: " + make);
        System.out.println("Car Model: " + model);
    }
}
```
qui est utilisé par le fichier `Main.java` dans le même répertoire.
```{code} java
public class Main {
    public static void main(String[] args) {
        Car myCar = new Car("Toyota", "Corolla");
        myCar.displayDetails();
    }
}
```
Si les deux fichiers ne sont pas dans le même répertoire et sont structurés de la façon suivante,
```{code} bash
/vehicles
    Car.java
Main.java
```
alors il suffit d'ajouter `import vehicles.Car;` au début de `Main.java`.
:::

:::{important} Ne push pas de bugs !
Vous n’avez pas encore vu les branches Git, donc voici une règle simple pour le travail en équipe : si votre code ne compile pas, ne le push pas.

Lorsque vous commencerez à écrire des tests : si un code ne passe pas un test, ne le push pas non plus.

Si vous avez besoin d’aide pour déboguer, commentez la partie du code problématique en expliquant le bug, puis poussez ces commentaires.

Même lorsque vous maîtriserez les branches Git, il restera essentiel de ne pas push de bugs sur la branche principale. Utilisez une branche de développement et décrivez clairement le problème en commentaire.

Dans les prochains semestres, vous découvrirez l'intégration continue, un système qui testera automatiquement chaque modification à chaque commit et qui vous empêchera même de push du code contenant des erreurs !
:::

:::{important} Git avec ou sans IDE  
Vous utiliserez différents outils et IDE, certains avec intégration Git, d'autres sans.
Vous devez savoir utiliser Git via le terminal si votre outil ne l'intègre pas. 
Lors des cours de Qualité de développement, vous apprendrez à utiliser Git dans VSCodium. 
L'intégration Git dans d'autres logiciels seront assez similaires à VSCodium. Il sera à vous de les découvrir vous-même.
:::

### Résolution de conflits

Vous serez certainement amené à rencontrer des conflits. 
Voici un tutoriel simple pour vous aider à résoudre ces conflits dans le terminal, que vous travailliez seul ou en équipe. 
Des explications plus détaillées sur la résolution de conflits seront fournies dans le cours de Qualité de développement.  

:::{warning} Je n'ai pas encore de groupe...
:class: dropdown
Si le dépôt de votre équipe n'est pas encore créé, vous pouvez créer un dépôt personnel pour tester ou utiliser votre dépôt existant en Qualité (n'oubliez pas de supprimer les fichiers de test à la fin du tutoriel si vous utilisez votre dépôt de Qualité).
:::

:::{note} Qu'est-ce qu'un conflit ?
:note: dropdown
Il arrive parfois d'oublier de synchroniser les dépôts local et distant avant de commencer à travailler, ce qui entraîne par exemple des modifications sur un fichier obsolète. 
Lors du push de vos modifications, Git détecte les différences et vous demande de choisir comment fusionner les deux versions.
Des conflits peuvent également survenir lorsque plusieurs personnes modifient le même fichier simultanément.
:::

1. Pour commencer, créez un fichier `conflict.md` contenant le texte suivant.

```{code} md
# Git Conflict Resolution

Let's explore how to resolve conflicts in Git.

## Merging Without Conflicts

Another member should add some content here:

Now, add some content locally without pulling the latest changes from GitLab:

## Merging With Conflicts

Another member should add some content here, then you should add different content locally without pulling the latest changes from GitLab:
```

2. Synchronisez votre dépôt local avec le dépôt distant.

### Merge simple

Nous allons commencer par un exemple où le même fichier a été modifié de manière différente, mais les modifications concernent des sections de code distinctes et peuvent être fusionnées sans conflit.

3. Si vous êtes en équipe, un autre membre doit synchroniser son dépôt local pour modifier `conflict.md`. Si vous êtes seul, ouvrez le Web IDE de GitLab. Ajoutez le texte suivant :
```{code} md
Another member should add some content here: Hello
```

4. Commit et push vos modifications pour avoir cette nouvelle version de `conflict.md` sur GitLab.

5. Sans avoir récupéré ces nouvelles modifications sur votre dépôt local, ajoutez le texte suivant dans le fichier `conflict.md` de votre répertoire de travail :
```{code} md
Now, add some content locally without pulling the latest changes from GitLab: Hi
```

6. Lorsque vous effectuerez un push, une erreur s'affichera pour vous indiquer qu'il y a des changements sur le dépôt distant qu'il vous faut récupérer dans votre dépôt local avec un pull. Si vous effectuez un pull, vous recevrez à nouveau la même erreur concernant les branches divergentes.

7. Exécutez la commande suivante.
```{code} sh
git merge origin/main
```

8. Git fera un add automatiquement et vous proposera d'écrire un message de commit pour l'opération, avec un message auto-généré déjà pré-rempli. Vous pouvez modifier ce message si vous le souhaitez.

Vous pouvez vérifier que `conflict.md` contient bien les deux modifications. 

9. Finissez la résolution avec `git push`.

### Merge avec conflits

Dans l'exemple précédent, nous avons vu une fusion où les modifications apportées aux deux versions du même fichier ne se chevauchaient pas, ce qui a permis à Git de fusionner automatiquement les changements.

Cette fois, nous allons refaire la même opération en modifiant `conflict.md` de la façon suivante :

10. Un autre membre de l'équipe (ou vous-même à partir du Web IDE de GitLab) doit modifier le fichier et synchroniser avec GitLab de la façon suivante :
```{code} md
Another member should add some content here, then you should add different content locally without pulling the latest changes from GitLab: Hello
```

11. Localement, vous allez écrire :
```{code} md
Another member should add some content here, then you should add different content locally without pulling the latest changes from GitLab: Hi
```

12. Lorsque vous effectuerez un push puis un pull, vous allez rencontrer les mêmes erreurs qu'avant. Cette fois, `git merge` renvoie aussi une erreur :
```{code} sh
Auto-merging conflict.md
CONFLICT (content): Merge conflict in conflict.md
Automatic merge failed; fix conflicts and then commit the result.
```

13. Ouvrez `conflict.md` dans un éditeur de texte de base. Vous allez voir les lignes suivantes.
```{code} md
<<<<<<< HEAD
Another member should add some content here, then you should add different content locally without pulling the latest changes from GitLab: Hi
=======
Another member should add some content here, then you should add different content locally without pulling the latest changes from GitLab: Hello
>>>>>>> origin/main
```

Entre `<<<<<<< HEAD` et `=======` se trouve la version courante sur le dépôt local et la version du dépôt distant entre `=======` et `>>>>>>> origin/main`.

14. Remplacez ce bloc de texte par la version finale de votre choix (qui peut différer des deux versions proposées). Par exemple :
```{code} md
Another member should add some content here, then you should add different content locally without pulling the latest changes from GitLab: Haha
```

15. Add, commit et push la version finale.

## Gestion de projet

Dès le début, organisez-vous pour bien répartir les tâches. 

:::{important} Réunion d'équipe
Lors de chaque réunion de groupe, effectuez un tour de table rapide où chaque membre doit répondre à trois questions :
- Qu'ai-je déjà fait ?
- Qu'est-ce que je suis en train de faire ?
- Qu'est-ce que je vais faire ?
:::

Bien que la gestion de votre projet ne fasse pas l'objet d’une évaluation spécifique, les encadrants vérifieront régulièrement votre avancement et l’équilibre du travail au sein du groupe. 
L’implication de chaque membre est cruciale pour le succès du projet.

:::{important} Rendus personnalisés  
Les rendus sont personnalisés et chaque étudiant devra indiquer sa contribution. 
Le manque d’implication dans ce projet pourra avoir un impact considérable sur votre note.
:::

## Conclusion

Une fois la préparation terminée, vous passerez immédiatement à la phase de rendu des maquettes et des tables fonctionnelles de l'IHM.
Commencez à réfléchir à vos idées pour le projet et à remplir votre fichier README.
