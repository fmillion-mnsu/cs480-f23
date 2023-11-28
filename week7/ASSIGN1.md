# Assignment for Tuesday, November 28th

We're going to start to bring all the pieces together! Today you will do an **individual** project in which you will write a GitHub Actions file to build a Docker container from a repository. Your GitHub Actions file will contain steps to both build and run the container and produce its output.

## Steps

1. *Create a new repository* in the CS480 organization called `week7-<yourname>`.

    > **Important**: Your repository must be **private** or else the runner will not work!

2. Clone the empty repository to your computer.
3. Create a Dockerfile that will print out a message:

        FROM ubuntu:latest

        RUN echo Hello, I'm <PUT_YOUR_NAME_HERE> and this is an automatically generated Docker container. > /message.txt
        RUN echo This container's build time is: `date` >> /message.txt

        CMD cat /message.txt

    This Dockerfile is pretty simple - during the *build time* it prints a message to a file within the container image, and at *run time* it just prints out the message.

    You can (and should!) try building and running the Dockerfile locally to make sure it works and that you didn't make any typos:

        docker build -t week7 .
        docker run --rm week7
    
4. Commit and push your Dockerfile to your repository.
5. Now, it's time to add a GitHub Action file. Action files are located at the path `.github/workflows` within your repository, so create that path on your system. 
6. We'll now put together our GitHub Action file piece by piece. Let's start with the easy stuff:

        name: Build and run Docker image

        on:
          push:
            branches: [ "main" ]

    The `name` is obvious - this will show up in the Actions view on GitHub.

    Recall that the `on` section defines when the action should run. In this case, we're telling GitHub Actions to run the action every time a push occurs on the `main` branch. 

7. Now, let's create our job:

        jobs:

          build-and-run:

            runs-on: ["self-hosted","docker"]

            steps:

    We're going to write steps next, but let's stop here.

    Recall that the `jobs` section can contain multiple job entries. Jobs typically execute in *parallel*, but you can define conditions to, for example, ensure that something is finished before the next step runs. A common example would be to wait for a production build to finish before attempting to deploy it. We'll do this during the final quiz assignment.

    The `build-and-run` is just an ID for the job. 

    The `runs-on` section tells GitHub what **labels** the runner needs to have in order to run the pipeline. In this case we provide two label requirements:

    * `self-hosted`: We want this job to run on *our* runner, not a GitHub hosted one. This is because using our own runner is free, whereas using the GitHub runners has limits and other limitations.
    * `docker`: The runner in our organization has been configured to allow use of Docker. However, since this is not a standard feature available on all runners, I set up a **label** to indicate that the runner is capable of running Docker. (Recall that labels are arbitrary and we can use them any way we like.) Although we have only one runner, this label ensures that should we have more runners that may not be able to run Docker, those runners won't be selected to run the job.

    > Recall that this is how we can implement the classic CI/CD pattern - we can have runners *labeled* as deployment runners.

8. Let's write the steps for our job. The steps are pretty straightforward:

        steps:

        - uses: actions/checkout@v3

        - name: Build the Docker image
          run: docker build -t <YOURNAME_LOWERCASE_NO_SPACES>_week7 .
        - name: Run the Docker image
          run: docker run --rm <YOURNAME_LOWERCASE_NO_SPACES>_week7

    Note that you need to replace that `YOURNAME...` thing with your name. For example: `flint_week7`. 

    Remember that the `uses` block tells GitHub Actions to pull the code from that repository and run it within the context of the job. `actions/checkout` is a very common action that you will probably use more than any other action - because its job is to get your code from GitHub onto the runner!

    Also notice that these commands are very much like the ones we ran while we were playing with Docker. All we're doing here is having the GitHub runner run the commands for us.

9.  Double-check your action file. Remember that **indentation matters!**
10. Save your action file as something like `docker.yml` in the `.github/workflows` directory.
11. Commit and push the change.
12. Now, go visit your repository on GitHub and visit the Actions tab. If all went well you should see your Action running (or maybe it's already finished) and be able to view the results!

# Part 2

Now we're going to introduce a variable. 

As we discussed there's a few ways to add a variable. One way is to include it in the job definition:

    env:
      SOME_VARIABLE: value

Another way is to add the variable and its value to the GitHub environment file on the fly. If you are familiar with Bash scripting this line should make sense to you, but if not, what it's doing is adding a line to the file whose name is in the variable `$GITHUB_ENV`. That line contains the name of the variable and the value we want to give it. 

    - name: Set environment variable
      run: echo "SOME_VARIABLE=value" >> $GITHUB_ENV

## Steps

1. Edit your Dockerfile to print out all environment variables in your message. Add these lines before the `CMD` line:

        ARG SOME_VARIABLE
        RUN echo "Environment variables: " >> /message.txt
        RUN env >> /message.txt

    Change `SOME_VARIABLE` to a variable name of your choice. Variable names should be capitalized, must not contain spaces, and cannot start with a number.

1. Change your Docker Build command as follows: `docker build -t <YOURNAME_LOWERCASE_NO_SPACES>_week7 --build-arg "SOME_VARIABLE=$SOME_VARIABLE" .`

    Remember to change `SOME_VARIABLE` to your variable name.
 
1. **Copy** your GitHub action to a new file - for example, `docker-step2.yml` - and do two things: change the job name, and add the environment variable you added above. The value does not matter, since that `env` command will print out *all* variables. You can use the `env:` section - no need to use the scripting method for setting a variable.

    You can simply open your action, do a Save As and give it a new filename in the same directory, and then make the edits. 

    > Hint: You *would* use the scripting method if you wanted to set a variable at runtime during your job that you could not ascertain ahead of time. 

1. Commit your new action and wait for your GitHub action to run.
1. Go to your Actions view and see your action running!

# Submission

No D2L submission - I'll see your repository in the GitHub organization!

> *I can see jobs that have run on the runner, even from your own repositories - so I'll be able to tell if you don't actually run your own jobs on your own repositories!!!*
>
> This is another reason why you might use a self-hosted runner - it allows your organization to do code and job auditing and maintenance. :)

