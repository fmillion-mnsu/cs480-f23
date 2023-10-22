# Week 2: Source Control II

Let's dive into some advanced Git techniques! Remember for your presentations - this guide gives you the essential information, but you should spend time doing some of your own research to come up with additional details for your presentation!

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

## Discussion: Git Histories

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
