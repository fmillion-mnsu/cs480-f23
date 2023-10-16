# Week 1: Introduction and Source Control

## Day 1 - October 17

Welcome to CS 480! Today we will accomplish the following:

* Go over the syllabus
* Go over the "bird's-eye view" of the content we will be covering in this course
* Assign you into your course project teams
* Ensure everyone is set up to work with Git, GitHub and so on
* Make your first commit to your project repository
* Assign flipped-classroom presentations for Friday and next week

## Day 2 - October 19

**Presentations for today:**

* Git: Branching and Simple Merging
* Git: Merge Conflicts

Today we'll do some in-class exercises on branching and dealing with merge conflicts. You'll deliberately introduce a merge conflict within your team, and then you'll address and fix it. Bring your laptops!

### Discussion: Branching and Merging

A *branch* is a diverging series of commits starting at a specific point in the Git history. By making a branch, you can work independently - you can make commits and push them to your own branch without affecting anyone else's work.

However, eventually you'll probably want to have your changes incorporated into the "main" branch. To do this, you use the *merge* operation - this operation causes Git to compute the steps necessary to integrate your code with whatever the current state of the target branch is:

* If nothing else has happened on the target branch since you made your branch, your changes are just tacked onto the history of the target branch and it is "fast-forwarded" to match your branch.
* If other things have happened on the target branch, but it is possible to *automatically merge* your branch with it, a "merge commit" will be created which represents the changes that your branch brings to the current state of the target branch. 
  * Automatic merge is generally possible if your changes and the target branch's changes are in different files, or if it is possible to logically determine which parts of a single file have been changed by which person. 
* If both you and the target branch have changed the *same* part of a file and an automatic merge cannot be done, a *merge conflict* occurs. This will be covered in the next presentation, but the general process is that you must manually decide what the "correct" state of the target branch is and manually create a merge commit to finish the merge.

When working locally on your own machine, you can pretty much merge any branch with any branch with no restrictions. But most of the time you will be working with a remote Git repository. Git repositories hosted on source code control services (such as Github) typically employ *policies* to control how merges are done to specific branches - this is especially important for branches that represent production code or "source of truth"  code. 

For example, a project might have a `release` branch. At any given time, this branch always represents the current production, live version of the code - it's the code that has been deployed to the application server, compiled and uploaded to the App Store, etc. (It's also a prime target for continuous deployment - something we'll be looking at later in this course.) Naturally, you want to protect what gets pushed to this branch. Therefore, it is very common for such a branch to have a policy in place that requires a *pull request* (sometimes called a *merge request*) in order to merge into the branch. Depending on the platform, the rules for completing the pull request can be complex - for example, a release branch may be configured such that any merge into release must be approved by at least two approved reviewers, must pass all automated tests, must demonstrate a certain threshold of code coverage in its tests, and must not create a merge conflict.

We'll be looking a lot more into branch policies in our later discussions, but the key for this week is that remote Git servers can and do implement policies that protect pushing and merging to certain branches.

### Discussion: Merge Conflicts
