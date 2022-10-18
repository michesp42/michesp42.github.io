---
title: 'How to Set Up a Git Bare Repository to Track Dotfiles'
date: 2022-10-14T12:28:49+08:00
draft: false
categories: ['tutorials']
tags: ['linux', 'dotfiles', 'git']
---

There are many ways to manage
[dotfiles](https://en.wikipedia.org/wiki/Hidden_file_and_hidden_directory) on
Linux and other Unix-like operating systems; you can do it manually, for
example, using shell scripts and even
[Makefiles](https://www.gnu.org/software/make/manual/html_node/Introduction.html),
or you can use dedicated tools like [stow](https://www.gnu.org/software/stow/)
and [yadm](https://yadm.io/). Another common way people do it though is using
_git bare_ repositories, and it is the method of tracking dotfiles that I
ultimately chose upon considering the alternatives.

When you use git normally you have a repository that contains a `.git` folder
meanwhile when you initialize a directory as a git bare repository it becomes
the `.git` folder itself, and you will have to track files that are outside of
it. This is perfect for tracking dotfiles that are all scattered all around in
your home directory.

In the following sections, I will show how to set up a bare repository with
some additional helpful git configurations, especially if you want to add
a `README` and `LICENSE` for your repository but don’t want them cluttering up
your home directory.

## Step 1: Initialize repository

The first thing we need to do is create a directory and initialize it as a git
bare repository. In my case, I usually create a `~/.local/dotfiles/` folder and
use it as the bare repository, but you can use any sensible directory you want.

```bash
mkdir --parent ~/.local/dotfiles/
cd ~/.local/dotfiles
git init --bare
```

Now we have a bare repository and if you list out its contents it will look
something like this

```shell
$ ls -F ~/.local/dotfiles/
branches/       config       HEAD    index  logs/     ORIG_HEAD
COMMIT_EDITMSG  description  hooks/  info/  objects/  refs/
```

which confirms that it really is just like a `.git` directory.

## Step 2: Add an alias

For git to know when we want to work with our dotfiles wherever in the
file system we have to specify a couple of flags, namely

- `--git-dir` that is used to set the path to the repository and, for our
  purposes, this should point to the dotfiles folder we set above
- `--work-tree` that is used to set the working tree, which we'll set to be the
  home directory.

Therefore, we'll have to run something like the following whenever we are
working with our dotfiles.

```bash
git --git-dir="$HOME"/.local/dotfiles --work-tree="$HOME" ...
```

This would be a hassle to type all the time, so we might as well add an alias
for it in our login shell's configuration file, e.g., the `~/.bashrc` file. I’m
calling the alias `dots`, but it can be anything you prefer.

```bash
alias dots='/usr/bin/git --git-dir="$HOME"/.local/dotfiles --work-tree="$HOME"'
```

## Step 3: Additional configuration

### Ignore untracked files

By default, running `git status` will show all untracked files in the working
tree. We don’t want this for the `dots` alias since we are only interested in
the dotfiles we are tracking and not all the other files in our home directory,
so we have to add the following configuration for the bare repository.

```bash
dots config --local status.showUntrackedFiles no
```

### Remove `README` and `LICENSE` from `$HOME`, sort of

{{<admonition>}}
This step is not essential for setting up a bare repository for your dotfiles,
but it might be worth doing for a cleaner home directory.
{{</admonition>}}

If you have a `README` and `LICENSE` file for your dotfiles repository, but you
want to get rid of them from the listings of your home directory, then you will
have to use `git sparse-checkout`. We first need to enable the sparse-checkout
feature and then set `README` and `LICENSE` to be ignored when we execute `dots
sparse-checkout`.

```bash
dots config core.sparseCheckout true
dots sparse-checkout set '/*' '!README' '!LICENSE'
```

The `README` and `LICENSE` will now disappear from the home directory, but they
still exist and are being tracked by git, just not appearing in the working
tree.

## Step 4: Manage the dotfiles

Now we can use the `dots` alias like a normal git command to manage dotfiles
from anywhere in the file system.

```bash
dots add ~/.bashrc
dots commit -m 'add bashrc'
dots push -u origin main
```
