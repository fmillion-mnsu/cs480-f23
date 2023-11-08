# Week 4: DevOps

## **Assignments:**

* [Tuesday: In-class GitHub Actions Getting Started assignment](ASSIGN1.md) - **Individual project, one submission per student!**

## Presentations:

### Tuesday

* [What Is DevOps? (High level overview!)](#discussion-what-is-devops)
* [DevOps and Source Control](#discussion-devops-and-source-control)

### Thursday

* [GitHub Actions - Getting Started](#discussion-github-actions)
* [GitHub Actions - Actions and Runners](#discussion-github-actions-actions-and-runners)

## Discussion: What is DevOps?

DevOps stands for "Developers + IT Operations". 

Traditionally, developers and IT operated independently. Developers (understandably) need deeper access to their systems and data than "other users", while IT operations is typically responsible for maintaining an organization's systems integrity and "operations". IT is also usually tasked with things such as deploying new computer systems, running updates, maintaining servers and so on. How can we mesh these two things?

DevOps, as a broad term, refers to the integration of IT operations procedures into the software development lifecycle. It's a wide field of study, encompassing many areas such as project management, cybersecurity and network infrastructure. Most of the content in this course will focus on two aspects of DevOps: *automation* (continuous integration and continuous deployment) and *infrastructure* (infrastructure-as-code, containerization, platform-as-a-service...).

### A typical scenario

You have just finished developing your great web application. You've done (at least some) testing, and you've made sure your database scripts are ready to go. What's next? 

You probably thought about documentation. By this, we mean you documented what someone else needs to do in order to get the application running. In your documentation, you'll specify things such as:

* what Web framework you used
* what programming language and runtime your backend needs
* what libraries you need to install
* what database engine needs to be available
* how to run the script to populate the database
* and so on.

As a developer who has full administrative access to your own computer, you've been able to figure all of this out on your own. You've been able to test various scenarios and determine the best combination of software to make your application sing. But here's the rub: do you believe that the IT department at any organization of significant size is going to let *you*, the lowly *developer*, get root or administrative access to the production servers to do all of this? **Most likely not!**

So instead, you have to depend on someone from the IT department to do all of this for you. A person in IT, who's been working hard all week and is excited to finally get home for the weekend to spend some time with his family, gets that dreaded E-mail from you. "Management wants the web app deployed before end of business today, can we get this pushed up right now?" The IT person frowns, mutters some inappropriate profanity and gets to work trying to interpret and understand your developer documentation. *New virtual environment, check... install Flask, check... ok, new public folder on the Apache server, check... new database in the MySQL instance, check...* The IT person's phone rings. It's the family. "Where are you? Dinner's getting cold and we've been waiting so we don't start without you!" Frantically trying to hurry up, our overworked and underpaid IT person finally gets the application deployed, logs out, and jumps in the car to head home. 

But then comes the dreaded *emergency alert* tone... Something isn't working. Management is very unhappy because the new rollout is a disaster. Parts of the application aren't working. Unusual errors are being thrown. Our poor IT person is now sitting at the dinner table with the kids and family and a laptop, alternating between diagnosing the issue and eating a bite of cold, stiff food. In the meantime, the developer is long gone for the weekend as well and isn't reachable. This is *not* going to be a good weekend...

So, what went wrong here? The main issue is, of course, the fact that our IT worker had to *manually* install and deploy this Web application. For a personal project or for a very small test program, this strategy may work. However, as soon as we are working with a mission-critical system, it is a much better idea to *automate* the deployment process to whatever degree we can, so that there is little room for human error. 

> Naturally, human error can come up in many other places, such as in the automation scripts themselves. However, even that can be somewhat alleviated by using testing and staging environments. It's even better once you start looking at *containerization*, which is a topic we'll get into in a couple of weeks.

At the company-wide meeting on Monday, the developers and the IT staff are all sitting in a room, enduring the berating from management, finance, sales and public relations. "If this isn't fixed, it could tank our company," says public relations. "The public is upset and if we don't fix it, they'll look to other solutions!" Finance is showing us a report detailing how much money we lost over the weekend. Sales is quite upset about all of the calls over the weekend asking why the product that was pitched so hard is failing at the most basic tasks.

**The IT manager stands up and suggests that the organization implement DevOps.**

After a short pitch, everyone is convinced. Not only will employing a proper DevOps strategy help prevent these kinds of issues, but it will also bring some organization to parts of the project that have always been a bit haphazard. It's on. DevOps is coming to town!

### What does DevOps let us do?

DevOps starts at project management. While we aren't going to cover project management strategies in this course, it's important to understand the link between project management and DevOps. As just one example, if a project uses an Agile management strategy with weekly sprints, IT can plan for at least one major deployment to production per week (with others possibly occurring for major security issues or serious bugs). 

We then allow software to take over the role of the actual deployment of the code. No IT person needs to login to the web server under the `root` account and manually unzip the code and run database migration scripts by hand. All of this will now be handled automatically, with the actual steps and procedures being documented in *code*. There's a security advantage here as well - the less often someone needs to login to a production server as an administrative user, the less likely it is that a security compromise infiltrates that server.

Finally, we'll be implementing a bug tracking system. Bugs reported by users will be automatically entered into the system as tickets, which will be directly associated with tasks for developers. Bugs will be triaged and scored based on severity, and fixing of bugs will then be able to be prioritized as well. IT's job is now focused back on maintaining the actual server operating systems, networking and hardware maintenance.

Traditional DevOps can go beyond this flow as well - for example, there still may be a need for an IT administrator to install a particular programming framework or runtime library on a server. Such requests might be handled with a traditional ticketing system. 

In the next section we'll talk more about how DevOps connects with source control such as Git.

## Discussion: DevOps and Source Control

For this section of our course, the main aspect of DevOps that we're going to look at is CI/CD, which stands for Continuous Integration and Continuous Deployment. At a high level, you can think of CI as "automated builds" and CD as "automated upload to production". 

### Continuous Integration

CI is concerned with taking source code in a source code repository and producing *artifacts*. An artifact is anything that results from processing the source code. The most common kinds of artifacts are:

* a compiled version of a program written in a compiled language like Java or C#. The compiled version may also be a self-executing installer program.
* a minified, deployment-ready build of a Web application
* a packaged version of an application or library, following the language's standards for packaging (such as Python package wheels)
* a formatted and prepared copy of documentation, in a ready-to-view file format such as PDF or HTML
* a Docker container image

Secondary artifacts may also produced by the CI process, such as: 

* logs of the build process
* results from unit and integration test runs
* debugging symbol files for debug versions of compiled programs
* error messages if the build process failed

The CI process involves running a *script* which specifies how to produce the build artifacts. The script can contain any actions that you want, but most commonly you will include instructions to clone the source code repository, compile or build the code, and collect the output of that process as an artifact. 

> You can get pretty creative with CI. While the most well-known use case is for building software, CI can be applied to almost any process that you want to occur automatically. For example, I have used CI to do the following:
>
> * Take a piece of music in a digital audio workstation and render the complete song to an MP3 file
> * Take some source Markdown files and produce an EPUB-format electronic book
> * Transform a source code repository in some way (such as extracting out certain parts of the code)
> * Download and update a local archive of content, based on a source code repository that contains a list of URLs to copy
>
> The limits of CI are really those of your imagination (and the computing resources you have available)! 

Just as important as how to build the source code is *when* to build it. CI systems can be configured in a variety of different ways. Typical strategies include:

* Building the code when a *push* to a certain branch or list of branches occurs
* Automatically building the code every night at the same time. 
  * You may have seen some applications that offer "nightly" builds - this is how that's done.
* Building the code when certain events occur within the source code management system, such as an issue being resolved or a merge/pull request being accepted
* Manually triggering a build

CI scripts are typically comprised of multiple steps or *workflows*, and different scenarios may call for different sets of workflows to be executed. For example, a CI system might be configured so that a push to any development branch causes the unit and integration test suite to be run, and errors to be reported to the developer who authored the commit. A more strict configuration may forbid commits that do not pass unit tests, although this is uncommon except on production or testing branches as it is still a good idea for developers to push their code to the central repository even if it isn't ready for review or testing.

### Continuous Deployment

While at one time the CD was a means for distributing music and digital data, today the "CD" section of CI/CD is *continuous deployment*. Continuous deployment could be seen as a special case of continuous integration, and indeed many modern CI/CD systems treat deployment as just another CI workflow. What differentiates CD is that it often is the "last step" - the step that actually puts all of the work up to this point into production. Up until the CD step, anything can fail and nothing will change on production. However, when the CD workflow runs, that's it - the change is going up to production. Because of this, CD workflows often have *many* conditions that must be met before they are allowed to run.

Some CI/CD systems only allow conditional execution of CD workflows, not CI workflows. This is becoming less common in current systems such as Github Actions, where *any* CI workflow can be conditional. 

Another aspect of CD that can differentiate it from CI is that CD often needs much higher privileges. Building the software can be done without any special permissions in most cases. However, actually deploying the software requires privileges, and not just any privileges, *very special and powerful* privileges. So, CD workflows are given access to sensitive credentials, and it is common for CD workflows to run on a different system than CI workflows. It is also common that CD workflows must go through a rigorous approval stage, to try to prevent a rogue developer from inserting scripts or code into the workflow that might expose or compromise those precious secret credentials.

> In June 2023, Microsoft suffered a serious security breach. A master signing key for Azure was accessed and leaked. These master keys are *extremely powerful*, and a person having access to one can do many extremely dangerous things. A leaked signing key could lead to a hacker being able to obtain complete access to data systems, inject arbitrary code into production systems undetected, and even destroy systems and data. Signing keys are an example of the kinds of credentials that are often needed in CD pipelines, but their immense power requires immense responsibility, which is why developers are much less likely to be directly interacting with CD workflows.
>
> If you'd like to read more about the Microsoft breach, check out [this article](https://blog.gitguardian.com/protect-your-keys-lessons-from-the-azure-key-breach/).

### CI/CD's relationship with Git (and source control in general)

Some CI/CD systems require you to configure the workflows (also sometimes called "pipelines") within the source control management system itself. For example, Azure DevOps (the new name of Microsoft's "Team Foundation Server", a Git-based source control server) has a service known as Azure Pipelines which lets you configure CI/CD pipelines. The *pipelines* are configured by a system administrator as part of the source control system's interface. Each step in the pipeline consists of an *action*, which is simply an instruction to do something: run a script, copy a file, mark a file as an artifact, set an environment variable, and so on. Azure DevOps treats CD pipelines independently from CI pipelines, and CD pipelines focus on operations specifically designated for the task, such as uploading files to FTP servers and restarting server programs.

While you are likely to still see CI/CD systems like this, the more common strategy today is to include the pipeline or workflow configuration *directly in the repository itself*. The configuration is stored as one or more files that are committed directly to the source code repository.

Why is this useful? This disconnects the interdependence between the source code itself and the CI/CD system. If a major change needs to be made to the source code, such as renaming a directory, the developer can also make the change to the CI workflow itself, rather than having to separately ask an IT manager to update the pipeline in the CI/CD system.

Even more importantly, it allows CI/CD to fall under the same review and approval process as source code itself. Nobody is going to sneak in a rogue script or CI action without a reviewer seeing it, and even if it passes review, every developer can view the change! This reduces the chance that a rogue CI/CD administrator changes the pipelines without anyone knowing!

Additionally, modern CI/CD systems differentiate CI and CD to a lesser degree; the "ultimate" convergence is where the system does not differentiate the two at all, and a deployment task is simply another workflow. The one component that stays out of the code (and *should!*) is the secrets themselves. However, the actual deployment steps can be listed in the source tree, with any sensitive information like credentials, server addresses and so on masked out. The CI/CD system will replace the placeholders with the actual values when the workflow is run.

## Discussion: GitHub Actions

There are many CI/CD systems out there. To name a few:

* **[GitHub Actions](https://github.com/features/actions)** - the star of our show!
* [GitLab CI/CD](https://docs.gitlab.com/ee/ci/)
* [Gitea Actions](https://docs.gitea.com/usage/actions/overview) (mostly compatible with GitHub Actions)
* [Azure DevOps](https://azure.microsoft.com/en-us/products/devops/pipelines) (Azure Pipelines)
* [Travis CI](https://travis-ci.com)
* [CircleCI](https://circleci.com)
* [Jenkins CI](https://jenkins.io)
* And [many more...](https://www.lambdatest.com/blog/best-ci-cd-tools/)

So with all these choices, why are we using GitHub Actions and not some other tool? A few reasons:

* GitHub Actions is easy to configure, because it's built into GitHub. You don't need to connect an outside service to your repository and share Git credentials with some external program in order to get CI/CD workflows working.
* The configuration itself is stored in the [YAML](https://yaml.org/) language, which is a neat way to store structured data objects in easily-editable plain text. It's similar to XML but is much easier to work with directly in a text editor like Visual Studio Code.
* It's widely used. It's not the most *popular* tool (that title goes to Jenkins), but GitHub as a platform is used by [over 80%](https://www.6sense.com/tech/source-code-management/github-market-share) of organizations and users for source code management. The skills you learn with GitHub Actions will easily translate to other CI/CD systems.

So, how do we use GitHub Actions?

### YAML - the configuration file format

If you haven't seen YAML files before, don't worry - they're very straightforward and easy to read. YAML files consist of typical data structures - strings, numbers, lists, dictionaries (key-value pairs) and so on, expressed in plain text. It serves a purpose similar to JSON but is arguably a "better" format for a few reasons, not the least of which is that it allows comments!

Some believe YAML stands for "Yet Another Markup Language", but [the official YAML website](https://yaml.org) says it stands for "YAML Ain't Markup Language" - another use of the "recursive acronym" trend that's popular among open source developers. (GNU = GNU is Not Unix, LAME = LAME Ain't an MP3 Encoder...)

While a YAML file technically can consist of a simple *scalar* value (such as a string or an integer), this isn't very useful. YAML becomes useful when you use it to store common data structures like lists and dictionaries.

Here is a sample of a YAML file:

    name: GitHub Actions Demo
    run-name: ${{ github.actor }} is testing out GitHub Actions
    on: [push]
    jobs:
    Test-GitHub-Actions:
        runs-on: ubuntu-latest
        steps:
        - run: echo "Hello GitHub Actions running on a ${{ runner.os }} server!"

Firstly, much like Python, *indentation is significant in YAML.* The indentation level controls what contains what. 

If you take a look at the lines that are all the way justified left, you'll notice all of them start with a string followed by a colon. This is because this YAML file represents a *dictionary* (a key-value store)! Each line that is not indented contains both a key and a value separated by a colon. 

Take a look at the `on:` value. It contains square brackets - and they work very much like they do in Python. The square brackets define a list. This list contains a single item, `push`. 

Finally, the last item, labeled `Test-GitHub-Actions`, contains a complex substructure. At 4 spaces in, we have another key-value set - this means that the `Test-GitHub-Actions` key actually contains as its value another key-value store, or in other words, another dictionary.

Finally, the `steps` value within this subdirectory contains a list. The list contains one item, which contains... a key-value store.

Confused yet? This file as presented may seem a bit daunting because there's a *lot* of nesting going on. It might help if we represent exactly this same file as a Python dictionary:

    yaml_contents = {
        "name": "GitHub Actions Demo",
        "run-name": "${{ github.actor }} is testing out GitHub Actions",
        "on": [ "push", ],
        "Test-Github-Actions": {
            "runs-on": "ubuntu-latest",
            "steps": [
                {
                    "run": "echo \"Hello GitHub Actions running on a ${{ runner.os }} server!\""
                }
            ]
        }
    }

Now you have a better picture of what's happening. What makes this a bit confusing is that the indentation level is not "consistent" - i.e. it's not always 4 spaces. The very last line, with a list item, is technically starting a new indent level, 2 spaces after the 4-space indent level. For example, suppose we needed to add more values to that sub-sub-dictionary:

    Test-GitHub-Actions:
        runs-on: ubuntu-latest
        steps:
        - run: echo "Hello GitHub Actions running on a ${{ runner.os }} server!"
          environment: 
            - This is a sublist
            - This list is part of the "environment" key under "steps" under "Test-GitHub-Actions".
        - run: echo "This is another step."
        - run: echo "And this is yet another step."

One key point of YAML is that, much like Python lists, order is significant. The list of steps (which is a list of dictionaries) is processed in forward order by GitHub Actions. You don't have to specify step numbers - if you need to add a step in the middle of the steps, you simply put that step in the correct location. This makes editing YAML files (and in turn GitHub workflows) pretty easy.

Don't worry if you don't fully grasp YAML yet. You'll get plenty of opportunities to practice - not just with GitHub Actions, but with Docker when we get to the Containerization unit!

### The simplest GitHub action

Guess what - you've already seen the simplest GitHub Actions workflow! That sample YAML file above is a functional GitHub Actions workflow. But how do you make it run?

The in-class assignment from Tuesday had you follow the Microsoft [GitHub Actions Quickstart](https://docs.github.com/en/actions/quickstart). One of the lines in the sample file specifies when to run the workflow: `on: [push]`. 

As configured, this means the workflow will run on *any* branch on which the file exists as written! So if you make a branch and commit that branch to GitHub, the pipeline will run again for that branch. 

This is fine, but what happens if you *don't* want the workflow running every single time someone pushes some new code? (Which is almost certainly the case!) You can adapt the `on` value to many different scenarios and very tightly control when the workflow automatically runs!

## Discussion: GitHub Actions - Actions and Runners

GitHub Actions can seem a bit magical from the outside. Just stick a configuration file in your repository and set it up the right way, and magically, things *happen*. Code gets built, tested and deployed all on its own. Naturally, there's a lot more code behind the scenes to help this run. In this section we'll talk about how GitHub Actions actually works.

### Actions

What does GitHub Actions *actually* do when you run your workflows? Where does the code come from that gets run? Believe it or not, the code itself is hosted...on GitHub!

Check out this step in the GitHub Actions demo:

        - name: Check out repository code
          uses: actions/checkout@v4

That `uses` value indicates where to find the code that should be run for this step of the workflow. Want to see the code? Try going to [[https://github.com/actions/checkout]]!

> The `@v4` means to checkout the Git item named `v4`. Just as with the `git checkout` command, this can be any Git identifier, including a branch name, a tag, or a commit ID. It's extremely common (and best practice, if you get into developing your own actions) to use tags to "lock in" certain versions of the action. This way, workflows can be reasonably guaranteed that their behavior won't suddenly change because you released a new version of an action.

Officially, GitHub Actions are written in TypeScript. This is not *strictly* necessary, but it's the standardized language for Actions since all of the OS system images used for running GitHub Actions workflows come with NodeJS installed. If you wanted to, say, write an action using Python or C#, you could absolutely do that, but you'd have to first run a different action (written in TypeScript) that installs the necessary runtime environment for your action. You can include this in your action's configuration - if you're curious, [here is an example](https://shipyard.build/blog/your-first-python-github-action/) of how to do it.

This course won't focus on actually *developing* GitHub actions, but now you know how GitHub Actions actually works. Every step, other than running a shell command, exists as a Git repository containing the code for that action. 

There's a few neat things here. The Actions are all open source, by design and by requirement of the architecture. And anyone can write their own action; just publish a public GitHub repository. Anyone who wants to use the action need only specify it. For example, if your GitHub username is `student` and you just wrote a cool GitHub action and pushed it into a repository called `do_things`, anyone who wants to can use your action by simply using `uses: student/do_things@v1` (or whatever version tag you've selected). This makes GitHub Actions infinitely extensible by anyone for anyone!

How can you discover what other actions are available? There's a small collection of them available under the GitHub user `actions` (which is where `actions` comes from in `actions/checkout@v4`). [This page](https://github.com/orgs/actions/repositories) lists all of the actions available (along with some supporting repositories which are not actually usable actions). For example, `setup-python` lets you install Python (optionally specifying an explicit version) to install into the build environment. There's also `upload-artifact` which lets you upload the *artifacts* your code has generated. The artifacts are then accessible in the GitHub Actions view, and can also be downloaded by later workflows using `download-artifact` (for example, to actually deploy a compiled program).

Of course, there are more actions than those available under the `actions` user ... *many, many* more. The [GitHub Actions Marketplace](https://github.com/marketplace?type=actions) is a public index that lists all publicly listed GitHub actions. There are actions that are designed by companies to allow you to directly interact with their platforms or services. For example, there's an action to upload a compiled Android application to the Google Play Store. There are even actions that will build iOS applications, provided you run the action on a Mac.

### Runners

Speaking of running the actions, where and how does that happen?

Actions are run using a program called a **runner**. A runner is a piece of software you can install on any Windows, Mac or Linux server. Runners connect to Github and use a *pull* strategy to poll GitHub to see if any actions are waiting to be run. If so, the runner picks up any actions it's able to run (more on that in a moment) and schedules them to run. The runner is the thing that actually clones the action repositories, runs the code, uploads the artifacts, returns the results to GitHub, and so on.

Linux-based runners typically use the *Docker* containerization system. Docker is a topic we'll be covering in a couple of weeks, but for the purposes of this discussion, you just need to know that Docker is a system that lets you start up predictable, reproducible, preconfigured environments. Thus, a GitHub Actions runner can start each job with a known fresh environment. Unfortunately this doesn't work on Mac at all (because on Mac, Docker is just a Linux virtual machine) and on Windows certain special requirements must be met to use Docker for Windows to run Windows programs. Using Docker is not a *requirement* of using a GitHub runner, but it is a good idea if it's possible for you to do so. (For example, imagine if a job that ran before you installed Python 3.10, and you need Python 3.9. The `setup-python` action will likely be able to figure this situation out, but you can imagine it's not possible for code to account for *all possible* scenarios...)

GitHub offers free use of their runners for a limited time per month. If you're working on publicly-accessible repositories, you can use GitHub Actions for free. But if you are working on private repositories, GitHub lets you use Linux-based runners for 2,000 minutes of actions per month (rounded up to the next minute). Due to the licensing costs of Windows and the hardware requirements of Mac, you only get 1,000 minutes worth of Windows and 200 minutes worth of Mac execution time per month. This might sound like a lot of time, but imagine your program takes 10 minutes to build - you could easily burn through 20 builds in one month on a Mac instance... (You also get a limited amount of artifact storage for free - 500MB for free accounts. )

It's also possible to run your own runner easily. If you need lots of Mac build time but you also own a Mac, you can install a GitHub runner on it and it will pick up and run your GitHub workflows for you. (The same of course applies to Windows and Linux.) GitHub allows unlimited use of runners you host yourself regardless of project type.

During class we'll set up a runner (together, not an assignment) for our organization and then you'll change your GitHub action to use our own custom runner!
