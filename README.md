# studious-meme

Welcome to the rebase playground.

Introduction
---

1. This repo has four main branches (poem, modernPoem, alphabet, and readme).
2. To set a tracking branch, use this command:

    git branch --set-upstream-to origin/<nameOfBranch>

3. To unset a tracking branch:

    git branch --unset-upstream

4. In general, reset your work by (both these commands can be destructive normally, but since this is a playground, they are safe...):

    git checkout -B <nameOfBranch> origin/<nameOfBranch>
    git branch --unset-upstream
    # If necessary, do
    git reset --hard

5. You can delete a local branch with

    git branch -D <branchName>

Core Concept: Canonical Form
----------------------------

For practical purposes, the canonical form of rebase is:

    git rebase <base> --onto <destination>
    git rebase -i <base> --onto <destination>

with the second form being used for making interactive changes to history.

ALL other forms of rebase are shortcuts of this one concept. So understanding and visualizing the meaning of this command is essential; everything afterwards -- such as rewriting history or merge resolution -- is fluff. Important fluff, but fluff.

If you visualize rebase as cutting a length of rope and tying it to another rope, where are three questions to ask about each rebase?
a. Where will we start cutting from? That is, where does the commit chain we are moving start?
b. Where will we stop cutting? That is, where does the commit chain we are moving end?
c. Where will we attach? That is, where will we attach the commit chain we previously cut?

Let's get to understanding the canonical form through a series of demonstrations:

### Concept 1: Commit reordering

1. Let's add these very instructions to the alphabet branch.

    git checkout -B alphabet origin/alphabet
    git branch --unset-upstream
    git cherry-pick origin/readme

2. Let's do the same thing with rebase

    git checkout -B readme origin/readme
    git branch --unset-upstream
    git rebase origin/alphabet

3. Let's construct our own branch.

    git checkout -B ourOwnBranch origin/master
    git branch --unset-upstream
    git cherry-pick origin/readme
    git cherry-pick origin/letterC
    git cherry-pick origin/letterA
    git cherry-pick origin/letterB
    git cherry-pick origin/modernPoem~
    git cherry-pick origin/modernPoem

4. Let's do a similar reconstruction, except with rebase.

    git checkout -B altOwnBranch origin/readme
    git branch --unset-upstream
    git rebase origin/letterC
    git rebase origin/modernPoem

Did we succeed? What's the same about what we did? What's different?

### Concept 2: Commit selection

So far, we've taken advantage of two implicit behaviors:
a. rebase moves all commits not found between the current branch and the upstream.
b. rebase uses upstream as newbase when it isn't given.

Let's make these behaviors more explicit.

1. Let's add some letters to our poem.

    git checkout -B exercise2a origin/letterC
    git branch --unset-upstream
    git rebase origin/poem

How many letters did we copy?

2. Let's try again, but with an explicit upstream:

    git checkout -B exercise2b origin/letterC
    git branch --unset-upstream
    git rebase origin/master

That didn't do much -- git found that there were three commits (letterA, letterB, and letterC), but those already existed in our branch. So there wasn't anything to do. Let's be more explicit:

3. Let's try again, but with an explicit upstream:

    git checkout -B exercise2c origin/letterC
    git branch --unset-upstream
    git rebase origin/master --onto origin/poem

There we go. That's the same result as exercise A.

4. Let's try another variant...

    git checkout -B exercise2d origin/letterC
    git branch --unset-upstream
    git rebase origin/letterA --onto origin/poem

And now we've demonstrated what the canonical form does. It moved the commits for letters B and C onto the origin/poem commit.

Core Concept: Interactive Rebasing
----------------------------------

Let's show off what we can do with interactive rebasing.

    git checkout -B fun origin/letterA
    git reset --hard origin/alphabet
    # Git will likely configure the tracking branch for you. Check with `git status`. If not, do
    git branch --set-upstream-to origin/letterA

Now let's rebase interactively:

    git rebase -i
    git rebase -i origin/letterB

(For now, you can just close the editing window when done.) What's the difference between the two commands?

### Exercises


    1. Rewrite history, changing the commit ordering.
    2. Rewrite history, changing the commit messages.
    3. Rewrite history, combining all the commits into a single commit.

Core Concept: Merge Resolution
------------------------------

Let's combine our modern poem changes onto the poem changes.

    git checkout -B games origin/modernPoem
    git rebase origin/poem
    # Resolve conflicts
    git add poem.txt
    git rebase --continue

### Exercise

    1. Rebase the poem changes onto modernPoem, e.g.
    git checkout -B nightmare origin/poem
    git rebase origin/modernPoem
    # Repeat the resolve, `git add poem.txt`, and `git rebase --continue` steps. Repeatedly.

Good luck. This one is hard. (By the way, these kinds of problem are why a quality merge resolution tool like BeyondCompare or equivalent is an amazing resource, even if you only use them occasionally.)

Exercises
---------

### Stacking Challenge
1. Stack the poem changes on top of readme on top of the alphabet.
2. Use interactive rebase to change the order to alphabeta, then poem, then readme.
3. Use interactive rebase to make all the alphabet changes in a single commit.

### Build your own playground
1. Create a new branch in master.
2. Write your own poem with at least five different commits to the same file.
3. Add the changes for the letters A, C, and T.
4. Without merging, include the latest modern poem.
5. Rearrange the commits to your poem so they happen in a different order.
