# PS5: Error handling and Git integration in VSCodium

(ps5-objectives)=  
The goal of this session is to understand the following points:  
- [ ] [Error handling](#exercise-1--error-handling)  
- [ ] [`git graph`](#alias)  
- [ ] [`git checkout`](#checkout)  
- [ ] [`git revert`](#revert)  
- [ ] [`git reset`](#reset)  
- [ ] [Git integration in VSCodium](#git-with-ide)  

## Exercise 1: Error handling

:::{warning} Disclaimer  
:class: dropdown  
In this exercise, we will focus on writing and understanding error handlers without worrying about code reorganization, file management, or automatic compilation.  
:::  

1. Create a directory `error-handling` inside `PS5`, then add the following files: `level1`, `level2`, `level3`, and `play.cpp`.  

2. In `play.cpp`, copy the following code:  

```cpp
#include <iostream>
#include <string>
#include <fstream>

int getLevelFromUser() {
    int userInput;
    std::cout << "Enter a game level: ";
    std::cin >> userInput;
    return userInput;
}

std::string getLevelData(int level) {
    std::string filepath = "level" + std::to_string(level);
    std::ifstream file(filepath);
    std::string levelData;
    levelData.assign((std::istreambuf_iterator<char>(file)), std::istreambuf_iterator<char>());
    file.close();
    return levelData;
}

void play() {
    int level = getLevelFromUser();
    getLevelData(level);
    std::cout << "You are playing level " + std::to_string(level) + ".\n";
}

int main() {
    play();
    return 0;
}
```

:::{hint} What does this code do?  
:class: dropdown  
We are simulating part of a game where:  
- `getLevelFromUser` retrieves the game level from user input.  
- `getLevelData(int level)` loads the resources for level `level` by opening the corresponding file (e.g., `level1` if `level` is `1`). The content of `level1` is then copied into `string levelData` using `assign`. The file is then closed with `file.close()`, and `levelData` is returned.  
- `play` retrieves the level number and its resources, then simulates gameplay with the message `You are playing level 1.` (if `level` is 1).  
For this simulation, the files `level1`, `level2`, and `level3` must simply exist.  
:::  

3. Compile and execute the code to observe its behavior based on user input.  

4. In `getLevelFromUser`, implement an input validator that allows only 3 attempts, with an integer between `firstLevel` and `lastLevel` (e.g., from 1 to 5). After 3 failed attempts, the function should throw an error.  

We will not handle this error immediately because we want to return to the game’s main menu, which should not be accessible from `getLevelFromUser`. Error handling without terminating the program is always preferable to stopping execution with an error code.  

5. Catch the error in `play` and simulate returning to the main menu by displaying `Returning to main menu.`  

6. In `getLevelData`, check if the corresponding level file exists. If it does not (`if(!file) {...}`), return the message `"Resources not found for level <level number>."` and terminate the program with `EXIT_FAILURE`.  

This is an example of local error handling, which avoids throwing an error that needs to be handled elsewhere, simplifying the code logic.  

## Exercise 2: Git integration

Create a directory `git-integration`.  

### Git without IDE  

First, we will learn to use the following commands in the terminal.  

#### log  

1. Run `git log --oneline` to display the repository history, where each commit appears on a single line, starting with a **commit hash** followed by the commit message.  

:::{hint} `q`  
:class: dropdown  
Reminder: Press `q` to exit the log view.  
:::  

2. Run `git log --oneline --graph` to visualize the commit history as a graph (drawn in [ASCII](https://en.wikipedia.org/wiki/ASCII)), where each commit is represented by a star.  

Your graph should be mostly linear, except where conflicts created divergent branches.  

:::{important} HEAD and origin/HEAD  
**HEAD** represents the current commit in your local repository (not your working directory). Similarly, **origin/HEAD** represents the current commit in the remote repository.  
:::  

#### alias  

When managing multiple branches, you can add `--decorate` (to show branch names) and `--all` (to display all branches) to `git log --oneline --graph`.  

1. Define `git graph` as an **alias** using:  
```{code} sh
git config --global alias.graph "log --oneline --graph --decorate --all"
```

2. Run `git graph`.  

:::{important} `git status` and `git graph`  
During the exercise, run `git status` and `git graph` after each action to better understand what happens!  
:::  

#### show  

1. View details of a specific commit using `git show <commit hash>`.  

#### checkout  

We can navigate through different vertices in this graph, meaning we can return to different states of our repository as recorded by commits.  

1. Run `git checkout <commit hash>` with the hash of a previous commit. For example, your command could look like `git checkout abc0123`.  

:::{important} Detached HEAD  
When you check out an old commit, you enter a **detached HEAD** state, where any changes or new commits will be lost unless you create a new branch.  
We will not cover this process in this exercise.  
:::  

2. Observe the state of your (old) repository.  

3. Return to the most recent commit (and exit the detached HEAD state) by running `git checkout main`.  

:::{seealso} `git checkout -`  
:class: dropdown  
The command `git checkout -` allows you to switch back to the last visited commit before the current HEAD position.  
:::  

#### revert  

1. Create a file named `revert.txt` containing the line `Hello World!` and commit this change.  

2. Add a second line to `revert.txt` containing `Hi!` and commit this change.  

Now, imagine that adding the second line was a mistake. We will **revert** this commit while keeping the commit history intact. This is useful because the erroneous commit might contain valuable information or may have already been pushed to the remote repository.  

3. Run `git revert HEAD --no-edit`, then check the content of `revert.txt`.  

You will see that a new commit has been created with the default message `Revert "<message of the reverted commit>"`. You could also use `git revert HEAD -m "<message>"` to provide a custom message instead of the default one.  

We can also revert multiple commits at once. Each reverted commit will have its own revert commit.  

4. Run `git revert HEAD~3..HEAD --no-edit` to revert the last three commits.  

You can omit the `--no-edit` option if you want to add a message to each reverted commit. Alternatively, you could use `git revert -n HEAD~3..HEAD`, which does not create separate revert commits immediately. Then, you can run `git commit -m "<message>"` to create a single commit (instead of three) with your custom message.  

:::{danger} Reverting an older commit  
Reverting a specific commit `A` in the history (if it is not the latest commit) can be risky, as it might cause conflicts with later commits that modify the same files as `A`.  

It is generally recommended to only revert the latest commits (i.e., `HEAD` or `HEAD~<number>..HEAD`).  

If you need to revert an older commit, you must resolve conflicts manually, add the changes, and then use `git revert --continue`. If you get stuck, you can use `git revert --abort` to cancel the operation.  
:::  

#### reset  

1. Create a file `reset.txt` containing `Hello World!` and commit this change **without pushing**.  

2. Add a second line to `reset.txt` saying `Hi!` and commit this change **without pushing**.  

The `git reset` command works similarly to `git revert`, but instead of creating a new commit to undo the changes, it removes commit `A` from the history.  

3. Run `git reset HEAD~1` to remove the last commit.  

You can also use `git reset HEAD~<number>` to remove the last `<number>` commits.  

:::{important} `--soft`, `--mixed`, and `--hard`  
`git reset` has three main options: `--soft`, `--mixed`, and `--hard`.  

- `--soft` removes commits but keeps their changes as staged changes.  
- `--mixed` (default) removes commits and unstages their changes, keeping them in the working directory.  
- `--hard` removes both commits and their changes completely.  
:::  

:::{danger} Resetting a pushed commit  
If a commit has already been pushed to the remote repository, using `reset` can be dangerous because it alters history. This could require overwriting the remote repository's history with your local one. Although this is possible with `git push --force`, the `main` branch is protected by default (preventing forced pushes). In such cases, **use `revert` instead of `reset`**.  
:::  

### Git with IDE  

Now, open your Git repository in VSCodium.  
If VSCodium asks if you want to regularly perform `git fetch`, you can say yes. If you said no, don’t worry, we will explain `git fetch` below.  

1. In the vertical toolbar on the left, you will find **Extensions**. If you see the error **Error while fetching extensions. XHR failed** on Debian at the IUT, check the [VSCodium support guide](#vscodium-proxy) to configure the proxy.  

2. Search for and install the **Git Graph** extension by **mhutchie**.  

#### log  

1. Open **Git Graph** by clicking the **Git Graph** button at the bottom of your screen or by selecting **View** > **Command Palette** (shortcut `F1` or `Ctrl+Shift+P`), then type `Git Graph` and choose **Git Graph: View Git Graph**.  

:::{seealso} Command Palette  
:class: dropdown  
You can perform all Git operations in VSCodium via the Command Palette, including all Git commands.  
:::  

The graph displayed by the Git Graph extension represents your commit history, with commit hashes on the right.  

#### show  

1. Click on a vertex in the graph to view details of the corresponding commit.  

2. Click on a modified file within a commit to see the specific changes (similar to `git diff`).  

#### status  

1. You can view your repository’s status in **Source Control** (left sidebar).  

#### add  

1. Create a new file.  

2. You can stage the new change in Source Control by clicking **+** next to the change or stage all changes by clicking **+** at the top.  

#### restore  

1. To unstage a change, click **-** next to a staged change.  

2. To discard untracked changes in a file, click the **arrow left of +**, labeled **Discard Changes**.  

#### commit  

1. In the **Message** box at the top of Source Control, write your commit message.  

2. Click **Commit** to commit your changes.  

#### push  

1. Click **Sync Changes** to push your changes.  

#### pull  

1. In Source Control, hover over **...** above the message box to open **More Actions**.  

2. In this dropdown menu, you will find **Pull** among other Git commands.  

You can also use the **Command Palette** (`F1`), type `pull`, and select **Git: Pull**.  

#### clone  

1. Similar to `pull`, you can clone a repository by entering its URL (with a PAT).  

#### config  

1. In the buttons to the right of **Git Graph**, you will find **Repository Settings**, where you can configure repository settings.  

### fetch/remote update  

`git fetch` is almost the same as `git remote update`, but allows you to be more specific by using `git fetch <branch>` to retrieve information about a specific branch.  

By default, `git fetch` only retrieves information from the remote repository named `origin`. In the future, if you contribute to existing projects, you will likely need to **fork** them, creating copies with separate remote and local repositories. In this case, it is useful to fetch changes from multiple remotes, which can be done using `git remote update` (or equivalently, `git fetch --all`).  

1. Click **Fetch from Remote(s)** in the Git Graph buttons.  

Sometimes, clicking **Refresh** is helpful to update the graph view after recent changes.  

#### checkout  

1. In Git Graph, right-click on a commit message and choose **Checkout**.  

2. To return to the latest commit, right-click the `main` branch icon and choose **Checkout Branch**.  

#### revert  

1. In Git Graph, right-click the most recent commit and choose **Revert**.  

#### reset  

1. In Git Graph, right-click an older commit, select **Reset current branch to this Commit...**, and choose **Soft**, **Mixed**, or **Hard** (preferably Soft or Mixed).  

:::{danger} Do not reset a pushed commit!  
:class: dropdown  
Reminder!  
:::  

#### merge  

1. Create an empty file named `merge.txt` and synchronize it so that it exists in both repositories.  

2. Locally, add `Hello World!` to the first line of the file.  

3. On GitLab, use the Web IDE to add `Hi!` to the first line of the file.  

:::{seealso} GitLab Web IDE  
:class: dropdown  
The GitLab Web IDE is based on VSCode/VSCodium, so its Git integration is very similar but without extensions. You can still use the Command Palette.  
:::  

4. Perform a fetch in Git Graph and observe the divergent branches.  

5. Right-click on the commit message of the branch from the remote repository and select **Merge into current branch...** with the **No commit** option.  

6. Open the `merge.txt` file.  

7. You can choose one of the suggested options above the conflict or manually resolve the conflict by writing a new text that replaces both, such as `Bye!`.  

You can also select **Open in merge editor**, which appears in the bottom right corner. This will open three views: two showing the conflicting file versions and a third where you will write the final merged version.  

8. Complete the synchronization with the merged version.  

:::{seealso} `git merge --abort`  
:class: dropdown  
`git pull` performs the same actions as `git fetch` followed by `git merge`. If you forget to check the repository state before pulling or merging, you can use `git merge --abort` to cancel the merge.  
:::

Go back to the [objectives](#ps5-objectives) and check off the points you have mastered. If you need help, ask your instructor.  
