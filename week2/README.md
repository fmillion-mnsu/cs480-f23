# Week 2: Source Control II

Let's dive into some advanced Git techniques! Remember for your presentations - this guide gives you the essential information, but you should spend time doing some of your own research to come up with additional details for your presentation!

## Presentations:

### Tuesday:
* [Advanced Git Merging](#discussion-advanced-git-merging)
* [Git History](#discussion-git-history-viewing)

### Thursday:
* [Git Stashing and Worktrees](#discussion-stashing-and-worktrees)
* [Bisecting and Blaming](#discussion-bisecting-and-blaming)
* [Rebasing (and other ways to rewrite history)](#discussion-rebasing-and-other-ways-to-rewrite-history)

## Discussion: Advanced Git Merging

Git merging is a complex topic! For example, how does Git know when a merge conflict has occurred? How does it deal with, say, a big insertion in a file that pushes a bunch of other unchanged content down to later lines? What about more complex merges with more divergent histories? What if, for some reason, you need to do a merge but you really don't care what the other branch contains and just want your code as-is to be the result of the merge?

Git offers a variety of *merge strategies* (algorithms) that let you control to varying degrees what actually happens during the merge process. We know that the purpose of a Git merge is to take two branches of code that have different histories and *merge* them together into a new single source tree. So far we've seen the default behavior, which is known as the `ort` strategy. 

This strategy, which is an optimized and slightly modified version of the classic strategy known as `recursive`, first looks at the history of both branches to try to determine the point of divergence. Basically, it looks through the history to see what which point both branches were at the same commit - this is almost always the point at which you and/or the other branch used the `git checkout -b` command to make a new branch. Then it moves forward through each of the two branches and calculates the differences using a differencing, or `diff`, algorithm. (If you're a Linux/UNIX user and you've ever used the `diff` command, that's basically what Git uses to determine differences in the two branches.) This is therefore known as a 3-way merge - the three sources are the state at the point of divergence/branching and the current state of both of the two branches.

However, there are other merge strategies available, which use different algorithms and methods to determine what the final state of the merge should be. You can specify which strategy to use by using the `-s` command line option - for example `-s ours`.

### Merge strategies 

Here's a very short description of each of the merge strategies:

* `recursive`: The original strategy from which the current default `ort` was derived. `ort` is basically a faster, more optimized version of `recursive`, which Git claims can be up to 25x faster on large source trees (think Linux kernel sources).
* `resolve`: A much simpler strategy which cannot detect more subtle changes such as renamed files. Typically not used anymore.
* `octopus`: A strategy that can handle more than two branches during a merge. However, it will not create merge conflicts and will instead refuse to proceed if merge conflicts are detected. (This is because the merge conflict syntax only allows for two branches to be involved in the conflict.) This is often used to bring together several branches in one shot which are all known to have worked on completely different areas of the codebase - i.e. where you know for sure no merge conflicts will occur.
* `subtree`: A version of the `ort` strategy which can also detect whether you have moved an entire directory into a subdirectory. For example, if you're developing a Python package and you moved all of the code into a subdirectory and you are merging a branch which had not yet done the move, `subtree` will detect this and process it as a proper merge, rather than simply viewing all of your files as new and all of the incoming files as deleted.
* `ours`: The simplest strategy. It just ignores everything from the incoming branch, and makes your current branch state (the `HEAD`) the result of the merge. You might see this used in the context of protected branches requiring pull requests, but you deliberately need to ignore the branch that is in the pull request for some reason.

### Merge strategy options

Many of the merge strategies offer some options to alter their behavior as well. For example:

* `ort` (and its relatives `recursive` and `subtree`) offer a handful of options (`ignore-space-change`, `ignore-all-space`, `ignore-space-at-eol`, `ignore-cr-at-eol`) which let you control how the merge algorithm handles changes in whitespace alone. Based on which of these options you choose, you can cause Git to ignore changes that involve *only* whitespace. For example, if you add a space to the end of a line, and the incoming branch has a completely different line, this will not result in a merge conflict - instead, your change (of just whitespace) will be ignored and the incoming change will be considered the correct one for the merge. *Be careful when using these if you're working with Python code, since indentation, which is considered whitespace, is significant in Python!*
* `ort` supports an *option* called `ours` - this is distinct from the `ours` merge strategy, in that it basically auto-resolves all merge *conflicts* by choosing the change in your branch (not the incoming one). You could think of it as creating a merge conflict and then going through and always accepting the "current" change. This is distinct from the `ours` strategy because it will still accept incoming changes that *don't* cause merge conflicts, whereas the `ours` strategy will simply ignore *all* incoming changes.
* `ort` also supports the `theirs` option, which is basically the opposite of `ours` - it will assume all *incoming* changes are the accepted ones.
* The older `recursive` strategy allows the `diff-algorithm` option, which changes the actual algorithm used by `diff`. The `ort` strategy always uses the `histogram` diff algorithm.
* Both `ort` and `recursive` can be instructed not to check for renamed files. The option's name was changed for some reason - in `recursive,` it's `no-renames`, while with `ort` it's `find-renames`. 

To specify options for Git merge strategies, you use the `-X` command line option - for example `-s ort -X theirs`.

### Other merge options 

There's a few other options you can use to alter the behavior of Git merges. Here's a few of them that you may find useful:

#### `--no-ff`: Do not create a "fast forward" merge.

Let's suppose you have a `main` branch. You make a `feature1` branch and make some changes there. Finally, you want to merge the changes back onto `main`. However, in the meantime, no other changes have happened on `main`.

At this point, Git can simply do what is known as a *fast-forward merge*. This means that, instead of actually creating a merge commit, Git simply moves the pointer on the `main` branch to point to the last commit you made on your branch. No merging happens at all.

There are a couple reasons you may not want this to happen, however. For example, suppose you are working on a project that has a requirement that merge commits be auditable. A fast-forward merge does not create such a merge commit, and thus could interfere with auditing requirements. Not allowing fast-forward merges ensures that a merge commit is created.

#### `--ff-only`: Only merge if a fast forward is possible.

This is the opposite of `--no-ff`. It will perform *only* a fast-forward merge. You likely won't use this too often in larger collaborative projects, but in a personal project it might be useful. It's also potentially useful to test whether a merge conflict would occur or not, since `--no-ff` will simply abort a merge if a conflict would occur. However, it's not a reliable indicator, since `--no-ff` will fail even if a successful auto-merge is possible.

#### `--squash`: Collapse all commits on your branch to a *single* commit on the target branch

This one can be useful in certain cases. What it does is to make a *single* merge commit without also bringing forward all of the commits that make up the merge. In other words, if you branched, made 10 commits and are now merging, only a single commit with *all* of your changes will be made on the target branch. (Typically all of your commits are pulled forward, with the merge commit being the one that resolves the divergences that occurred in the interim.)

Note that merge conflicts can still occur in this scenario, and will need to be resolved as usual. However, you can combine `--squash` with `-s ours` to generate a commit that basically looks like you took your working tree, copied it somewhere, checked out the target branch and copied the files back over, overwriting everything. Use with caution!

#### Others

There's a few other lesser-used options that are available for merging:

* `--no-commit`: Perform the merge, but don't auto-create the merge commit even if there are no conflicts. In this case, you still need to do a `git add -A`/`git commit...`, just like you do after a merge conflict; you simply won't have any conflicts if none occur.
* `-e`/`--edit`: Invoke the editor (maybe `vim`...) to edit the message on the merge commit. Alternatively, you can use `-m` to specify the message on the command line, much like you can with `git commit`.
* `--abort`: One of a few ways to back out of a merge that created conflicts (or one that you started with `--no-commit`). One other way is simply to use `git reset --hard`, which removes any and all changes that have occurred since the last commit on your branch.

There's also a bunch of general options that can apply to many other Git commands that we won't cover here.

*You may include information on third party tooling for merges as well, just make sure you include some background on these options and what options, if any, a third party tool supports.*

## Discussion: Git History Viewing

The magic command for this discussion: `git log`. 

If you're working on a project alone, you will likely be familiar with the history of your commits, since you'll have been the one who made most of them. However, what happens on large projects with tens or even hundreds or *thousands* of developers? (Git scales up pretty well!) There's often a lot of reasons you need to view the commit history and even figure out when a certain change occurred in a file (and who to `blame`...!)

The `git log` command offers a whole host of ways to view the history of commits in your Git repository. We'll go over only a few of the most important ones - but do check out the rest of the options and make some notes on them!

* When run with no options, `git log` prints out the history of commits on your current branch going back to the very beginning of the project. (Remember that every branch - even a feature branch or a hotfix branch - has a path leading all the way back to the very first commit, consisting of `diff`s between commits along the way!) The history includes the date and time, commit hash, author and message of each commit.
* `-n`: Limit how many commits back you see. `n 5` shows only the last five commits.
* `--oneline`: Display a compact single-line view of commits - an abbreviated hash and the first line of the commit message only.
  * Linux/UNIX nerds: pipe this into `nl` to get a count of the commits...
* `--merges`: Show only commits that occurred as part of a marge. (Alternatively, `--no-merges` to do the inverse.)
* `--graph`: Shows a visualization of the history tree. This is a command-line version of the visualizations commonly offered on Git sites such as GitHub or BitBucket.
* You can specify a branch to view history on that branch, or you can specify a commit by its (possibly abbreviated) ID to see only that commit's details.
* `--since`/`--after`/`--until`/`--before`: Filter the commit history by date. You can specify the date in the format `YYYY-MM-DD`, e.g. `2023-10-20`.

Another command that's often used to view history and to inspect specific commits is `git diff`. If you're at all familiar with the `diff` command on Unix, the output of most `git diff` commands will be familiar to you. Basically, it's a set of changes consisting of the starting point in both files and lines beginning with either `-` or `+`, meaning that a line was removed or added from the source in the destination. Changed lines will be seen as a pair of `-` and `+` lines, with the `-` line showing the original content and the `+` line showing the new content.

To use the `git diff` command, you can either provide it with a single reference (and it will compare it against your current state) or you can provide two references, and it will compare those two reference points. References can be a number of things, such as a (possibly abbrviated) commit ID, a branch name, a tag name, `HEAD` (current state), or things like `HEAD~2` (two commits back from the current state).

`git diff` has s
One really cool trick to keep in your back pocket is how to use `git diff` to display the changes between two places in a compact summary view. You've probably seen this view when you pull in changes from a remote server:

    Updating 5524541..cff1e7a
    Fast-forward
     .gitignore                                 |   4 +-
     .vscode/settings.json                      |   7 ++
     README.md                                  |  40 ++++++-
     clean.sh => dev_scripts/clean.sh           |   0
     dev_scripts/main.sh                        |   2 +-
     ... many more files here ...
     41 files changed, 1044 insertions(+), 502 deletions(-)
     create mode 100644 .vscode/settings.json
     rename clean.sh => dev_scripts/clean.sh (100%)
     delete mode 100644 dev_scripts/test_old.py
     create mode 100644 postinstall/2.0.7_00_fix_missing_models.py
     create mode 100644 test/csv_test.py

This nice view shows you each file that has changed, how many lines overall changed, and any other wider changes such as renames, deletes, creates and so on.

Want to see this view for *any* two commits?

    git diff --stat --summary 5524541..cff1e7a

In this example we're viewing the difference between two commits with the given IDs.

*In your presentation, you can also cover other tools for visualizing Git history if you choose.* 

## Discussion: Stashing and Worktrees

Ugh. We've all done it, right? You're coding along, and then *that guy* Slacks you and says there's a big problem on the `main` branch that needs a hotfix *right now* (because, you know, the company is losing $500K per second, and you really would like to keep getting paid, wouldn't you?) So you quickly try to checkout `main` to see what's up, and...

    error: Your local changes to the following files would be overwritten by merge:
    app/plugins/replace-HR-with-chatgpt.js
    Please, commit your changes or stash them before you can merge.

*"But I don't wanna commit!"* you whine. You're not at a good "stopping point"! What to do?

`git stash` to the rescue. `git stash` is a way to... well... *stash* away some changes locally so you can access them later, without ever pushing or committing anything to the mainline commit history. The simplest and most straightforward way to use `stash` is like this:

    git stash

Yep, it's that simple. Now, your working directory is the same as it was at the last commit. Now you can go checkout `main`, branch to `hotfix-because-that-guy-did-it-again-19`, fix *that guy's* changes (someone's really gotta talk to him about not writing code deliberately designed to fool our unit testing), and merge then back into `main`. All the while, your work sits in the stash, waiting for you.

*Finally*, production is good again, the money is flowing, paychecks are saved, and you can get back to... *assisting* the company with AI. You checkout your `feature-chatgpt-plugin` branch. Now what?

    git stash pop

That's it. Now you are right back where you started before *that guy* bothered you yet again!

*(Hint: You of course have to save all your files in your editor before you* `git stash` *- Git isn't* that *clairvoyant!)*

Only a few minutes later, it happens again. You get a message that branding has just pushed new assets to the repository and they'd like all developers to incorporate it into their current code. *Ugh.* Here we go again. Guess I have to save all my work, `git stash` it, checkout the `branding` branch, copy out the files I need to somewhere else, checkout my branch again, `git stash pop`... Or maybe I just need to clone the repo a second time in another directory... *(Gotta remember to bring up the idea of separate repositories at the next standup!)*

*Not so fast!* There's another way, and this one's even cooler than `stash`ing. 

    git worktree add ../app-branding branding

What this does is to make another copy of the repository that's linked to the current one, with the contents of the `branding` branch. Now you can simply go to the `app-branding` folder and grab the files you need! (The first argument after `add` is the path to the new copy of the files - in this case, `..` for parent directory, so `app-branding` will be at the same level as the original code filder.)

With this strategy, you don't have to do *anything* on your main working directory. Everything is just as you left it. This is *extremely* convenient when you need to hand-pick changes or files from another branch one at a time or in some complex fashion. It's also really cool for reviewing the contents of another branch, commit, etc. at any time without having to worry about unsaved/uncommitted changes. (A worktree would also been a workable solution to *that guy*, because you could checkout a worktree on the `main` branch and go to that folder to work on the hotfix!)

Why do this instead of just cloning again? When you clone, you create not just a worktree, but all of the Git structures themselves. It's *very easy* for these to get out of sync - even between your own two copies! (Imagine you `commit` on one copy of the repo and then go to the other copy and start working there accidentally...) A worktree is still linked to the same Git structures in the main folder, so there's now a single "source of truth" for the repository.

Once you're done with the worktree, you can ask Git to delete it for you by using `git worktree remove` - for example, `git worktree remove ../app-branding`. You could just delete the folder itself, but Git still thinks there's a second copy of the worktree there at this point. This will eventually get cleaned up, but it is also possible (but difficult, thankfully) to confuse Git into a state where it thinks the entire `branding` branch was cleared out of all files...

If you expect that the extra working directory might get moved or deleted but you want Git to remember its location (this is common if you're working on an external drive), you can use `git worktree lock` to force Git to assume the worktree is always going to be present at some point (even if it's temporarily missing). Otherwise Git will eventually automatically remove references to the extra worktree once it sees it missing for long enough.

Just like with any other command accepting references, you can also create a worktree based on a tag, a specific commit, or a relative commit like `HEAD~2`. 

Both of these commands have some extra options that you should take the time to explore!

## Bisecting and Blaming

A push to production! All looks well. Right? Seems OK. I'm gonna crawl into bed now! No calls to the 24 hour help line. No pings from the uptime monitor. No alarms in the logging... oh wait... uh oh... oh no... 

*\*buzz buzz\**

It all started with that one notification. "Error message found in server log." You tap the notification, thinking maybe it's no big deal. Maybe it's just something lagging. Maybe it's something out of our control...

    Traceback (most recent call last):
      File "api.py", line 65, in process_login
    ValueError: invalid value for 'uid'

You feel the blood pressure rising. That nice night of sleep is not to be. This is the third night you've been shortchanged on sleep. Eventually it's going to catch up with you. You already started nodding off during the day today...

Yawning, you get up and go to your computer and login. Time to start debugging. You look through the code that's on the `production` branch and find something strange. *How is that value being passed for `uid`?* You know for a fact this wasn't there in the last push to production a couple weeks ago. Since then there's been literally thousands of commits. How did this pass unit testing and QA? But more importantly, *when did this get introduced?*

Git has two functions that will help you point the finger: `bisect` and `blame`. Bisect lets you search through a huge commit history to find exactly where a change occurred, and Blame lets you identify exactly who made the commit that caused the change. Nifty, eh? Only thing you have to hope for now is that it wasn't somehow your own mistake, caused by another sleepless night last week...

### `git bisect`

> **A little detour: the binary search (bisection) algorithm**
>
> At some point you've probably heard this puzzle: In a phone book of 1,000 pages, assuming all entries are alphabetical, what is the maximum number of pages I would need to look at to find a specific name? (Pretend it's a history buff asking&ndash;someone who still actually uses those big thick bound stacks of paper.)
> 
> The solution to this puzzle is pretty neat. You open to the exact middle of the phone book first. Then you look to see if the name that you are looking for is alphabetically *before* or *after* the entries on that page. (You might get lucky and you might find the entry on that very page.) If the name you're looking for is *before* the current page, you throw out the second half of the book; if it's *after*, you throw out the first half.
> 
> Now you repeat the process. You turn to the middle page for the section you still have. Is the name before or after the entries on this page? Repeat the same process of ripping the book in half again and keeping only the half that might contain your entry. (If you find your entry on the page in question, you're done - "`break` out of the `for` loop.")
> 
> If you continue doing this, you'll find that after 10 iterations, you'll be left with one page. The entry you're looking for will be on that page! (Unless it's not in the phone book at all, in which case maybe your friend got rid of their landline phone.)
> 
> What's neat about this binary search algorithm is that it scales *logarithmically*. Consider the same exercise, but for a phone book containing 10,000 pages. (You'll only need a maximum of 14 iterations.) What about a phone book containing a listing for every single human being in the world, spanning well over 100 million pages? Assuming the book is alphabetical, you can still find a specific person's page in less than **27** iterations!

The `git bisect` command lets you use the binary search strategy to search through a Git history and find where a change occurred. Even on the *largest* repositories with *tens of thousands* of commits, this operation will only take you a couple of minutes (probably no more than 14 tries or so!) - and maybe even less, since you can even have `bisect` run a command for you to help you determine quickly if the change exists in the current commit!

The basic steps for a Git bisect operation are as follows:

1. Find a commit where the problem doesn't exist. In our scenario, perhaps it's the last production merge, or it's a `tag` for the previous published version. If you have no idea, you *could* go all the way back to the initial commit, but this will probably end up wasting more time - it's best if you can find a single commit that you know does not have the problem. *Make a note of this commit - its commit hash, its tag, etc.*
2. Switch to a commit that you know has the problem. This might be the current production commit, your current `HEAD`, etc.
3. Run `git bisect start` and `git bisect bad`. This tells `git` to start the bisect operation, and also that you're sitting on a commit with the problem.
4. Run `git bisect good <commit_id>` to tell `git` where you know the problem doesn't exist. 
5. Now run `git bisect` on its own. This will checkout a commit that's exactly in between the two commits you specified. Look to see if the problem exists.
    * If so, run `git bisect bad`.
    * If not, run `git bisect good`.
6. Keep repeating step 5 until you run out of commits to test. This will take "log-base-2 of n" iterations at most, with `n` being the number of commits in the range you initially specified. (There's that binary search algorithm at work - just like the phone book example!)
7. Eventually, once you run out of commits to try, you'll have landed on the commit where the problem initially surfaced! Now you can go on to figuring out who to blame (more on that in a moment).
8. You can make note of which commit you're on and then run `git bisect reset` to get back to where you started prior to starting the bisect operation.
    * You can optionally switch to a different commit, branch, etc. with `git bisect reset <commit_id>`

> **Hint:** You can use the terms `old` and `new` instead of `good` and `bad`. This might be useful if you're not looking for something that *broke*, but rather just looking for which commit introduced a change, a new feature, new strings, etc. You have to pick one set or the other - you can't suddenly start using `old` and `new` after starting a bisect using `good` and `bad`.
>
> You can type `git bisect terms` to know which terms are currently in use.

While you are bisecting, you have a few options:

* `git bisect log` will show you what you've done so far - which commits you've deemed good and bad. 
* `git bisect run <command> [args]` will let you run a command that automatically determines whether each commit is good or bad. 
  * If you're good at scripting, you can use this to *automate* the bisecting process. Your command needs to use shell return values to indicate `good` or `bad`: `0` means the commit is `good` (or `old`) and `1` means the commit is `bad` (or `new`). 
  * If you're crafty with command line tools like `grep` you can easily find which commit a change was introduced in. You could alternatively write a Python script, a Java program, whatever you feel most comfortable with, that tests the codebase for the change you're interested in.
  * Another common scenario is to run the command which compiles a program to determine at which commit the compilation broke. 
  * The program can return a special return value of `125` to indicate that this particular commit should be `skip`ped. Skipping commits may be useful if they are irrelevant to your test case.
* `git bisect skip` to ignore the current commit. You can also give a specific commit or a range of commits (separated by .. - e.g. `1234abc..9876fed`). Commits that are `skip`ped will not be included in the bisect operation - it's basically as if those pages are missing from the phonebook.

Ok, you've found the commit. You can use `git log` to view the commit history from your current position and you'll see who made the commit. It's *that guy* again! I swear, his manager is gonna get a call...

But wait! Just because the *commit* is authored by *that guy*, it doesn't necessarily mean he's the one who introduced the bug. For example, perhaps the commit was merging in some changes from another branch. Git does have a command that can show us when each individual line of a file was committed and who it was committed by, however - this works across merges (except squashed ones) and branches as well. 

### `git blame`

Just like a good insurance company, everything that happens in a Git repository is someone's fault. This command is luckily quite a bit simpler than `git bisect` because it's just one command. To use `git blame`, just checkout the commit where the change occurred, and then type `git blame <filename>`. You'll be greeted with a view like this:

    457c899653991 (Thomas Gleixner           2019-05-19 13:08:55 +0100   1) // SPDX-License-Identifier: GPL-2.0-only
    ^1da177e4c3f4 (Linus Torvalds            2005-04-16 15:20:36 -0700   2) /*
    ^1da177e4c3f4 (Linus Torvalds            2005-04-16 15:20:36 -0700   3)  *  linux/kernel/panic.c
    ^1da177e4c3f4 (Linus Torvalds            2005-04-16 15:20:36 -0700   4)  *
    ^1da177e4c3f4 (Linus Torvalds            2005-04-16 15:20:36 -0700   5)  *  Copyright (C) 1991, 1992  Linus Torvalds
    ^1da177e4c3f4 (Linus Torvalds            2005-04-16 15:20:36 -0700   6)  */
    ^1da177e4c3f4 (Linus Torvalds            2005-04-16 15:20:36 -0700   7)
    ^1da177e4c3f4 (Linus Torvalds            2005-04-16 15:20:36 -0700   8) /*
    ^1da177e4c3f4 (Linus Torvalds            2005-04-16 15:20:36 -0700   9)  * This function is used through-out the kernel (including mm and fs)
    ^1da177e4c3f4 (Linus Torvalds            2005-04-16 15:20:36 -0700  10)  * to indicate a major problem.
    ^1da177e4c3f4 (Linus Torvalds            2005-04-16 15:20:36 -0700  11)  */
    c95dbf27e2015 (Ingo Molnar               2009-03-13 11:14:06 +0100  12) #include <linux/debug_locks.h>
    b17b01533b719 (Ingo Molnar               2017-02-08 18:51:35 +0100  13) #include <linux/sched/debug.h>
    c95dbf27e2015 (Ingo Molnar               2009-03-13 11:14:06 +0100  14) #include <linux/interrupt.h>
    7d92bda271ddc (Douglas Anderson          2019-09-25 16:47:45 -0700  15) #include <linux/kgdb.h>
    456b565cc52fb (Simon Kagstrom            2009-10-16 14:09:18 +0200  16) #include <linux/kmsg_dump.h>
    c95dbf27e2015 (Ingo Molnar               2009-03-13 11:14:06 +0100  17) #include <linux/kallsyms.h>
    c95dbf27e2015 (Ingo Molnar               2009-03-13 11:14:06 +0100  18) #include <linux/notifier.h>
    c7c3f05e341a9 (Sergey Senozhatsky        2018-10-25 19:10:36 +0900  19) #include <linux/vt_kern.h>
    ^1da177e4c3f4 (Linus Torvalds            2005-04-16 15:20:36 -0700  20) #include <linux/module.h>
    c95dbf27e2015 (Ingo Molnar               2009-03-13 11:14:06 +0100  21) #include <linux/random.h>
    de7edd31457b6 (Steven Rostedt (Red Hat)  2013-06-14 16:21:43 -0400  22) #include <linux/ftrace.h>
    ^1da177e4c3f4 (Linus Torvalds            2005-04-16 15:20:36 -0700  23) #include <linux/reboot.h>
    c95dbf27e2015 (Ingo Molnar               2009-03-13 11:14:06 +0100  24) #include <linux/delay.h>
    c95dbf27e2015 (Ingo Molnar               2009-03-13 11:14:06 +0100  25) #include <linux/kexec.h>
    f39650de687e3 (Andy Shevchenko           2021-06-30 18:54:59 -0700  26) #include <linux/panic_notifier.h>

Look at that - every single line in the file is listed along with *who last committed a change to it* (such as changing or adding it)! *Now* we can catch that rascal who insisted on being careless (or maybe malicious...?) by inserting that bad value into our source code!

You can alter the output of `git blame` in a couple of ways by using these command line switches:

* `--show-stats` shows some statistics at the end of the output. The most interesting one is `num commits`, which tells you how many commits have contributed to this file's current state.
* `-M` and `-C` tells `git blame` to also look for lines that were *moved* or *copied* (but not *changed*) within the file (`-M`) or within other files in the same commit (`-C`). `blame` will show these changes normally but you'll only see half of the change. This will make sure you see both ends of the change.
* `-e` to include the email field from the commit
* `-s` to *not* include the author name field from the commit.
* And many others - look at the documentation for `git-blame` for details.

You'll be doing a fun activity in class where you'll find out just *which* one of your scoundrel classmates broke the code!

## Discussion: Rebasing (and other ways to rewrite history)

Rebasing may seem a bit confusing at first. So before we get into rebasing itself, let's recall a fundamental aspect of how Git works.

If you're a UNIX/Linux user, you may have seen the `diff` command before. The `diff` command lets you identify the differences in two text files on a line-by-line basis. It's more than just detecting which lines have changed though, it's also detecting if lines have *moved* or been *deleted*. The output of `diff` is intended to be compact but yet unambiguous enough that it can be used to cause the same changes to occur at a later time. In other words, if you `diff file1.txt file2.txt`, the output is sufficient that you could later convert any copy of `file1.txt` into `file2.txt`, but the size of the output is only a fraction of either file (depending on how much similarity there is).

Git uses `diff` internally to determine what changes have occurred in your repository when you make a commit. Generating the *worktree* for a specific commit is basically a task of taking an initial point in time (typically the initial commit) and *replaying* all of the `diff`s between that initial commit and the commit you are requesting. (It's a bit more complex than that but the internals aren't super important, but if you're curious you can view the source code for Git itself in (naturally) a Git repository at <https://github.com/git/git>.)

A Git commit history is basically a type of linked list. Each commit contains the commit it is based on, the `diff` of the two commits, and the ID of the new commit. It's possible to walk backwards through the list by following the pointer to the previous commit. (Branching complicates this somewhat but you can follow the path all the way back to the initial commit from just about any commit.)

The key here is that each commit simply stores the `diff` between its previous commit. There's nothing special about a specific `diff` output, however. This is where rebasing come in. Put in one sentence, rebasing **regenerates the `diff`s between a given commit and your current state** (`HEAD`). It is one of a couple of ways you can "rewrite history" with Git.

> It's important to interject something here. On your own local machine you can rewrite history with impunity. You could download the Linux source tree and change the very first commit to contain your name instead of Linus Torvalds' name as the author. You could then rebase the entire Linux source tree, regenerating each and every commit along the way, and now you have a repo that makes it look like *you* wrote the Linux kernel. However, you will very quickly find that you're not able to actually *push* that change back upstream to the Linux source code repository on Github.
>
> Pushing rebased histories often requires the permission of *force pushing*, which means you are telling the remote Git server to ignore any existing commits that you are overwriting. You are unlikely to have this permission as a simple contributor to a project. You may have permission to force push if you are a core maintainer of a project or, naturally, if it's your own project.
>
> To see the effect in action, you can create your own Github repository and clone it on your machine. Then, commit something and push it. Now, make another change and use the command `git commit --amend`. This command rewrites the most recent commit with a new `diff` - basically a one-commit rebase. You could think of it as "undo the last commit and redo it with the current state of the worktree." This will succeed without incident on your own local machine. However, you may find you are unable to actually push this change to your repository. It is common for many source code hosting systems to restrict force pushes by default, requiring you to explicitly allow them. Therefore, some of these techniques are useful for your local machine or as a project maintainer, but may not be useful in all cases.
>
> Because of this, it's generally advised not to rebase commits you have already pushed to a remote. In practice this isn't always possible, since you probably are in the habit of `git add -A; git commit -m 'stuff'; git push`. (You might even have an alias or a keyboard shortcut for it...) And at the very least, you probably push your commits to remote at the end of every day or when you're finished working for a while. So it's possible you will need to rebase commits you pushed to a server. The silver lining is that in some systems you *can* force-push to branches *you* created - just not to others' or protected branches. This is something you'll have to try out yourself and possibly work with your administrator to enable.

So, why would you rebase? Imagine this scenario: You made a branch off from the `main` branch to work on a feature. However, it took you a bit longer to get your work done than you originally planned. (Try going to bed on time.) You got your feature working, but since then, a whole bunch of stuff has happened on `main`, most importantly some critical security fixes.

If you were to merge your new feature into `main`, you are likely to get merge conflicts, because the `main` branch has many files that have changed since you checked out your branch. You *could* have frequently merged `main` into your branch, but you forgot. You could merge `main` right now, but that will create a bit of a disjointed history path.

This is one case where `rebase` comes in. In this sense, `rebase` literally means to *base* your branch from a new starting commit. Git will generate entirely new commits starting from the rebase point - this isn't *changing* commits, it's making *new* ones - new commit IDs and all.

Rebasing lets *you* handle the possibility of conflicts rather than making it something that someone higher up the chain has to deal with. When you finally do merge your new feature into `main`, it can be accomplished with a simple fast-forward, or at worst a much simpler merge. This results in a much more linear, clean history path. This has many benefits, among which are much easier use of `git bisect` in the future - complex branching histories can confuse `bisect` and remove some of the benefits and speed with which you bisect.

There's two ways you can execute a `rebase`: standard and interactive:

* Standard rebasing is automatic. Git starts from the branch you are rebasing from and then uses the same differencing algorithm to try to apply your new changes all the way up the chain back to your current state. Just like with merging, you can get merge conflicts from doing this - you resolve them in the same way, but rather than *committing* after resolving the merge, you do `git rebase --continue` to tell Git that you've fixed the conflict and it is OK to proceed. Depending on how many changes there are between the initial commit you branched from and the one you are rebasing to, you may not get any conflicts, or you may get many.
* Interactive rebasing allows you to have very precise control over the new commits. You can selectively apply or not apply the commits, change the commit messages, squash commits (i.e. combine two or more commits' changes into one commit) and execute arbitrary commands during the rebase process. An interactive rebase opens your text editor and shows you a list of every commit that will be re-generated - this is where you can specify what you want to happen for each commit, and you can also insert lines to, for example, execute commands at certain points. 

To start a Git rebase, you simply run `git rebase commit_id` with `commit_id` being a reference to where you want to rebase from. This can be a commit hash, a branch's current pointer (by branch name) or a tag. So for example, if you branched off of a tag called `v1.0` and now you want to rebase to `v1.1`, you could simply run `git rebase v1.1`.

> To do an interactive rebase, you add the `--interactive` option to the command, e.g. `git rebase --interactive v1.1`.

### Other ways to rewrite history

Oh man, wouldn't it be great if we could erase bad things from history? While in the real world it's probably not a good idea to remove our knowledge of many historical events from the record, in Git there are sometimes good reasons to actually rewrite history. 

> Just as with rebasing, this can be challenging if you're working with remotes. You need force push permission to do history rewrites that you intend to push to a remote. It's *very* common for, at a minimum, a `release` or `production` branch to forbid force pushes. However, in depending on your circumstances, you might have to talk with your sysadmin or whoever runs your Git repo remotes if you need to be able to push (unless you're the one who runs the remote repo, in which case get into the configuration and security settings and have at it.)

#### Amending the most recent commit

We mentioned the `git commit --amend` command. This is the simplest way to modify history - it simply means "whatever the last commit is? Replace that with the current state, recalculating the diff with the previous commit." If you have not yet pushed your latest commit to the remote, you can even do this without worrying about having force-push permissions, since you haven't yet uploaded the commit anyway. A perfect use case? You realize after committing that you forgot to save a file or two in your editor. Rather than making another commit with a message like "I forgot to save", you can simply incorporate those changes into the commit you already made.

You also can change the commit message if you wish - even if you haven't made changes. 

Rebasing is the next level up the chain - it's the way you can change a file going back multiple commits, not just one. As we said, amending a commit is more or less like rebasing to the last commit.

#### Squashing

This is only loosely considered "modifying history", because it's commonly done for cleanliness in the repository. Squashing means to take multiple commits and fold them into a single commit. One place where this might be done is in a merge - you can specify the `--squash` option while merging, which will create *only* the merge commit with *all* of the aggregate changes, rather than bringing forward all of the commits on your branch that are integrated into the target branch.

Sometimes this is done for a production branch to keep its history clean. The production branch then only has single commits for, say, given release versions or major features. The downside to doing this is it breaks the link between the feature branches and the main branch. You can partially resolve this by merging the release branch back into your own branch after you squash merge, which will more or less create an empty merge commit which brings the two histories back in "sync". 

#### That ugly big file

*That guy* did it again. (Seriously, is anyone going to talk to the supervisor?) You're onboarding a new developer and they clone the repository only to see that it's going to need to download multiple *gigabytes* of data. "Why?" asks the newbie. "Isn't this just a simple webapp?" *Yes*, you murmur, *but* that guy *accidentally committed a whole ton of our video assets to the repository months ago, and even though we deleted them they're still in the git history.*

The "nuclear option" for rewriting history is known as `filter-branch`. It's a pretty powerful&ndash;and *dangerous*&ndash;tool that can remove those big files from your repository... or permamently screw it up. If you don't have your repository on a remote, you will definitely want to make a full backup prior to messing with `filter-branch`!

If you know the exact filename, you can blast away the file from your current history tree like this:

    git filter-branch --index-filter 'git rm --cached --ignore-unmatch filename' HEAD

(replace `filename` with the path to the file in the repository to delete)

The `filter-branch` command is complex and we won't spend a whole lot more time on it here, but you should read the documentation on the command if you want to learn more about its options. You can do a lot more than remove files - for example, you could run a script that converts all `<CR><LF>` line endings (DOS/Windows) to `<LF>` line endings (Linux) and run it throughout every commit in the repository. You could write any arbitrary script and give it to `filter-branch` and Git will run that script for *each commit*, generating new commits along the way. It's sort of like `rebase` on steroids. You can even obliterate commits made by specific users (maybe *that guy* needs this treatment!).

There's also one final option that you'll hear about for the use case of removing some big huge file from a repository: BFG Repo-Cleaner. It's a third-party Java application that can very quickly scan through a Git repository and remove references to a given file or files. Since the typical case for deleting a big blob is a binary file, such as an image, audio file or video, BFG Repo-Cleaner can skip regenerating the entire commit history and simply look for commits that reference the binary file and remove the file from their references. 

> Git handles non-text files a bit more crudely. Rather than doing `diff`s and storing the results, it simply stores "this binary file got added/was changed/was removed". These tags can be easily detected and removed by third party tools like BFG.

As in the case of `rebase`, you need force-push access to actually affect these changes on a remote repo. Maybe make *that guy* send the E-mail to upper management and IT requesting force-push access. It was his screw-up after all...
