---
title: Git Cheatsheet
description: Git commands I frequently need but can't remember
---
Initial Date: 2025-03-19   
Status:🪴Sprout (Work in Progress)  

----
## Basics   

To fetch a remote branch from GitHub:  

```bash
git fetch <remote> <branch>
```

----

How to clone a private repo using your personal access token (GitHub):  

```bash
git clone https://<token>@github.com/<your account or organization>/<repo>.git
```

----

How to check and update git credentials for any repository:   

```bash
git config --list
```

```bash
git config user.name "My Name"
git config user.email "myemail@email.com"
```

----

How to delete a folder   

```bash
rm -rf <folder-name>
```

----
## Remotes  

How to get the remote URL of a repo.

In this case the remote is called `origin`, but works with other cases changing the name. 

```bash
git ls-remote --get-url <origin|remote-name>
```

---

How to obtain more information about the remote repo   

```bash
git remote show <origin|remote-name> 
```

---

How to get a list of all remotes URLs  

```bash  
git remote -v
```

---

How to remove a remote you no longer need  

```bash
git remote remove <remote-name>
```

---

## Commits  

How to fix a commit message gone wrong  

```bash
git commit --amend -m "correct commit message"
```

----

## Submodules  

Check this note for more information, use cases and common commands: [What is a Git Submodule?](/development/git/what-is-a-git-submodule)
