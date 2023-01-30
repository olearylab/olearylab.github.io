---
Title: Git fetch and merge
Date: 2022-07-26
---

#git

# Why fetch & merge, not pull

## Introduction

When using git, you have two types of branches: _local_ and _remote-tracking_ branches.
You can see your local branches by typing `git branch`,
and your remote-tracking branches by typing `git branch -r`.
E.g.:

```zsh
thomasburger@MBPvanThomas2 NeuronBuilder % git branch
  master
  spikerasters
* synapse_bugfix
  test_branch
  ```

and

```zsh
  thomasburger@MBPvanThomas2 NeuronBuilder % git branch -r
  origin/HEAD -> origin/master
  origin/Modules
  origin/NetworkBuilder
  origin/PlasticityRules
  origin/ProperSynapses
  origin/Units
  origin/channel_libraries
  origin/compathelper/new_version/2021-03-15-01-00-10-022-3170862194
  origin/compathelper/new_version/2021-03-15-01-00-13-698-1560394130
  origin/compathelper/new_version/2021-03-17-01-00-29-146-1232274421
  origin/compathelper/new_version/2021-04-01-01-14-14-873-2255874221
  origin/compathelper/new_version/2021-04-28-01-13-49-151-1696217232
  origin/compathelper/new_version/2021-04-28-01-13-54-539-2275432496
  origin/gitignore
  origin/master
  origin/observers
  origin/resetneuron
  origin/synapse_bugfix
  ```

The names of the remote-tracking branches are the name of the remote
(usually 'origin' or whatever name you gave it),
followed by "/" and the name of the branch on the remote.
Both type of branches are stored locally on your machine in the form of compounding commits.

An important difference is 
that remote-tracking branches are safely updated by `git fetch` or `git push`.
One does not work on remote-tracking branches correctly,
in contrast with local branches,
where you move the tip of the branch through committing.

Common operations involving remote-tracking branches are

- Updating them with `git fetch`
- Merge them into currently checked-out branch
- Create new local branches based on them

## Creating local branch from remote-tracking branch

Creating a new local branch from a remote-tracking branch
can be achieved by doing `git branch --track` or `git checkout -b`

For example, you can do `git branch --track fixed origin/fixed`.
This creates a local branch `fixed` which corresponds to the remote-tracking branch
`origin/fixed`.
Alternatively, `git checkout --tracked -b fixed origin/fixed` immediately changes the working tree to be on the `fixed` branch.
Because the input branch `origin/fixed` is a remote-tracking branch,
git automatically sets up the newly created local branch `fixed`
to track the remote-tracking branch `origin/fixed`.

Note that, for both of these commands,
git is able to infer the `--track` option when an input is a remote-tracking branch,
and it can be omitted.

## Updating from remote repository

You can update your remote-tracking branches from the remote `origin`
by doing `git fetch origin`.
This might give output like

```zsh
remote: Counting objects: 382, done.
remote: Compressing objects: 100% (203/203), done.
remote: Total 278 (delta 177), reused 103 (delta 59)
Receiving objects: 100% (278/278), 4.89 MiB | 539 KiB/s, done.
Resolving deltas: 100% (177/177), completed with 40 local objects.
From ssh://longair@pacific.mpi-cbg.de/srv/git/fiji
 3036acc..9eb5e40  debian-release-20081030 -> origin/debian-release-20081030
* [new branch]      debian-release-20081112 -> origin/debian-release-20081112
* [new branch]      debian-release-20081112.1 -> origin/debian-release-20081112.1
 3d619e7..6260626  master     -> origin/master
```

The important bits are in lines like
```
 3036acc..9eb5e40  debian-release-20081030 -> origin/debian-release-20081030
* [new branch]      debian-release-20081112 -> origin/debian-release-20081112
```
Here, `3036acc..9eb5e40` says that the remote-tracking branch `origin/debian-release-20081030`
was advanced from commit `3036acc` to commit `9eb5e40`.
`debian-release-20081030` is the name of the branch on the remote.
The line beginning with `* [new branch]` says that a new branch on the remote was created
since the last time we `fetch`ed.

The advantage of `git fetch` is that is leaves the working tree untouched.
To actually incorporate the changes from the remote into the working tree,
we have to do a `git merge`.

So if we are working on a `master` branch, after doing `git checkout master`,
we can do

```zsh
git merge origin/master
```

If, instead, you want to see what the differences are between your local branch and the remote-tracking branch
you can do

```zsh
git diff master origin/master
```

By doing `git fetch` you have some breathing room to examine what would happen if you merge,
and to decide what you want to do next.

## Updating remote branches

Say you have made changes in your local branch `experiment`,
and you want to push these to the remote repository `origin`.
This is achieved simply by doing

```zsh
git push origin experiment
```

You may get some error messages.
For example, if you get the error that the remote repository cannot fast-forward the branch,
if probably means someone else pushed changed into that branch.
In that case, you'll need to fetch and merge to incorporate their changes before pushing again.

As a side effect, this will also update your remote-tracking branches,
corresponding to the branches in the remote you are pushing to.
