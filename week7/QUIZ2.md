# Quiz 2 - CS 480, Fall 2023, Block 2

**The quiz repository is [here](https://github.com/mnsu-cs480-f23/cs480-quiz2).** (https://github.com/mnsu-cs480-f23/cs480-quiz2)

This project will serve as your final of two *quizzes* for the course. This is a take-home quiz that you may do on your own time. **You are to work INDIVIDUALLY on the quiz - you may NOT work with others or in your groups!** You may **NOT** ask others in class for help on the quiz. However, you **MAY** use Internet resources as reference as long as those Internet resources do not include "asking other members of the class via the Internet" or "copying and pasting stuff as-is"! You **MAY** also use code or notes you have written for this course.

This quiz is due **on Friday of finals week** (December 8th) **at 11:59 PM**. Points will be deducted for late quiz completion. **If your quiz is not submitted by Sunday, December 10th at 11:59 PM you will receive ZERO POINTS for the quiz!**

**You may E-mail me if you are having functional or technical issues with the quiz activities. I will NOT answer questions specifically related to the knowledge you need for the quiz but I am happy to help you if you are experiencing unexpected or unusual errors that are preventing you from proceeding. Please E-mail me at flint.million@mnsu.edu if you need help.** ***Do not wait until the last minute!***

## Conventions

There are many code samples given in the quiz to help you along. Unless otherwise specified, *any parameters inside of angle brackets* (greater than and less than signs, `< >`) *should be replaced by you with an appropriate substitution.* For example, you should replace `<some-string>` with a string of your choosing. Sometimes the substitution will give you an indicator as to what it is, such as `<some-container-name>`. If you see the *same* placeholder in multiple commands or lines of code, you should use the same value everywhere that placeholder occurs.

For the most part, strings should consist of only alphanumeric (A-Z) characters, numbers and hyphens. Sometimes, other requirements will be noted where the placeholder is used.

Some steps in this quiz instruct you to replace a part of a configuration file, string, or command with your name in lowercase. 

When you see this, **include BOTH of your names with no spaces**. This is very important! For example, if you used only your first name, and someone else in the course also shares your first name, *their work would overwrite your work!*  We are sharing one server and one organization so we must all ensure that we use unique name strings.

For example, I would use `flintmillion` when the instructions call for `<yourname_lowercased>`

## Some things to remember:

* Whenever you commit something to your Git repository, **use informative comments**!. 
* Include **comments** in your code files! 
  * Both `Dockerfile`s and YAML support comments by preceeding them with a "hashtag" (`#`)

    YAML file:

        yaml:
          group: # this is a comment
            # this is also a comment
            - item
            - item

    Dockerfile:

        FROM ubuntu # use the latest Ubuntu image
        # Install a package
        RUN apt -y update && apt -y install apache2

    All of these are valid comments.
* Test, test, test! There is no limit as to how many times you can push code changes to try soemthing else if things are not working. (Try to use a somewhat informative commit message in this case!)

## Steps

1. **Fork** the repository `cs480-quiz2` into the *organization* (**not** to your personal GitHub) and name it `cs480-quiz2-<firstname>-<lastname>` (replacing `firstname` and `lastname` with your name in lowercase). Make your repository **private**. This repository contains a REST API very similar to what you would have done for Quiz 1. Place the repository within the CS480 course organization and make sure it is marked private (so nobody else can cheat off of you!).

    Please note that the code in this repository may require Python 3.10 or later. You may have issues if you are using Python 3.9. If you run into this, you can either install Python 3.10, or if you use  `anaconda` you can create an environment containing Python 3.10 for the project.

2. Clone your fork to your machine, setup the appropriate Python virtual environment and install the requirements.

    > **Reminder:**
    >
    > To create a Python virtual environment: `python -m virtualenv venv` followed by the appropriate activate script:
    >
    > Windows cmd.exe: `venv\Scripts\activate.bat`
    > PowerShell: `. .\venv\Scripts\activate.ps1`
    > Linux/Mac: `source venv/bin/activate`
    >
    > You should have `virtualenv` installed from the API project, but if not: `python -m pip install virtualenv`
    >
    > To install the requirements, after activating the virtual environment (you should see `(venv)` on your command prompt): `pip install -r requirements.txt`

3. Create a **branch** titled `quiz2`.

4. Change the HTML code in the method `def get_main()` to display your name on the page.

    This will help you make sure you're accessing *your* site rather than a different one.

    You can also change the entire HTML page if you are so inclined, but that's not a requirement.

    Commit your change to your `quiz2` branch.

5. Run the API to make sure it works. Visit <http://localhost:3000/> once the API is running to test it. Also visit your Docs page at <http://localhost:3000/docs>. You don't need to actually *use* the API, just make sure the pages come up as expected.

    > The web app will tell you that it is listening on `http://0.0.0.0`. Do not use this as a link. `0.0.0.0` in a server address is Linux networking nomenclature that means "available on all IP addresses".

6. Create a **Dockerfile** to build the API.

    You'll use the following commands in your Dockerfile:

        FROM
        COPY
        RUN
        CMD
        EXPOSE

    You should not need to use any other commands. You may look up Dockerfile syntax on the Internet to help you with this part.

    > Hints and Notes:
    >
    > * The name of the file `Dockerfile` is **case sensitive**. You must name it `Dockerfile` with a capital D and no other capital letters. `DockerFile`, `dockerfile`, `DOCKERFile` etc. will all not work.
    > * Use a Python base image such as `python:3.10`.
    > * `COPY` the code into the Docker container to a directory of your choice - `/app` is a common choice. You can use a command like `COPY . /app/` to copy the entire source tree into the container. Note that the directory *must* include the trailing slash (`/`) or else Docker will complain.
    > * Use `WORKDIR` to change the working directory before you run Python commands - using `cd` to change directories won't be maintained between commands in the Dockerfile
    > * The repository has a `.dockerignore` file that will prevent your virtual environment from being copied into the container. You *do not* need to create a virtual environment inside the Docker container. The Docker container itself, in a sense, serves as the virtual environment - simply install your requirements file as a step in your Dockerfile. You may safely ignore any warnings about running `pip` as root.
    > * Remember to `EXPOSE` your port - leaving it at 3000 is fine.
    > * Remember to specify the actual command to run with `CMD`.

7. Make sure you have Docker running, and then try building your Dockerfile and running it locally. (If Docker is not running you'll receive an error relating to being unable to connect to the Docker daemon.)

    > Hint: 
    >
    > The Docker build command looks like this:
    >
    >    docker build -t <some-container-name> .
    >
    > and the command to try running the container looks like this:
    >
    >     docker run -p 3000:3000 --rm -d <some-container-name>
    >
    > Remember to use `-p` to expose the container's listening port on your host. If your container is listening on 3000, you can simply forward 3000 through using `-p 3000:3000`. Or, you can put it on some other port like `-p 5000:3000` which will cause your computer to listen on port 5000.
    >
    > Note that Mac computers have a builtin service that listens on port 5000, so you should probably use 3000 or similar. Also, port 6000 is [blocked by most browsers](https://dev.to/myogeshchavan97/comment/14p38) due to it being a port used by the X11 window system, so don't use port 6000 either.
    >
    > Also, remember you can use `--rm` to ensure the container will be removed when you exit it, so you won't get stuck with a bunch of stale containers.
    >
    > Use the `-it` flags to make sure your `Ctrl+C` command to exit the API will be passed through to the container.
    >
    > For example: `docker run -it --rm -p 3000:3000 mycontainer`

8.  Commit your Dockerfile to the `quiz2` branch.

9.  Now, create a GitHub Action to build your container. 

    Reminder: your GitHub Action file goes in `.github/workflows` and must be a YAML file ending with a `.yml` extension. You can name the YAML file anything you like.

    You should name your container using the tag `quiz2-<yourname_lowercased>`. Replace `<yourname_lowercased>` with your first and last name, lowercased, no space. 

    You **may** refer to Assignment 1 from Week 7 for info on how to do this, and you **may** look for example GitHub Action files on the Internet if you need help. 
    
    > **IMPORTANT**
    >
    > **Do not actually run the container in the action script - only build it!** If you do run the container, your action will never end and will eventually time out since this container is designed to run for a long time!

    A few hints:

    * Remember to use an `on:` section and specify that the workflow should run when code is pushed to your `quiz2` branch - don't use the `main` branch!
    * Use `runs-on: ["self-hosted","docker"]` to make sure the container will run on our class server and not on a public GitHub runner - it won't work there!
    * You must check out your code in the job. This doesn't happen automatically!
    * Use `run:` to specify a command to run for each step of the action. 

10. Push your GitHub Action to your branch. Visit GitHub to make sure your action ran **successfully.** Fix any errors before you proceed if your action fails.

    > Hint:
    >
    > Your container should build successfully. You should be able to view the docker build log in GitHub Actions.

11. Now, we're going to update our build action to also push the container to our private container **registry**. (Since GitHub charges for use of their container registry system, we have our own which is available at no cost to you - but you need to take a couple of different steps to connect to it. This is where a **secret** will come in!).

    In your GitHub action that builds your container, **change the name of the container** (hint: the `-t` option) to be the following:

        cr.cs480.campus-quest.com/quiz2-<yourname_lowercased>

    For example, I might use: `cr.cs480.campus-quest.com/quiz2-flintmillion`.

    `cr.cs480.campus-quest.com` is the address of a publicly available container registry for our course. We will use this in the final step of the course to **deploy** your container to a **live website!**

    After you make this change, run your GitHub action again (by pushing your change) to make sure everything still works.

12. Add a step to your GitHub action that will **login to the Docker container registry** with the password being provided using **GitHub Secrets**. The command is: `docker login cr.cs480.campus-quest.com -u <username> -p <password>`. Use this command as given in your `run:` step, changing only the items in angle brackets.

    > The credentials for the Docker server login are as follows:
    >
    > * Username: `quiz2`
    > * Password: `$ubmitURQu1z`

    **You need to provide the password as a GitHub Secret**. Providing the password directly in the GitHub action will reduce your score on this section considerably (and may not actually work very well...)

    > **Using Secrets**
    >
    > You can specify your secret values on GitHub's website using the **Security** section of the settings for your repository. You will find "Variables and Secrets". Select "Actions" under this section to add your secrets. Put your secret under the **Repository Secrets** section (ignore the Environment section).
    >
    > To use the secret in your GitHub workflow, specify it as another parameter in the step you need it in. For example:
    >
    >        - name: Use the secret
    >          run: echo "${MY_SECRET}" > secret.txt
    >          env:
    >            MY_SECRET: ${{ secrets.MY_SECRET }}
    >
    > Access to secrets is per-step in this situation. This helps to exercise the principle of least-privilege - you only have access to the secret during the specific command or operation in which you need it.
    >
    > Environment variable names (secret or otherwise) are generally in `ALL CAPS` and should use *underscores* (`_`) instead of dashes. Example: `THIS_IS_A_GOOD_VARIABLE_NAME`, but `this-is-not`.
    >
    > Place the username and the password in double quotes in your run statement. Also, when you use a variable name, start with a `$` and then place the variable name inside of *curly braces* - for example `${MY_SECRET}`. 
    >
    > Your login command should look like: `docker login cr.cs480.campus-quest.com -u "quiz2" -p "${<WHATEVER_YOU_CALLED_YOUR_PASSWORD_SECRET>}"`
    >
    > *Note:* Do not try using `echo` to provide the password to the standard input for the `docker login` command. Even though this may be regarded as a safer practice, it does not work properly within our GitHub Actions configuration. Additionally, GitHub Secrets is providing us roughly equivalent security by allowing us to exclude the password from the Git repository itself. 

13. Add another step to your GitHub action that executes the command `docker push cr.cs480.campus-quest.com/quiz2-<yourname_lowercased>`. This command uploads your container image (after it's been built) to the container registry server.

14. Commit your changes to your GitHub action and make sure that 1) the build still succeeds and that 2) **the push to the container registry succeeds** (no errors occur).

    > If you have Docker working locally, you can test to make *sure* the container uploaded properly by using these commands:
    >
    >     docker login cr.cs480.campus-quest.com
    >     (provide the username and password when requested)
    >
    >     docker pull cr.cs480.campus-quest.com/quiz2-<yourname_lowercased>
    >
    > If these commands succeed - i.e. you do not get any error messages, the container should have uploaded successfully and you are ready to proceed.

15. Create a `docker-compose.yml` file in your repository with the following contents, changing as appropriate:

        version: '3'

        services:
          <yourname_lowercased>:
            image: cr.cs480.campus-quest.com/quiz2-<yourname_lowercased>
            labels:
            - host=<yourname_lowercased>

        networks:
          default:
            name: cs480
            external: true

    > **Tech Details:**
    >
    > The server on which all of this is running is running a **reverse proxy** known as [Traefik](https://traefik.io). Among many other capabilities, this program monitors for containers running on the server, checks for which port is `EXPOSE`d, and automatically makes that service available via the Internet. 
    >
    > The `host` label is a configuration setting that the proxy can detect, which it uses to define what the URL of your site will be - the `.cs480.campus-quest.com` part is appended automatically.
    >
    > It is therefore important to use the `EXPOSE` statement in your Dockerfile - if things are not working, that is one thing to check!
    >
    > Reverse proxies are amazing pieces of software that can significantly reduce the amount of manual configuration you need to do - even when doing advanced deployment scenarios across multiple servers in multiple datacenters. If you are taking CS485 with me in the spring, you'll learn how to setup a reverse proxy yourself as part of a project!

16. Create a **new job** in your GitHub Actions workflow file. (i.e. do not add another step to your existing job, and do not create a new .yml file, but create a *new* job in the *same* file.)

    This job must specify that your first job needs to complete successfully before it can run. (Otherwise, the deployment will happen *while* your container builds, before it finishes - not very useful!)

    Here is what you need to enter for the job. You must modify this code to make use of your **secret** for Docker Login and change the `needs` value to the ID of your build job (among a few other changes noted). 

    > In a real-world setting, "the DevOps guy" would either give you this or help you write it, so I'm giving you most of what you need here.

        deploy-container:
          needs: <YOUR_BUILD_JOB_NAME_GOES_HERE>
          runs-on: ["self-hosted","docker"]
          name: "Deploy Docker container"
          steps:
            - name: Checkout
              uses: actions/checkout@v3
            - name: Login to Docker registry
              run: <COPY THE LOGIN STEP FROM YOUR BUILD JOB HERE>
            - name: Run the deployment action
              uses: mnsu-cs480-f23/deploy-container-action@v1
        
    > **Reminder:**
    >
    > The "id" for `needs` is the name of the key in the YAML file, not the value of the `name` parameter.
    >
    > For example:
    >
    >     jobs:
    >       myjob:
    >         name: A great job
    >         ...
    >       anotherjob:
    >         name: Another great job
    >         needs: myjob
    >         ...
    >
    > Note that the value of `needs` is `myjob`, and not "A great job".

    This job makes use of a **custom GitHub action**. Writing GitHub actions is easy and you can do it in almost any language. For this example, I used Python to write a simple script that deploys your website based on the Dockerfile. Feel free to visit the action repository at <https://github.com/mnsu-cs480-f23/deploy-container-action> if you want to see an example of how it's done.

17. Finally, push this change to the GitHub action file.

**If everything went as expected, you should now be able to visit `https://<yourname_lowercased>.cs480.campus-quest.com/docs` and see your web API.** Test this to make sure it works - and take a screenshot with your address bar visible! **Commit and push your screenshot to the `quiz2` branch.**

> **If it didn't work...**
>
> The first thing to do is **view the logs of your GitHub action runs**. Often simply reviewing these logs will give you the clue you need to fix things. This applies even if GitHub Actions did not report any error - the logs will still be generated and made available to you through the web interface.
>
> If everything seems to have worked (no errors reported by GitHub Actions and nothing seems wrong in the log output) but your website is not accessible at the expected URL, check your `docker-compose.yml` file - that's most likely where the problem is. Review the code given in the quiz and make sure your code is correct - **observing indentation!**
>
> If your container build is failing, check the `Dockerfile` for errors first!

You did it! You went through the whole process of containerizing, automating and actually deployng an application! *GREAT JOB!*

## Requirements

* **Use informative commit messages!** Messages like "fix" or "change" are insufficient. Do your best to come up with a reasonable description of the change you made for each commit!
* Your Docker container should build and deploy successfully and run properly in the final commit on your `quiz2` branch! Test thoroughly!

## Deliverable

Your final submission should be a *private Git repository that is part of the organization* which contains the following components:

* A `quiz2` branch with commits for:
  * a Dockerfile to build a container image that can run the API
  * a GitHub action that builds the Docker image (and nothing else)
  * a `docker-compose.yml` file based on the example in the quiz.
  * a GitHub action file (`.yml`) that both builds *and* deploys the Docker image as per the instructions. A secret key should be used for the deployment step - the password should *not* be present anywhere within your repository.
  * a screenshot of your API successfully running on the *public web server* as per the instructions (e.g. `https://yourname.cs480.campus-quest.com/docs`)

If you are confused or unsure of any requirements please feel free to E-mail me and I will clarify.

## Grading Rubric 

&nbsp; | 0 | 1 | 2 | 3 | 4 | 5 | Percentage of grade
-|---|---|---|---|---|---|-
**Dockerfile creation** | No Dockerfile written | Dockerfile created but does not build at all or is wholly incomplete | &nbsp; | Dockerfile exists and successfully builds but is either not on its own commit (separate from adding other files) or does not contain any comments | &nbsp; | Dockerfile created on its own commit, successfully builds and contains good comments explaining lines in the file | 15%
**GitHub Actions - Build Docker** | No attempt made to write GitHub Action to build Docker container | GitHub action created but contains errors and does not run at all | GitHub action created and runs successfully but does not properly build the Docker container | GitHub action created that successfully builds the Docker container but with missing elements (e.g. Dockerfile does not `EXPOSE` a port, improper tag given with `-t`...) | GitHub action created that successfully builds the Docker container with the correct tag name | GitHub action created that successfully builds the Docker container with the correct tag name and contains comments | 20%
**Docker Compose File** | No attempt made to introduce Docker Compose file | Docker Compose file created but wholly incomplete or with syntax errors | Docker Compose file created with no syntax errors but with missing elements (e.g. no `ports` section) | Functional Docker Compose file created but not on its own commit (i.e. added at the same time as another new file such as the Dockerfile) | Docker Compose file added that is fully functional and runs locally |Docker Compose file created that runs successfully and contains comments | 30%
**GitHub Actions - Deploy Docker** | No attempt made to write GitHub Action to build and deploy the Docker container | GitHub action(s) have syntax errors and/or do not work | Only a GitHub action for pushing the container image exists but no action for deploying the image **OR** *the secret password was included in the GitHub action file and not in a secret* | GitHub actions for pushing the container image and deploying it are provided | &nbsp; | GitHub actions for pushing the container image and deploying it are provided and contain comments | 20%
**Punctuality** | Quiz not submitted after Sunday December 10th at 11:59 PM | Quiz completed significantly late (more than 1 day) or in a way that cannot be graded by the instructor (you need to fix that ASAP if so!) | &nbsp; | Quiz completed late less than 1 day | Quiz completed late less than 12 hours | Quiz completed on time! | 15%

This quiz will be worth 7.5% of your total grade.

Good luck!
