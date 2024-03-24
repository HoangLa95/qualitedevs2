# TP7 : Résolution de conflits Git

:::{important} QCM
:class: dropdown
Ne pas oublier de répondre au QCM sur Moodle.
:::

:::{important} Disclaimer
:class: dropdown
La résolution de conflits que nous allons voir est une résolution simple quand vous avez qu'une seule branche de travail.

Pour des fusions plus complexes, consulter [la documentation](https://git-scm.com/docs/git-merge) de `git merge`.
:::

## Résolution de conflits

Parfois, on oublie de faire un pull avant de commencer à travailler et on modifie un fichier qui n'est plus à jour. Lors du push des nouvelles modifications, Git nous informe que les fichiers sont différents et c'est à nous de décider comment fusionner les deux versions. Cela pourrait arriver aussi quand plusieurs personnes travaillent sur le même fichier et les modifications faites rentrent en conflit. Aujourd'hui, nous allons apprendre à gérer ces conflits avec et sans IDE.

Pour commencer, créer un dossier `TP7` et un fichier `conflict.md` avec le contenu suivant.

```{code} md
# Conflict resolution in Git

Let's learn some conflict resolution in Git.

## Merge without conflicts

Write something here on GitLab Web IDE:

Write something else here locally without pulling from GitLab first: 

## Merge with conflicts

Write something here on GitLab Web IDE and something else locally without pulling from GitLab first:
```

Synchroniser votre dépôt local avec le dépôt distant.

### Merge Simple

Nous allons commencer par un exemple où le même fichier a reçu des modifications différentes mais ces modifications peuvent être fusionnées sans conflits.

Ouvrir le Web IDE de GitLab et ajouter du texte. Par exemple,
```{code} md
Write something here on GitLab Web IDE: Hello
```

Commit vos modifications pour avoir cette nouvelle version de `conflict.md` sur GitLab.

Sans récupérer ces nouvelles modifications sur votre dépôt local, ajouter du texte dans `conflict.md` sur votre poste de travail. Par exemple,
```{code} md
Write something else here locally without pulling from GitLab first: Hi
```

#### Avec VSCodium

Synchroniser vos changements locaux avec le dépôt distant.

Vous allez recevoir un message d'erreur avec comme option `Show Command Output`. Cliquer dessus.

Il vous indique qu'il y a des branches divergentes dans votre dépôt, ce qui signifie qu'il y a plusieurs versions différentes d'un même fichier et que nous allons devoir les fusionner.

Appuyer sur `F1` pour ouvrir la barre de commande dans VSCodium. Dans la barre de commande, taper `git merge` puis cliquer sur l'option `Git: Merge` qui vous est proposé.

Maintenant, il vous propose plusieurs branches et il faut choisir avec quelle branche on va fusionner. Choisir `origin/HEAD` (qui représente la branche courante sur le dépôt distant).

La fusion a fonctionné, maintenant vous devez voir sur votre poste de travail les deux modifications.
```{code} md
Write something here on GitLab Web IDE: Hello

Write something else here locally without pulling from GitLab first: Hi
```

Il est prêt pour synchroniser les deux dépôts et les deux contiennent la même version du fichier.

#### Sans IDE

Si vous avez fait la manipulation avec VSCodium et vous voulez voir comment le faire dans un terminal, vous pouvez refaire les mêmes manipulations.

Quand vous faites votre push, vous allez recevoir une erreur qui vous indique qu'il y a des changements sur le dépôt distant qu'il faut récupérer sur le dépôt local avec un pull.

Maintenant, si vous faites un pull, vous allez recevoir la même erreur qu'avant sur les branches divergentes.

Taper la ligne suivante.
```{code} sh
git merge origin/HEAD
```

Il vous proposer d'écrire un message de commit pour l'opération avec un message auto-généré déjà écrit. Vous pouvez modifier le message si vous voulez. Appuyer sur `Ctrl+X` pour finir.

Vous pouvez vérifier que `conflict.md` contient bien les deux modifications. Finir avec `git push`.

### Merge avec conflits

Dans l'exemple précécent, nous avons vu une fusion où les modifications sur les deux versions différentes du même fichier ne rentrent pas en conflit et la fusion pouvait se faire automatiquement.

Cette fois, on va refaire la même manipulation sauf que sur le Web IDE de GitLab, on va écrire par exemple,
```{code} md
Write something here on GitLab Web IDE and something else locally without pulling from GitLab first: Hello
```
alors que localement on va écrire
```{code} md
Write something here on GitLab Web IDE and something else locally without pulling from GitLab first: Hi
```

#### Avec VSCodium

En refaisant `git merge` (à travers la barre de commande avec `F1`) avec la branche `origin/HEAD`, l'IDE nous montre le fichier `conflict.md` avec les deux versions différentes.
```{figure} ../images/merge-conflict.png
:alt: Merge conflict
:align: center
```

`HEAD` correspond à la version courante sur le dépôt local et `origin/HEAD` correspond à la version courante sur le dépôt distant.
- `Accept Current Change` va garder la version de `HEAD`.
- `Accept Incoming Change` va garder la version de `origin/HEAD`.
- `Accept Both Changes` va concaténer les deux versions.
- `Compare Changes` va ouvrir les deux versions dans deux tabs différents.

La meilleure solution est d'appuyer sur le bouton `Resolve in Merge Editor` qui apparaît en bas à droite.

Ce bouton va ouvrir trois fenêtres `Current`, `Incoming` et `Result` qui correspondent respectivement à la version de `HEAD`, la version de `origin/HEAD` et la version finale que l'on veut.

Dans ce cas, peut-être que l'on a envie de faire une version fusionnée complètement différente des deux versions proposées.

Écrire `Haha` au lieu de `Hello` et `Hi` dans `Result` puis appuyer sur `Complete Merge`.

Maintenant, vous pouvons synchroniser les deux dépôts avec cette version finale de `conflict.md`.

#### Sans IDE

Refaire les modifications du fichier `conflict.md` pour créer le conflit si vous l'avez déjà résolu avec l'IDE.

Vous allez rencontrer les mêmes erreurs qu'avant et quand vous taper `git merge origin/HEAD`, vous allez voir l'erreur suivante.
```{code} sh
Automatic merge failed; fix conflicts and then commit the result.
```

Ouvrir `conflict.md` dans un éditeur de texte de base. Vous allez voir les lignes suivantes.
```{code} md
<<<<<<< HEAD
Write something here on GitLab Web IDE and something else locally without pulling from GitLab first: Hi
=======
Write something here on GitLab Web IDE and something else locally without pulling from GitLab first: Hello
>>>>>>> origin/HEAD
```

Entre `<<<<<<< HEAD` et `=======` se trouve la version courante sur le dépôt local et la version du dépôt distant entre `=======` et `>>>>>>> origin/HEAD`.

Remplacer ce bloc de texte par la version finale que vous voulez :
```{code} md
Write something here on GitLab Web IDE and something else locally without pulling from GitLab first: Haha
```

Add et commit la version finale puis vous pouvez finir avec `git push`. 




