---
title: What is a Git Submodule?
description: We explore the topic of git submodules. What are they? How they work? When to use them?
---
Initial Date: 2025-03-12   
Status:🪴Sprout (Work in Progress)   

## Definition   

> "Submodules allow you to keep a Git repository as a subdirectory of another Git repository. This lets you clone another repository into your project and keep your commits separate."
> 
> *[Git Docs](https://git-scm.com/book/en/v2/Git-Tools-Submodules)*

## Use cases examples  

- Code you want to share between projects but manage from one source only (E.g.: Useful Hooks, Shared UI Components, etc.).  
- Content files (like `.md` files) managed and stored in a separate repo but used to populate a web project (I use this technique to keep this garden's content separated from the website's implementation repo).    

## Useful commands  

**To configure a submodule** (Command used in the base repository). 

```bash 
git submodule add <repo-url>
```

This command **clones the contents of the submodule repo into a folder in the base project**. The name of that folder is determined by the name of the submodule repository. 

After adding a submodule, a new file called `.gitmodules` is added to the base repository. In this file git stores the connections between different modules.  

It's important to **keep in mind that we are storing a reference to a folder from a different repository, but the files contained in said folder won't transfer automatically** just by adding a submodule.  

**To synchronize all the files between repositories** you'll need the following commands 👇

If you cloned a repo that has submodules --> **Initialize the local config between submodule and parent repo** (Command used in the base repository).

```bash
git submodule init
```

**To fetch all data from submodules** (Command used in the base repository)

```bash
git submodule update
```

To update the data from a submodule (Command used in the base repository)

```bash
git submodule update --remote <submodule-name>
```

This command **will synchronize the information that is available in the default branch of the submodule** (usually the `main` branch). If you need a more custom config, [checkout the git documentation for a step-by-step custom implementation](https://git-scm.com/book/en/v2/Git-Tools-Submodules).  