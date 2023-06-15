# Git cheat sheet

- [Git cheat sheet](#git-cheat-sheet)
  - [Basics](#basics)
  - [Branching](#branching)
    - [Keeping branches up-to-date with upstream](#keeping-branches-up-to-date-with-upstream)
    - [Remove old local branches](#remove-old-local-branches)
    - [Stashing](#stashing)
  - [History and logging](#history-and-logging)
  - [HEAD](#head)
  - [Relative refs](#relative-refs)
  - [Branch forcing](#branch-forcing)
  - [Reversing changes in Git](#reversing-changes-in-git)
  - [Cherry-picking](#cherry-picking)
  - [Rebasing](#rebasing)
  - [Amending older commit and reordering](#amending-older-commit-and-reordering)

## Basics

- Clone a repository using HTTPS
  - GitLab
    - `git clone https://gitlab.com/fcty.nl/pbl/code/pbl_windows_gpo.git`
  - GitHub
    - `git clone https://github.com/MarcoJanse/TheFactory.git`
- Clone a repository using SSH
  - GitLab
    - `git clone git@gitlab.com:fcty.nl/pbl/code/pbl_windows_gpo.git`
  - GitHub
    - `git clone git@github.com:MarcoJanse/TheFactory.git`
- Initiate a new local repository
  - `git init`
- Adding files
  - `git add <filename>`
  - `git add *`
- Commit
  - `git commit -m "commit message"`
- Fetch
  - `git fetch`
- Pull
  - `git pull`
- Push
  - `git push origin master`
- List files you've changed and those needed to add or commit
  - `git status`
- Check configured remote repositories
  - `git remote -v`

## Branching

- Create a new branch and switch to it
  - `git checkout -b <branchname>`
- Switch from one brancht to the other
  - `git checkout <branchname>`
  - `git switch <branchname>`
- list al branches
  - `git branch`
- Delete branch
  - `git branch -d <branchname>`
- Push branch to remote repository
  - `git push origin <branchname>`
- Push all branches to remote repository
  - `git push --all`

### Keeping branches up-to-date with upstream

- If you work for some time in a local branch, it's good practice to update your branch with updates from master
  - `git pull origin master`

### Remove old local branches

- Make sure that you're in sync with the remote repo
- check for local branches that have been merged online
  - `git branch --merged`
- Delete local branch
  - `git branch -d <branchname>`
- NOTE: you can use -D to force delete a branch that has not been merged

### Stashing

The `git stash` command can help you to (temporarily but safely) store your uncommitted local changes - and leave you with a clean working copy.

- Save uncommited files temporary before switching branches:
  - `git stash`
- When you're ready to continue where you left off, you can restore the saved state easily
  - `git stash pop`
- In case you want to apply a specific Stash item (not the most recent one), you can provide the index name of that item in the "pop" option
  - `git stash pop stash@{2}`

More info:

- [https://www.git-scm.com/docs/git-stash](git stash documentation)

## History and logging

- Check commit history and find commit ID's
  - `git log`
- Print history of commits on one page at a time:
  - `git log --oneline`
- To print out the history of your commits, showing where your branch pointers are and how your history has diverged:
  - `git log --oneline --decorate --graph --all`

## HEAD

HEAD is the symbolic name for the currently checked out commit -- it's essentially what commit you're working on top of.

HEAD always points to the most recent commit which is reflected in the working tree. Most git commands which make changes to the working tree will start by changing HEAD.

You can attach head to specific commit with `git checkout <Commit ID>`

A detached HEAD means it refers to a specific commit as opposed to referring to a named branch.

## Relative refs

- You can move upwards one commit at a time with `^`
- You can move upwards a number of times with `git checkout <branch/HEAD> ~<num>`
- Going up two parents of main can be done by entering `git checkout main^^`

## Branch forcing

- You can directly reassign a branch to a commit with the `-f` option:
  - `git branch -f main HEAD~3`
  - moves (by force) the main branch to three parents behind HEAD.

## Reversing changes in Git

- Git reset will move a branch backwards af if the commit had never been made in the first place
  - `git reset HEAD~1`
- For remote branches use Git revert
  - `git revert HEAD`
  - This will create a new commit that undoes the previous commit

## Cherry-picking

- To copy commits between branches use git cherry-pick
  - `git cherry-pick <commit1> <commit3>`

## Rebasing

- To do an interactive rebasing from four commits earlier
- .. and choosing only the commits you want and in which order:
  - `git rebase -i HEAD~4`
- Depending on your need, you can use cherry-pick or interactive rebase

## Amending older commit and reordering

- We will re-order the commits so the one we want to change is on top with `git rebase -i`
- We will `git commit --amend` to make the slight modification
- Then we will re-order the commits back to how they were previously with `git rebase -i`
- Finally, we will move main to this updated part of the tree to finish the level (via the method of your choosing)
