# odds-and-ends

Random knowledge that might as well be publicly shared.

## Git

### Squash commits w/o using rebase

Let's say we are working in a project with a linear history. In this case, we
don't want to maintain a history of branches or merge commits. We can use
interactive rebasing to work through our commits and squash them. But if we
don't actually need to rebase, and just need to combine the last N commits into
one commit, and fast-forward our mainline branch, then...we can do that more
directly!

We can figure out our squash point a couple different ways, but this way, we
just grab the first commit.

```bash
git log main..HEAD --oneline | tail -1
```

One we have the SHA1, we can soft reset and amend to squash all of our work into
one commit:

```bash
git reset --soft <MAGIC_TARGET_SHA1>
git commit --amend
```

Finally, we can switch to `main`, fast-forward onto our squashed commit, and
delete the old branch!

```bash
git checkout main
git merge <SQUASHED_BRANCH> --ff-only
git branch -D <SQUASHED_BRANCH>
```

But can we do better? Turns out we can!

What if we just rolled back the ENTIRE branch, instead of just rolling up into
the first commit? Then we can commit the working changes as a single commit on
our target branch!

Assuming we start out on our target branch, and we want to collapse our work
onto `main`:

```bash
git reset --soft main
git checkout main
git commit -m "description for the collapsed branch"
git branch -D <SQUASHED_BRANCH>
```

Sources

* [SO: how-can-i-merge-two-commits-into-one-if-i-already-started-rebase](https://stackoverflow.com/questions/2563632/how-can-i-merge-two-commits-into-one-if-i-already-started-rebase)
* [SO: git-how-to-squash-all-commits-on-branch](https://stackoverflow.com/questions/25356810/git-how-to-squash-all-commits-on-branch)
