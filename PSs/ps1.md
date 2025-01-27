# PS1 : Discover Git and GitLab

Before starting to code, it is important to familiarize yourself with the tools that help organize your programming work.

:::{seealso} Git
[Git](https://en.wikipedia.org/wiki/Git) is a widely used [version control system](https://en.wikipedia.org/wiki/Version_control). It is already installed on the department's machines. If you are working on your personal machine, you can [install it for free](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
:::

:::{seealso} GitLab
[GitLab](https://en.wikipedia.org/wiki/GitLab) is a platform that uses Git to allow you to store your projects, synchronize them between your different machines (IUT computers, personal computer), and share them with your collaborators.
:::

## Objectives

The goal of this session is to understand the following points:
- [ ] `git clone <remote repository address with PAT>` ([Personal Access Token](#personal-access-token) and [Configuring your workstation](#configuring-your-workstation))
- [ ] `git status` ([Local repository](#local-repository))
- [ ] `git add <filename>` ([Local repository](#local-repository))
- [ ] `git commit -m "<commit message>"` ([Local repository](#local-repository))
- [ ] `git push` ([Local repository](#local-repository))
- [ ] `git remote update` ([Remote repository](#remote-repository) and [Synchronizing the local repository with the remote repository](#synchronizing-the-local-repository-with-the-remote-repository))
- [ ] `git pull` ([Remote repository](#remote-repository) and [Synchronizing the local repository with the remote repository](#synchronizing-the-local-repository-with-the-remote-repository))
- [ ] `.gitignore` ([Ignoring files](#ignoring-files))

These points are essential for the rest of the course, so **you must master them!**

## Activating your GitLab account

As a student at the IUT of Orsay, you already have an account on the [IUT GitLab](https://git.iut-orsay.fr/).

```{image} ../images/git-iut-orsay.jpg
:alt: git.iut-orsay.fr
:align: center
```

To log in, use your department username and password.

## Personal Access Token

To establish a secure connection between your machine and the GitLab server of the IUT, we will use a **Personal Access Token** (PAT).

To create a PAT:

1. Go to your profile by clicking on your avatar (top left) and then **Edit profile**.

```{image} ../images/edit-profile.png
:alt: Edit profile
:align: center
```

2. In the left menu, click on **Access Tokens**.

```{image} ../images/access-token-menu.png
:alt: Access Tokens
:align: center
```

3. Add a new token by clicking on **Add new token**.

```{image} ../images/add-new-token.png
:alt: Add new token
:align: center
```

4. Give the token a name that indicates the workstation being used (for example, `IUTOrsay` if you're working on a machine at the IUT).
5. Set an expiration date of one year (the maximum allowed) from today's date.
6. Select all the [scopes](https://docs.gitlab.com/ee/user/profile/personal_access_tokens.html#personal-access-token-scopes).
7. Click on **Create personal access token**.

:::{important} SAVE YOUR PAT !!! 
Once you leave the creation page, the PAT will no longer be visible. It is essential to save it, as you will need it to access your projects!
:::

:::{note} One PAT per workstation
You need to create a distinct PAT for each workstation you use. It is possible to create as many PATs as necessary.
:::

## Creating a remote repository

1. Create your first repository by clicking on the `+` button located at the top left of the screen.

```{image} ../images/create-new-project.png
:alt: Create new project
:align: center
```

2. Create an empty project with the name `qualite-dev-s2-<first name>-<last name>` (for example `qualite-dev-s2-hoang-la`).

:::{warning} Warning!
The project you are going to create will contain all the practical sessions assignments for this course. You should not create a new project for each session.
:::

:::{important} `monprojet`
In the following, we will refer to this project as `monprojet`. Don't forget to replace it with the name you have chosen.
:::

3. Select the "Private" visibility option for your project and check the option to initialize the project with a README.

:::{important} README.md
Having a README in [Markdown](https://en.wikipedia.org/wiki/README) in a project is a standard practice. This file serves as the project's description.  

Markdown is also what I use for this website!
:::

4. Browse through the default README proposed by GitLab.

:::{important} Linux
The following instructions are intended for Linux. You are **strongly advised** to use Debian (an open-source Linux distribution) at the IUT.

If you are not comfortable with terminal commands on Linux, remember that you can always use a file manager and your mouse.
:::

:::{seealso} macOS
:class: dropdown
The Unix commands on Linux and macOS are very similar, but you may occasionally need to adapt some of them.
:::

:::{seealso} Windows
:class: dropdown
You can install the emulator [Git for Windows](https://gitforwindows.org/).
:::

## Configuring your workstation

Your project has been created on the IUT server. Now, to work on this project from your local workstation, you need to configure it first.

```{code} sh
git config --global user.name "<Prénom> <Nom>"
git config --global user.email "<prenom>.<nom>@universite-paris-saclay.fr"
```

You can now clone your remote repository to download it to your local workstation:
1. Click on the **Code** button on the right.

```{image} ../images/code-button.png
:alt: Code
:align: center
```

2. Copy the URL under the **Clone with HTTPS** option.

```{image} ../images/clone-https.png
:alt: Clone with HTTPS
:align: center
```

3. Open a terminal in your working directory.
4. Enter the following command, pasting the copied address and adding your login and PAT in the appropriate places.

```{code} sh
git clone https://<login>:<Personal Access Token>@git.iut-orsay.fr/<login>/monprojet.git
```

For example:
```{code} sh
git clone https://hla:1234thisIsYourPAT5678@git.iut-orsay.fr/hla/monprojet.git
```

:::{important} Personal Access Token
You must add the token that you saved earlier!
- **Not the name you gave it!**
- **Not your password!**
- **Not the Feed Token!** (which you can also find on the Access Token page)
If you haven't saved your PAT, revoke the one you've already created and repeat the process to generate a new one.
:::

## Local repository

A directory with the same name (local repository) has now been downloaded to your machine.

1. Run the following commands.

```{code} sh
cd monprojet/
ls
git status
```

The `ls` command allows you to list all the files in the directory, while `git status` shows the status of the repository. For now, the latter command should indicate that there is nothing to commit and your working directory is clean

2. What do you observe when running the following commands?

```{code} sh
touch my-first-file.txt
git status
```

:::{hint} Untracked files and changes
:class: dropdown
Git indicates that your working directory contains an **untracked** file or change.

Git is a version control tool: it allows you to keep track of changes made to the files in your project. However, not all files or changes are necessarily important. By default, the files you create or the changes you make are not tracked. Only the files and changes that are explicitly tracked will have their history recorded.
:::

3. Add a line to `my-first-file.txt` (for example, `Hello World!`).

4. Ask Git to track the change to `my-first-file.txt` by running the following command.

```{code} sh
git add my-first-file.txt
```

5. Run the `git status` command again. What does it tell you?

:::{important} Staged changes  
Once a file or change is being tracked, it changes status and becomes a **staged change**.
:::

:::{seealso} `git restore --staged`
:class: dropdown
If you accidentally added a change or file, you can unstage the staged change using the command `git restore --staged <nom du fichier>`.

For example, run `git restore --staged my-first-file.txt`, then check the status of the repository with `git status`. Don't forget to track the change again by using `git add`.
:::

6. Now, to create a history of the staged changes (in this case, the file `my-first-file.txt` with the line `Hello World!`), you need to commit these changes and add a descriptive message by running the command `git commit -m "<descriptive commit message>"`.

For example:
```{code} sh
git commit -m "Initial commit of my-first-file.txt saying Hello World!"
```

7. Re-run `git status` to check the status of your repository. What does it indicate?

:::{hint} Ready to push
:class: dropdown
The commit has been made. Your local version of the project is now ahead of the version on the GitLab server. You can now **push** these changes to synchronize the two versions.
:::

8. Run `git push`.

9. Go to GitLab and check that the remote repository matches the local repository.

## Remote repository

:::{important} GitLab's Web IDE
GitLab's Web IDE allows you to work directly on the remote repository.

The changes we will make to the remote repository will simulate changes made by you on another workstation or by a collaborator in your project.
:::

1. Click on `my-first-file.txt`.
2. Click on **Edit**.
3. Select the option **Open in Web IDE**.
4. Add a second line to this file (for example, `Hi!`).

You will see a `1` on the left bar, indicating that a change has been made.  
If you click on it, you will see a change regarding the `my-first-file.txt` file and the option to **Commit and push to 'main'**.

5. Add a commit message.
6. Click on the **Commit and push to 'main'** button.
7. Click on **Continue** when asked if you want to save it to the default branch (main).

:::{seealso} Branches
:class: dropdown
The concept of branches in Git allows for maintaining a stable version of an application or project. All changes are made on branches other than 'main' (different versions of the repository) and later merged with the 'main' branch.

For example, in a project, the user interface (UI) and the logic part of the code can be separated into two distinct branches, other than 'main'. The main branch ('main') will contain a stable and functional version of the project, while the other two branches will contain development versions.

In this course, we will not cover Git branches or other advanced Git features. You will only work on a single branch: 'main'.
:::

## Synchronizing the local repository with the remote repository

Go back to your local repository and let's assume that a collaborator made changes and then pushed them to the remote repository without you being notified.

1. Run `git status`. Do you notice anything unusual?

:::{important} `git status`
The `git status` command does not know about changes from the remote repository and only displays information about your local repository.
:::

2. Run `git remote update`, then `git status`. What do you see now?

The 'main' branch on the remote repository is different from your local repository. 

3. Run `git diff origin/main` to see the differences between the two repositories.

:::{important} Start with `git remote update && git status`!
:class: dropdown
In practice, when working on a project, it is common that someone else has modified part of the code between two work sessions. If you start a work session without checking the state of your repositories, you may face conflicts to resolve (for example, if you both modify the same parts of the code but differently).

Although we will discuss conflict resolution in this course, it is best to avoid them. That is why it is essential to always start with `git remote update && git status`!

You can also run `git diff origin/main` to see exactly what changes have been made.
:::

4. To synchronize your local repository with the remote repository (which, this time, is ahead), run `git pull`.

5. To view the commit history, run `git log`. To exit the log, press `q` (for 'quit').

## Ignoring files

:::{important} Why should we ignore some files?
During development, certain files must be systematically **ignored** in backups (for example, files generated during compilation) because they unnecessarily clutter the repository.

For instance, a complex game engine like Unity might require up to 1 GB of files to run certain modules, sometimes unused by your game. If these files are not ignored, you could be forced to push or pull 1 GB every time you sync your repositories, while the files specific to your game are only a few MB.

Configuration files can also cause compatibility issues, especially when syncing machine-specific configuration files with others.
:::

1. Create a file `hello-world.cpp` (for example, with `touch hello-world.cpp`).

2. Copy the following code.

```{code} cpp
#include <iostream>
using namespace std;
int main() {
    cout << "Hello World!" << endl;
    return 0;
}
```

3. Compile the code with `g++ -o <filename> <filename>.cpp`.


For example:
```{code} sh
g++ -o hello-world hello-world.cpp
```

4. Run the executable `hello-world` with the command `./hello-world`.

Executables are among the files that we want to ignore.

:::{important} `.gitignore`
The management of ignored files is handled via a `.gitignore` file located at the root of your project. In this file, you can add the names of files and folders you want to ignore permanently.

An example of a [`.gitignore`](https://github.com/github/gitignore/blob/main/Unity.gitignore) for Unity projects, which tend to be more complex.
:::

5. Create the `.gitignore` file.

:::{seealso} Why does `.gitignore` begins with a `.`?
:class: dropdown
Files whose names start with a `.` are hidden files (they do not appear when using a file manager). These are often configuration files.

To display hidden files in a file manager, you can usually press `Ctrl+H` (`H` for 'Hidden').
:::

6. Add the name of the executable `hello-world` in `.gitignore`.

7. Run `git add`, `git commit -m "<message>"`, and `git push` to send the `.gitignore` file to the remote repository.

8. Run `git status`. What changes compared to usual?

:::{hint} Where are my untracked files?
:class: dropdown
Normally, since we didn't add the executable `hello-world` with `git add`, `git status` indicates that there is an untracked file that might be important. However, thanks to the `.gitignore` file, Git recognizes that we've chosen to ignore `hello-world` and will never track changes to this file.
:::

## To finish...

Let's finish this lab with a little reorganization.

1. Create a folder `PS1/` in your project and move `my-first-file.txt` to this folder.

:::{warning} `.gitignore` remains at the root of the project!
The `.gitignore` file should remain at the root of the project, and **not in the `PS1/` folder!**
:::

You will maintain this project throughout the course by creating separate folders for each practical session (PS).

2. Go back to the root of the project and run `git add .`.

:::{important} `git add .`
You can use `git add .` to track all the new changes made (when creating or modifying multiple files or subfolders) **in the current folder**, which is why it is important to go back to the root of the project here.
:::

3. Commit the staged changes with `git commit`.

:::{warning} I forgot to add a commit message...
:class: dropdown
You are here because you ran `git commit` without specifying a message with the `-m` option (`git commit -m "<message>"`). Git is now forcing you to write a message by opening the following window.

```{figure} ../images/commit-message-window.png
:alt: Commit message window
:align: center

Here, we forgot to add a commit message for the deletion of a file named `test.txt`.
```

- Write your commit message on the first line.
- Press `Ctrl+X` to exit.
- Press `y` to confirm the changes.
- Press `Enter` to quit the window.
:::

:::{caution} Keep a readable log!
It is recommended to make structured commits, which involves:
- Writing clear commit messages.
- If you make distinct, unrelated changes, perform separate `git add` and `git commit` for each group of changes with a common theme, or use `git add .` and `git commit` once you have finished working on a part of the project before moving on to another.
:::

Since we will be ignoring many executables in the future, to avoid modifying `.gitignore` every time, we will use regular expressions to ignore files without extensions (executables).

4. Replace the content of the `.gitignore` file with the following code.

```{code}
*
!*.*
!*/
```

:::{important} What do these regular expressions do?
- We begin by using `*` to ignore all files.
- The expression `!*.*` prevents ignoring files with an extension (in other words, files without extensions, such as executables, will be ignored).
- The expression `!*/` prevents ignoring subfolders.
:::

5. Add your supervisor to your project by following the instructions below.
- Click on **Manage** on the left, then select **Members**.

```{image} ../images/manage-members.png
:alt: Manage members
:align: center
```

- Click on **Invite members** at the top right.
- Add your supervisor with the **Maintainer** role, which gives them almost as much control over the project as the **Owner** role.

:::{important} DO NOT FORGET!  
**If your supervisor is not added to your project, they will not be able to evaluate it, and you will receive a grade of 0!**
:::

6. Go back to the [objectives](#objectives) and check the points you have mastered. Practice the commands and points you have not fully understood yet. Call your supervisor over if necessary.

:::{seealso} `git rm`
:class: dropdown
You can also use `git rm <filename>` to delete a file that is already synchronized across both repositories.

A file that only exists in the local repository can be deleted using a file manager or with the `rm` command (without `git`). If you have already added (`git add`) this file to the staged changes, you can remove it using `git restore --staged <filename>`.
:::