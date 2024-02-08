---
title: "Git Tips"
description: "If you have a bad memory like me..."
date: 2024-02-04T00:00:36+08:00
hidden: false
comments: true
draft: false
categories:
- Skills
tags:
- Utility
---


# Git work flow with GitHub

## Basics

```Powershell
git config --global init.defaultBranch main (or master) # set default branch
```

initialize repo for the first time.

```powershell
git init
```

now we just use `git init`.

```Powershell
git add [file 1] [file 2] ... [file n]
git add . // all files
```

add files, i.e., stage files.

### more

```Powershell
git restore --staged [file 1] [file 2] ... [file n]
git restore --staged . // all files
```

restore staged files,.

```Powershell
git rm [file 1] [file 2] ... [file n]
git rm . // all files. (you won't do this, right?)
```

delete some (tracked) files. __which means removing files, you cannot find them in recycle bin!__
*But we can retrieve them from the commit history.*

rename a file:

```Powershell
git mv [old name] [new name]
```

### back to the flow

```Powershell
git commit -m "some commit info..."
git commit -am "some commit info..." 
```

commit staged files.

`-a` represents all staged files,namely, without using `git add`.

```powershell
git status
```

check the status.

## Stash is a good friend

stash is a command that allows you to temporarily store changes that you don't want to commit immediately. Stashing is handy if you need to quickly switch context and work on something else, but you're mid-way through a code change and aren't quite ready to commit.

In a word, stash save *away* changes. If you have multiple approaches to solve a problem, you can use stash to save the current approach and try another one, use stash to store each of them, and choose the best stash to apply.

```powershell
git stash save "some info..."
```

simply stash some changes.

```powershell
git stash list
```

list all stashes.

```powershell
git stash pop
```

apply the latest stash and remove it from the stash list.
if you want to apply a specific stash, use:

```powershell
git stash apply stash@{n}
```

where `n` is the number of the stash you want to apply.

```powershell
git stash apply
```

apply the latest stash and keep it in the stash list.

```powershell
git stash drop stash@{n}
```

remove a specific stash from the stash list.

## Branch

```powershell
git branch
```

view all branches and show current branch, which has `green` color and an `*` attributes.

```powershell
git branch [new branch name]
```

add a new branch.

```powershell
git switch [branch name]
```

switch to a branch.

```powershell
git switch -c [new branch name]
```

create a branch, and then switch to that branch.

```powershell
git merge -m [merge message] [branch]
```

merge a branch to the current branch.

```powershell
git branch -d [branch name]
git branch -D [branch name]
```

`-d` : delete a branch.

`-D` : force to delete a not fully merged branch.

```powershell
git branch -m [branch old name](optional) [branch name]
git branch -M [branch old name](optional) [branch name]
```

The `-m` option : rename a branch.

The `-M` option : forcefully rename a branch.

If the branch already exists, it will be replaced by the new one. This is different from the `-m` option, which will not replace an existing branch with the new one.

If the renamed branch has already been pushed to a remote repository, we need to push the renamed branch and reset the upstream branch:

for example, if the remote branch renamed from `master` to `main`, in local machine you should use:

```pwsh
git branch -m master main
git fetch origin
git branch -u origin/main main
git remote set-head origin -a
```

## Merge & Pull Request

### Local Git Merge

When the forked branch meets the requirements, we can merge it to the main branch.

```powershell
git checkout main
git merge [forked branch]
```

if git shows: `not something we can merge`, here we may encounter some conflicts, it's time to use `git status` to check the conflicts, and then edit the files to solve the conflicts. in VS Code, we can use `merge editor` to solve the conflicts in a more convenient way, i.e., VsSCode UI.

### GitHub Work Flow

when everything up to date, we could create a pull request and add comments. GitHub will automatically check the conflicts. After solving the conflicts, we can merge the forked branch to the main branch.

### When merged but lots of differences still exist

If there are lots of differences between the two branches, but git merge says 'Already up to date', it's possible that the branches have diverged in a way that Git does not recognize as a simple forward or backward set of changes.This can happen if you've rebased one of the branches, or if you've force-pushed to a branch that's been rebased by someone else.We can use:

```powershell
git diff [branch 1]..[branch 2] > [filename].patch
```

This will create a file that contains *__all__* the changes from `branch 2` that are not in `branch 1`.

switch to `branch 1` and use:

```powershell
git apply [filename].patch
```

if having conflicts, solve it.
then commit and push. all done.

here's another way:

```powershell
git checkout [branch 1] // switch to branch 1
git rebase [branch 2] // replay commits from branch 1 on top of branch 2, which means branch 1 will be updated to the latest commit of branch 2. do not misunderstand it.
```

This will replay the commits from `branch 1` on top of `branch 2`. If there are any conflicts, Git will pause and allow you to resolve them before continuing.

```powershell
git rebase --continue // or
git rebase --skip // or
git rebase --abort
```

git will require you to do command above.
*Note*: if `abort`, all uncommitted changes will be lost. so commit your work first!

#### Rename a branch locally

```powershell
git branch -m [old name] [new name] # typically means `move` a branch
```

#### Rename a branch both locally and remotely

```powershell
git push origin :[old branch name] [new branch name] # delete old branch and create a new branch
git push origin -u [new branch name] # set upstream
```

*Note: `-u` is a shorthand for `--set--upstream`.*

#### Change the default branch name (*not recommended*)

```powershell
git branch -M [old branch name] [new branch name]
git push origin -u [new branch name]
```

## Remote SSH & Repositories

### Create a GitHub remote repositories

`.githubignore`

:P

### Create an SSH

#### Windows

in Windows, you should install `Open-SSH` in `optional feature` panel via `settings` first.

```powershell
Get-Service ssh-agent
Start-Service ssh-agent 
```

and then

```powershell
ssh-keygen -t ed25519 -C "emailaddress" // using github-provided noreply email.
ssh-add ~/.ssh/id_ed25519 # If system cannot find the path to the file, using absolute path. e.g. `C:\Users\[user.account.name]\.ssh\id_ed25519.pub`.
Get-ChildItem -Path ~\.ssh -Force # check if the file exists.
Get-Content ~\.ssh\id_ed25519.pub | Set-Clipboard # copy the SSH public key to clipboard.
```

#### Linux

```bash
sudo apt-get install ssh
sudo apt-get install xclip # install xclip to copy text to clipboard.
ssh-keygen -t ed25519 -C "emailaddress"
ssh-add ~/.ssh/id_ed25519
xclip -sel clip < ~/.ssh/id_ed25519.pub
```

---

*Notes:*

The number `25519` in `id_ed25519` refers to the Ed25519 signature scheme, which is a specific algorithm for creating cryptographic signatures. This number is not arbitrary; it's part of the name of the algorithm.

Ed25519 was designed to be a high-security, high-performance signature scheme. It uses elliptic curve cryptography, specifically the curve known as `edwards25519` or `Curve25519`. The `25519` part of the name refers to the large prime number used in the mathematics of the curve, $2^{255}-19$.

Ed25519 has several advantages over other signature schemes such as RSA or ECDSA. It's faster, more secure against various attacks, and generates shorter keys (the private key is 256 bits, and the public key is 128 bits).

---

Navigating to GitHub `settings - SSH and GPG keys`, paste the `SSH public key`.

### When something goes wrong

There were time when my SSH key was not working, and I got the following error message:

```powershell
Pushing to github.com:[user.name]/[repository.name].git ssh: connect to host github.com port 22: Connection timed out fatal: Could not read from remote repository.
```

So far I do not exactly know why this happened, and I switched to `https` but it did not work either.
After struggling for a whole night and checking out GitHub help, although the reason why the error happened is still unknown, I ultimately grasp the key to make it. Here I'm presenting the solution:

First, check if the SSH key is working:

```powershell
ssh -T git@github.com
```

and it shows:

```powershell
Hi [user.name]! You have successfully authenticated, but GitHub does not provide shell access.
```

which means the SSH key is working.

If it shows `Permission denied (publickey).`, then the SSH key is not working. That's another story, you may need to reinstall the SSH key or reconfigure the SSH key.

```powershell
vim ~/.ssh/config 
```

and add the following lines:

```powershell
Host github.com
    Hostname ssh.github.com
    Port 22
    User git
```

reset `known_hosts`:

```powershell
cp ~/.ssh/known_hosts ~/.ssh/known_hosts.bak # create a backup just in case
vim ~/.ssh/known_hosts # delete the line of github.com; you can delete the whole file if you like.
```

Then try `push` again, and you will be pushing to the remote repository with pleasure. Now you can breathe a sigh of relief and enjoy a cup of coffee.

### Basic Configurations

```powershell
git config --global user.name [user.name]
git config --global user.name // show global username
git config --global user.email [user.email]
git config --global user.email // show the global email
```

origin is the default name Git gives to the server from where you cloned your repository. It's essentially an alias for the URL of the remote repository. When you clone a repository, Git automatically creates a remote connection called origin pointing back to the original repository. This is used to fetch updates, push changes, and more.

first time of the repo, we should:

```powershell
git remote add origin git@github.com:username/repositoryname.git
git remote set-url origin git@github.com:username/repositoryname.git
git remote -v # check if the remote is set correctly
git push --set-upstream origin main
```

The `user.name` is the name in GitHub, i.e. @username.

```powershell
git push --set-upstream origin [branch] # set upstream (remote) branch and push
git branch --set-upstream-to=origin/[branch] [branch] # when the branch exists both locally and remotely
```

The `user.email` *must* be public email of GitHub account, or may use GitHub-provided noreply account if email private.

```powershell
git push -u origin [branch] --verbose
```

*you should always use this command instead of `git push --verbose` when pushing a new branch to the remote.*

and then

```powershell
git fetch --verbose
git merge --verbose
```

or just:

```powershell
git pull --verbose
```

`git pull` is essentially a `git fetch` followed by a `git merge`.

Next time we just `git push --verbose`, not `git push -u origin [branch] --verbose` anymore.

---

*Breakdown of the command:*

`origin`: This is the default name Git gives to the server from where you cloned your repository. It's essentially an alias for the URL of the remote repository. When you clone a repository, Git automatically creates a remote connection called `origin` pointing back to the original repository. This is used to fetch updates, push changes, and more.

`main`: This is the branch that you're pushing to the `origin` remote. In many repositories, `main` is the default branch, but it could be named anything. When you run `git push -u origin main`, you're saying "push the commits in the local branch named `main` to the remote named `origin`".

`-u` stands for "upstream". This sets the upstream reference for the current branch. This means that in the future, you can simply type `git push` or `git pull` without specifying a branch, and Git will know that you mean `origin/main`.

`--verbose` provides more detailed information about what's happening during the push operation.

---

```powershell
git clone [url] --verbose # clone a repo, the main branch will be the default branch to clone.
git clone --recursive [url] --verbose # clone a repo with all its submodules(aka. sub-repos)
git clone --recurse-submodules [url] --verbose # same as above.
git clone -b [branch] [url] --verbose # clone a specific branch.
```

clone a repo. the `repo` will be a new folder. So you do not to create a new folder to store the repo.

usually may not `url`, may `ssh`.

## logs

```Powershell
git log // log overview
git log --oneline // simplified log
git log -p // details
git diff [branch 1]...[branch 2] // show differences
```

view logs. type `q` to exit view.

## Recovery

### Revert a merge

restore to previous commit/stage is a good way to solve conflicts, however, it is more dangerous because it may cause data loss if used improperly.

to revert a merge, first we should check out the desired revert commit ID/commit hash using `git log`, and then type:

```powershell
git revert -m 1 [commit-hash]
git revert [commit-hash]
```

`-m 1` means revert to the first parent of the merge commit.

### Restore from local commit

when lost changes due to improper use, first check the commit info:

```powershell
git reflog # short for `reference log`, which is a reference to the commits that are referenced by the tips of branches and tags.
```

get all the commit hash, and then use:

```powershell
git checkout [commit-hash]
```

to view the commit.
if it is the desired commit, save it using:

```powershell
git checkout -b [new branch name]
```

this will create a new branch and save the commit.

### Recover from remote repository

first we should use `git fetch origin` to fetch the latest changes and then:

```powershell
git reset --hard origin/[branch name]
```

we could try compare the local and remote repository before recovery:

```powershell
git diff origin/[branch name]
```

Remember, `git reset --hard` is a destructive operation. It permanently discards local changes and commits. Make sure you really want to discard your local changes before running this command. If you want to keep your local changes, consider using git stash or git branch to save them somewhere else before running `git reset --hard`.

*License: CC BY-NC-SA 4.0.*
