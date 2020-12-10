Git notes
=========

Source: [Pro Git](https://git-scm.com/book/en/v2)

Getting Started
---------------

### What is Git?

-   Comparing LVCS, CVCS, DVCS
    -   CVCS use delta-based, Git more like stream of snapshots
        (illustrations on pg 14)
    -   Almost all operations are local, not requiring interaction with
        server
    -   Three main states that files can be in:
        -   **modified**: changed, but not committed
        -   **staged**: marked a modified file to go into next commit
            snapshot
        -   **committed**: data is stored in local database
    -   Thre main sections of a Git project:
        -   Working tree: single checkout of one version of the project
        -   Staging area: file that stores info about what will go into
            next commit (technical name: the "index")
        -   Git directory: where Git stores metadata and object database
    -   Basic Git workflow:
        1.  Modify files in your working tree
        2.  Selectively stage changes you want to be part of your next
            commit, which adds *only* those changes to the staging area
        3.  You do a commit, which stores that snapshot permanently in
            Git directory

### First-Time Git Setup

-   git config
    -   `[path]/etc/gitconfig`: contains values applied to every user on
        the system and all their repos. Use `sudo git config --system`
        to modify it
    -   `~/.gitconfig` or `~/.config/git/config`: specific to user,
        affects all their repos. Use `git config --global`.
    -   `config` file in the Git directory (i.e. `.git/config`) of a
        given repo. Use `git config --local`.
-   Every level of config overrides the previous level (i.e. local
    overrides global, etc)
-   To view config, `git config --list --show-origin`; use
    `git config <key>` to see the value of a particular key-value pair

### Getting Help

-   `git help <verb>` or `git <verb> --help` or `man git-<verb>`
-   `git <verb> -h` for shorter help
-   `#git` or `#github` on Freenode IRC

Git Basics
----------

### Getting a Git Repo

-   `git init`: initialize a repo in an existing directory
-   `git clone <url>`: clone an existing repo

### Recording Changes to the Repo

-   See pg 27 for illustration
-   `git status`
-   If you modify a file after `git add`, you must add it again if you
    want the newer changes
-   `git status -s`: short status
-   `.gitignore`: see pg 31 for details
-   `git diff`: see changes not yet staged
-   `git diff --staged`: see changes that have been staged; compares
    against last commit
-   `git commit`
-   `git rm`: remove a file and stage it for removal
-   `git rm --cached`: stop tracking the file but keep it in working
    directory
-   `git mv`: convenience function for `git rm <old name>` and `git add
    <new name>`

### Viewing the Commit History

-   `git log`
    -   `-p` or `--patch`: show the difference introduced by each commit
    -   `--stat`: list of modified files, how many were changed, how
        many lines were added/removed, etc.
    -   `--pretty=(oneline|short|full|fuller|format|...)`: see pg 43 for
      details
    -   `--graph`: shows ASCII graph of branch and merge history
    -   `-<n>` where `n` is any integer: show last `n` commits
    -   `--since` and `--until`: example: `git log --since=2.weeks`
    -   `--author`: allows you to filter by commit author
    -   `--grep`: use `--all-match` to require that all criteria match
    -   `-S <string>`: increased or decreased number of occurrences of
      given string
    -   `-- <path to file>`: filter log output to changes to given file
    -   `--no-merges`: filter out merge commits

### Undoing Things

-   `git commit --amend`: fix a commit
-   `git reset HEAD <file>`: unstage a staged change
-   `git checkout -- <file>`: discard changes in working directory

### Working with Remotes

-   Remote repositories: versions of your project that are hosted on the
    Internet or network somewhere
-   `git remote`: see which remote servers you have configured
-   `origin`: default name Git gives to server you cloned from
    -   `-v`: shows URLs of remotes
-   `git remote add <shortname> <url>`: manually add a remote
    -   Can now refer to this remote using `<shortname>`
-   `git fetch <remote>`: get data from your remote; after you do this,
    you have references to all the branches from that remote which you
    can merge or inspect
    -   `fetch` only downloads the data to your local repo; it doesn't
        automatically merge it with any of your work or modify what
        you're working on
-   `git pull`: if your current branch is set up to track a remote
    branch, this will automatically fetch and then merge that remote
    branch into your current branch
-   `git push <remote> <branch>`: push commits upstream
    -   Example: `git push origin master`
    -   If someone else has pushed since you last cloned/pulled, your
        push will be rejected; you must first pull their changes and
        incorporate it into yours
-   `git remote show <remote>`: see more info about a particular remote
-   `git remote rename <old name> <new name>`: change shortname for
    remote
-   `git remote remove <remote>`: remove a remote

### Tagging

-   Tagging can be used to tag specific points in a repo's history
-   `git tag`: list the existing tags
-   Two types of tags:
    -   lightweight: just a pointer to a specific commit
    -   annotated: stored as full objects in Git database
-   `git show <tag>`: see the commit that was tagged
-   `git tag <tag> <commit hash>`: apply a tag to a commit
-   `git push origin <tagname>`: required to share tags
    -   `git push origin --tags`: used to push all tags at once
-   `git tag -d <tag>`: delete a tag
    -   `git push origin --delete <tagname>`: remote tag from remote
-   `git checkout <tag>`: view the versions of files a tag is pointing
    to (note: puts your repo in "detached HEAD" state)

### Git Aliases

-   Examples:
    -   `git config --global alias.co checkout`
    -   `git config --global alias.unstage 'reset HEAD --'`
    -   `git config --global alias.last 'log -1 HEAD'`

Git Branching
-------------

### Branches in a Nutshell

-   When you make a commit, Git stores a commit object that contains:
    -   Pointer to the snapshot
    -   Author's name and email
    -   The commit message
    -   Pointers to the commit(s) that directly came before this commit
        -   The initial commit has 0 parents
        -   Normal commits have 1 parent
        -   Commits that result from merge of two or more branches have
            multiple parents
-   See pg 63 for illustration of commits and their trees
-   A branch in Git is simply a lightweight movable pointer to a commit
-   When you commit, the `master` branch pointer (or whichever branch
    you're on) moves forward automatically
-   `git branch testing`: creates a new branch called `testing`
    -   Note: it doesn't switch to it, merely creates it; to create and
        switch, use `git checkout -b <new branch name>`
-   `HEAD`: a pointer to which branch you are on
-   `git checkout testing`: switch to `testing` branch
    -   `HEAD` now points to `testing` branch
    -   Git won't let you changes branches unless it can do it cleanly
-   `git log --oneline --decorate --graph --all`: will print out the
    history of your commits, showing where your branch pointers are and
    how your history has diverged

### Basic Branching and Merging

-   Example scenario:
    1.  Create a branch: `git checkout -b iss53`
    2.  Do some work on that branch: `git commit -m "Fixed issue #53"`
    3.  Get notified of a critical issue
    4.  Switch to production branch: `git checkout master`
    5.  Create a branch to add the fix: `git checkout -b hotfix`
    6.  Merge the fix branch, push to production:
        -   `git checkout master`
        -   `git merge hotfix`
        -   `git push`
    7.  Delete `hotfix` branch: `git branch -d hotfix`
    8.  Switch back to earlier branch and continue working: `git
    checkout iss53`
    9.  Note: The `hotfix` changes are not in the `iss53` branch
        -   You can merge them in with `git merge master`
        -   Or, you can wait to integrate those changes until you decide
            to pull the `iss53` branch back into `master` later
            -   This will perform a three-way merge between the master,
                the branch, and the common ancestor snapshots; it will
                create a merge commit which has more than one parent
            -   You can now delete the branch: `git branch -d iss53`

-   Merge conflicts
    -   If the merge doesn't go smoothly, you will need to resolve a
        merge conflict
    -   `git add` to indicate the conflict has been resolved
    -   Can use `git mergetool` for a GUI
    -   `git commit` to merge the commit

### Branch Management

-   `git branch`: with no arguments, lists current branches
    -   `-v`: shows last commit on each branch
    -   `--merged` and `--no-merged`: filter the list by branches that
        you have or have not yet merged into the branch you're on
    -   `git branch --no-merged <branch name>` to ask about the merge
        state with respect to some other branch without checking it out
-   See pg 79 for info about renaming branches

### Branching Workflows

-   Long-running branches: you can have a development branch that is
    farther along than your master branch, which is more stable
-   Topic branches: short-lived branch for a particular feature

### Remote Branches

-   Remote references are pointers in your remote repositories,
    including branches, tags, etc.
    -   `git ls-remote <remote>` or `git remote show <remote>`: get a
        list of remote references
-   If you do some work on your local `master` branch, and someone else
    pushes to the remote `master` branch, your histories will look
    different
    -   To synchronize, run `git fetch <remote>` which updates your
        local database and moves your `origin/master` pointer to its new
        position
-   If you have a branch you want to share, you have to push it: `get
    push <remote> <branch>`; when others do `git fetch <remote>`, they
    will get a remote-tracking branch
    -   However, they won't have local, editable copies of them
    -   For that, they can do `git merge <remote>/<branch>` to merge this
        into their current working branch
    -   Or they can do `git checkout -b <branch> <remote>/<branch>` to
        create their own local copy of the branch
-   Tracking branches: local branches that have a direct relationship to
    a remote branch
    -   If you're on a tracking branch and do `git pull`, Git
        automatically knows which server to fetch from and which branch
        to merge in
    -   `git checkout -b <branch> <remote>/<branch>` has a shorthand:
        `git checkout --track <remote></branch>`; you can also just do
        `git checkout <branch>` if the branch you're trying to checkout
        out doesn't exist and exactly matches a name on only one remote
    -   `git branch -vv`: see what tracking branches you have set up
        -   Note: this doesn't fetch from the server, it only gives you
            info in your database since the last time you fetched
        -   For up-to-date info, do: `git fetch --all; git branch -vv`
-   `git fetch` will fetch all the changes on the server that you don't
    have, but it won't modify your working directory at all
-   `git pull` is essentially a `git fetch` followed by `git merge`
-   `git push <remote> --delete <branch>`: deletes a branch from the
    remote

### Rebasing

-   Two main ways to integrate changes from one branch into another:
    `merge` and `rebase`
    -   Whereas `git merge` will do a three-way merge between the
        most-recent commits on each branch and their common ancestor
        (creating a merge-commit in the process), `git rebase` will
        instead start with the common ancestor and apply the changes in
        turn (see pg 94 for illustration)
    -   Rebase sequence:
        -   `git checkout experimental`
        -   `git rebase master`
        -   `git checkout master`
        -   `git merge experimental`: fast-forwards the `master` pointer
    -   Rebasing replays changes from one line of work onto another in
        the order they were introduced, whereas merging takes the
        endpoints and merges them together
    -   See pg 96 for details about `--onto` option for rebase
-   **Important**: Do not rebase commits that exist outside your
    repository and that people may have based work on
-   `git pull --rebase`: `git fetch` followed by `git rebase
    origin/master`
    -   `git config --global pull.rebase true`: make `--rebase` the
        default for `git pull`

[comment]: # (Git on the Server)
[comment]: # (Distributed Git)
[comment]: # (GitHub)
[comment]: # (Git Tools)
[comment]: # (Customizing Git)
[comment]: # (Git and Other Systems)
[comment]: # (Git Internals)
