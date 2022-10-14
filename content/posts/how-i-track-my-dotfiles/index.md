---
title: "How I Track My Dotfiles"
date: 2022-10-14T12:28:49+08:00
draft: false
---

{{< admonition warning >}}
This is still just a test blog (really the README of my [dotfiles
repo](https://gitlab.com/michesp42/local-dotfiles)) published for my restarting
of this website.
{{< /admonition >}}

## Local dotfiles

These dotfiles are tracked using a git bare repository and need these
[scripts](https://gitlab.com/michesp42/local-scripts) to work properly.

## Set up for git bare repo

These are the steps for setting up this git bare repository in case I forget
(which I probably will).

### Step 1: Initialize repository

Create and initialize the git bare repository with the following commands.

```bash
mkdir --parent ~/.local/dotfiles/
cd ~/.local/dotfiles
git init --bare
```

The directory `~/.local/dotfiles` can be replaced with any sensible directory
you prefer.

### Step 2: Add alias

For git to know when we want to work with our dotfiles we have to specify a
couple of flags, namely `--git-dir` and `--work-tree`. This would be a hassle to
type all the time so we might as well add an alias for it in our `~/.bashrc`. In
this case I’m calling it dots but it can be anything you prefer.

```bash
alias dots='/usr/bin/git --git-dir="$HOME"/.local/dotfiles --work-tree="$HOME"'
```

### Step 3: Additional configuration

#### Ignore untracked files

By default, running `git status` will show all untracked files in the work tree.
We don’t want to have this for the the dots alias since we are only interested
in the dotfiles we are tracking and not all the other files in our home
directory so we have to add the following configuration for the bare repository.

```bash
dots config --local status.showUntrackedFiles no
```

#### Remove `README` and `UNLICENSE` from `$HOME`, sort of

The `README` and `UNLICENSE` are located in the root of the dotfiles repository
so that when they are pushed to the remote repository they will be recognized
and rendered/processed(?) by gitlab and github. However, I don’t want to keep
seeing them in my home directory. I would have just put them somewhere else if
it was that simple but this seems not to be the case based on the discussion in
this [issue](https://github.com/TheLocehiliosan/yadm/issues/93). Luckily, I
found a solution from the same link and that is git `sparse-checkout`. I just
have to run the following.

```bash
dots config core.sparseCheckout true
dots sparse-checkout set '/*' '!README.org' '!UNLICENSE'
```

The commands above are explained
[here](https://github.com/TheLocehiliosan/yadm/issues/93#issuecomment-582585718)
and
[here](https://github.com/TheLocehiliosan/yadm/issues/93#issuecomment-886667802).

### Step 4: Manage the dotfiles

Now we can use the `dots` alias like a normal git command to manage dotfiles.
