# Environnement Linux sous Windows

Si vous êtes sur votre ordinateur personnel qui a seulement accès à un environnement Windows, vous pouvez utiliser le WSL (Windows Subsystem for Linux) avec Ubuntu.
Si vous êtes à l'IUT, utilisez simplement Debian.

## Installation

Ouvrez un terminal Windows (Command Prompt) et exécutez la commande `wsl --install`. Plus de détails [ici](https://docs.microsoft.com/en-us/windows/wsl/install).

## Configuration

1. Exécutez `wsl` pour ouvrir une session WSL.

:::{note} Package manager
Si vous avez installé la distribution Linux par défaut, vous devez avoir Ubuntu qui utilise le package manager `apt` (Advanced Package Tool) par défaut. D'autres distributions Linux utilisent des package managers différents (Debian utilise aussi `apt`, Fedora `dnf`, Arch `pacman`, etc.). La gestion centralisée et sécurisée des packages est un avantage des distributions Linux par rapport à Windows.
:::

:::{note} Environnement WSL
La dernière version de WSL (WSL2 qui est maintenant la version installée par défaut) crée une machine virtuelle qui simule un ordinateur avec un kernel et une distribution Linux.
:::

2. Vérifiez que vos packages sont à jour avec `sudo apt update` puis `sudo apt upgrade`.

:::{note} `sudo`
`sudo` est une abbréviation de **Super User DO** qui vous donner les droits d'administration aussi appelé **root privileges** et permet d'installer des packages.
:::

3. Vérifiez que vous avez les packages nécessaires pour les TPs avec les commandes suivantes.
```{code} sh
git --version
g++ --version
make --version
java --version
```

4. Si un package est manquant, vous pouvez l'installer avec `sudo apt install <package>`. Par exemple, `sudo apt install git` pour installer Git.

:::{note} Java 21
Vous pouvez installé la version stable la plus récente de Java avec `sudo apt install openjdk-21-jdk-headless`.
:::

## Utilisation

Vous pouvez maintenant lancer une session WSL depuis n'importe quel terminal Windows et travailler comme dans un environnement Linux.