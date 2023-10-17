# Week 1: Introduction and Source Control

## Day 1 - October 17

Welcome to CS 480! Today we will accomplish the following:

* Go over the syllabus
* Go over the "bird's-eye view" of the content we will be covering in this course
* Assign you into your course project teams
* Ensure everyone is set up to work with Git, GitHub and so on (attendance!!)
* Make your first commit to your project repository
* Assign flipped-classroom presentations for Friday and next week
* I'll give an example of a student presentation covering the absolute basics of Git as a refresher/review. 

## Day 2 - October 19

**Presentations for today:**

* Git: Branching and Simple Merging
* Git: Merge Conflicts

Today we'll do some in-class exercises on branching and dealing with merge conflicts. You'll deliberately introduce a merge conflict within your team, and then you'll address and fix it. Bring your laptops!

### Discussion: Branching and Merging

A *branch* is a diverging series of commits starting at a specific point in the Git history. By making a branch, you can work independently - you can make commits and push them to your own branch without affecting anyone else's work.

![Image illustrating Git branches](https://raw.githubusercontent.com/gist/dornfeder/13abff279de357f048e474d4ed6c692d/raw/2dbb70c4b14f3b83170a139649b6ff496f15d05a/example-diagram.svg)

However, eventually you'll probably want to have your changes incorporated into the "main" branch. To do this, you use the *merge* operation - this operation causes Git to compute the steps necessary to integrate your code with whatever the current state of the target branch is:

* If nothing else has happened on the target branch since you made your branch, your changes are just tacked onto the history of the target branch and it is "fast-forwarded" to match your branch.
* If other things have happened on the target branch, but it is possible to *automatically merge* your branch with it, a "merge commit" will be created which represents the changes that your branch brings to the current state of the target branch. 
  * Automatic merge is generally possible if your changes and the target branch's changes are in different files, or if it is possible to logically determine which parts of a single file have been changed by which person. 
* If both you and the target branch have changed the *same* part of a file and an automatic merge cannot be done, a *merge conflict* occurs. This will be covered in the next presentation, but the general process is that you must manually decide what the "correct" state of the target branch is and manually create a merge commit to finish the merge.

When working locally on your own machine, you can pretty much merge any branch with any branch with no restrictions. But most of the time you will be working with a remote Git repository. Git repositories hosted on source code control services (such as Github) typically employ *policies* to control how merges are done to specific branches - this is especially important for branches that represent production code or "source of truth"  code. 

For example, a project might have a `release` branch. At any given time, this branch always represents the current production, live version of the code - it's the code that has been deployed to the application server, compiled and uploaded to the App Store, etc. (It's also a prime target for continuous deployment - something we'll be looking at later in this course.) Naturally, you want to protect what gets pushed to this branch. Therefore, it is very common for such a branch to have a policy in place that requires a *pull request* (sometimes called a *merge request*) in order to merge into the branch. Depending on the platform, the rules for completing the pull request can be complex - for example, a release branch may be configured such that any merge into release must be approved by at least two approved reviewers, must pass all automated tests, must demonstrate a certain threshold of code coverage in its tests, and must not create a merge conflict.

We'll be looking a lot more into branch policies in our later discussions, but the key for this week is that remote Git servers can and do implement policies that protect pushing and merging to certain branches.

### Discussion: Merge Conflicts

You've just finished coding for the day and it's time to go home. Before you leave, though, you decide to merge your changes in with an upstream branch. You pull in the branch, run the merge and...

    CONFLICT (content): Merge conflict in code/my_latest_feature.py

You slowly lower your head, prepared to bang it against the desk repeatedly. *Not this again!*

![Animated image of a cartoon cat banging its head on a desk](https://media.tenor.com/2YOol__vPDAAAAAi/head-banging-cute.gif)

Resolving merge conflicts can be challenging because a merge conflict can come from many different situations. However, the one thing all merge conflicts have in common: the branch you are on and the branch you are trying to merge into have both experienced *similar* changes since you branched off from the target branch. 

Imagine that we have a text file in a repository. You just made a change to the file on a branch named `branch2` and made a commit with that change. You now want to merge your change into a branch called `branch1`. However, someone has already made a very similar change to the same file and pushed it to that branch. In this case, when you try to merge, you'll see something similar to this:

    Auto-merging file.txt
    CONFLICT (content): Merge conflict in file.txt
    Automatic merge failed; fix conflicts and then commit the result

This is teling us that Git was not able to merge `file.txt` because the sam parts of the file were changed both on your branch and on the target branch. To see exactly what happened, we simply open the file in our favorite text editor. We'll see something like this:

    Welcome to merging!

    <<<<<<< HEAD
    Who wishes they were still sleeping?
    =======
    Who enjoys being up at 8 AM for class?
    >>>>>>> branch1

There's a few things happening here. First of all, Git has added separator lines to the file. Secondly, it's added reference names to point out which branch each section of the conflict block refers to.

`HEAD` ???

Time for a bit of a detour into Git nomenclature. `HEAD` simply refers to "the current state of the code" - that is, whatever code is actually present in your source directory at this specific time, including changes that you may not have committed yet. 

The first line of the merge conflict contains a series of less-than signs (`<`). This means that the following lines are *your* current code. Following the line(s) of code in your current copy, you'll see a line of equals signs (`=`), followed by some more code. This code is what exists at the same location in your destination branch. The end of the merge conflict is denoted by a series of greater-than signs (`>`) followed by the name of the target branch.

Your job is simple - make the code "correct". In other words, remove the markers (the lines with `<<<<<<<` and so on), tweak the code so that it represents what you want to actually commit, and then make a new commit in the traditional way. The commit you make can include your code, the destination's code, neither, both, or whatever else you want. You could replace the entire merge conflict with something entirely new, or you could incorporate changes from both branches together. 

If you do *nothing* and just do an add/commit, you'll actually commit the merge conflict markers into source control - be careful! At a minimum, you certainly want to remove the markers. 

While you're in this "partial merge" state, you can't check out other branches or work on other parts of the code repository. You need to either complete the merge by manually changing the conflicting files to fix the conflicts, or you can revert the merge completely by using the command `git reset --hard` - this will cause the in-progress merge to be reversed and your code will be put back to the last commit you made prior to starting the merge.

There is another time when you might see a merge conflict: when you are *pulling* code from the remote server. There is only one situation where this occurs: when someone has pushed commits onto the same branch you are working on, and you have also made commits on the branch that were not pushed to the server prior to the other user pulling the branch and pushing the commits. For example:

1. Alice checks out the `test` branch.
2. Bob also checks out the `test` branch.
3. Alice makes a change and commits it to the `test` branch and pushes her changes to the server.
4. Bob also makes a similar change and commits it to the `test` branch.
5. Bob tries to push his changes to `test`, but he is told that there are changes that he must first merge before he can push his changes. He thus issues a `git pull`, which generates the merge conflict. (Alternatively, Bob proactively pulls from the remote repository prior to pushing his code - the result is the same.)
6. Now Bob has to resolve the merge conflict, make a new commit and push it to the remote server. In this case, the merge refers to the same branch, but to "divergent histories" - the commit log diverged when two users both committed off the same initial commit.

There's more stuff you can do with merge conflicts that we'll talk about next week during our Advanced Source Control series.
